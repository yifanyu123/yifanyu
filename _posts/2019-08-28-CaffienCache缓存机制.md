---
layout: post
title: 'CaffienCache缓存机制'
date: 2019-08-28
author: 小刺猬
color: '#6bc5d2'
cover: '../assets/cover/12.JPG'
tags: spring coding java cache
---
# CaffienCache缓存机制

前言:
今日在做项目优化时第一次引入Caffien Cache,该缓存机制能有效提升用户访问获取数据效率,我们可以将几乎不太变更的数据存入本地缓存，并定时更新缓存,在高并发场景将会有效提升系统性能

介绍:
> Caffeine is a high performance, near optimal caching library based on Java 8. For more details, see our user's guide and browse the API docs for the latest release.


[Github链接](https://github.com/ben-manes/caffeine)

## 一. 三种缓存机制
### 1. 手动加载
该例子的运行机制为
1. Spring容器启动,单例BseBusinessIndexManagerImpl Bean被创建
2. PostConstruct()方法被执行,initCache()方法创建第一个缓存,一个定时线程被启动,每隔60分钟initCache()方法都会被运行,我们从数据库中获取最新的多条数据,put方法会自动更新原来key值对应的数据,但是这也会造成一个问题,过期key对应的缓存数据将不会被删除
3. 关于get方法获取缓存数据,它的机制为每次先从缓存里查询,缓存中如果没有就调用降级方法去二级缓存或数据库中查找,并存入本地缓存
4. get 方法是以阻塞方式执行调用，即使多个线程同时请求该值也只会调用一次Function方法。这样可以避免与其他线程的写入竞争，这也是为什么使用 get 优于 getIfPresent 的原因

```java
@Slf4j
@Service
public class BseBusinessIndexManagerImpl implements BseBusinessIndexManager {

    private Cache<String, BseBusinessIndexPO> INDEX_CACHE = Caffeine.newBuilder()
            .maximumSize(2048)
            .recordStats()
            .build();

    @Autowired
    private BseBusinessIndexMapper businessIndexMapper;

    @PostConstruct
    public void init() {
        initCache();
        Executors.newSingleThreadScheduledExecutor(new ThreadFactoryBuilder()
                .setNameFormat("business-index-cache-updater").build())
                .scheduleWithFixedDelay(() -> initCache(), 60, 60, TimeUnit.MINUTES);
    }

    @Override
    public BseBusinessIndexPO getIndexById(Integer id) {
        return businessIndexMapper.selectByIndexId(id);
    }

    @Override
    public List<BseBusinessIndexPO> getValidIndexes(String tableName, boolean isRealTime,
            List<String> fieldNames) {
        return businessIndexMapper.getValidIndexes(tableName, isRealTime ? 1 : 0, fieldNames);
    }

    @Override
    public BseBusinessIndexPO getCachedIndex(String tableName, String fieldName) {
        return INDEX_CACHE.get("table.field:" + tableName + ":" + fieldName, k -> {
            String key = k.substring("table.field:".length());
            String t = key.split(":")[0];
            String f = key.split(":")[1];
            return businessIndexMapper.selectByTableAndField(t, f);
        });
    }

    private void initCache() {
        List<BseBusinessIndexPO> indexes = businessIndexMapper.selectAll();
        if (indexes.size() > 1001) {
            log.error("Business index count > 1000");
        }
        for (BseBusinessIndexPO index : indexes) {
            INDEX_CACHE.put("table.field:" + index.getTableName()
                    + ":" + index.getFieldName(), index);
            INDEX_CACHE.put("id:" + index.getId(), index);
        }
    }
}
```
### 2. 同步加载

```java
LoadingCache<String, Object> loadingCache = Caffeine.newBuilder()
        .maximumSize(10_000)
        .expireAfterWrite(10, TimeUnit.MINUTES)
        .build(key -> createExpensiveGraph(key));

String key = "name1";
// 采用同步方式去获取一个缓存和上面的手动方式是一个原理。在build Cache的时候会提供一个createExpensiveGraph函数。
// 查询并在缺失的情况下使用同步的方式来构建一个缓存
Object graph = loadingCache.get(key);

// 获取组key的值返回一个Map
List<String> keys = new ArrayList<>();
keys.add(key);
Map<String, Object> graphs = loadingCache.getAll(keys);
```

### 3. 异步加载
```java
AsyncLoadingCache<String, Object> asyncLoadingCache = Caffeine.newBuilder()
            .maximumSize(10_000)
            .expireAfterWrite(10, TimeUnit.MINUTES)
            // Either: Build with a synchronous computation that is wrapped as asynchronous
            .buildAsync(key -> createExpensiveGraph(key));
            // Or: Build with a asynchronous computation that returns a future
            // .buildAsync((key, executor) -> createExpensiveGraphAsync(key, executor));

 String key = "name1";

// 查询并在缺失的情况下使用异步的方式来构建缓存
CompletableFuture<Object> graph = asyncLoadingCache.get(key);
// 查询一组缓存并在缺失的情况下使用异步的方式来构建缓存
List<String> keys = new ArrayList<>();
keys.add(key);
CompletableFuture<Map<String, Object>> graphs = asyncLoadingCache.getAll(keys);
// 异步转同步
loadingCache = asyncLoadingCache.synchronous();
```

## 二. 值回收
### 1. 基于大小回收

```java
LoadingCache<String, DataObject> cache = Caffeine.newBuilder()
  .maximumSize(1)
  .build(k -> DataObject.get("Data for " + k));

assertEquals(0, cache.estimatedSize());
```

### 2. 基于时间回收
未完待续...
- 访问后到期

```java
LoadingCache<String, DataObject> cache = Caffeine.newBuilder()
  .expireAfterAccess(5, TimeUnit.MINUTES)
  .build(k -> DataObject.get("Data for " + k));

```
- 写入后到期

```java
cache = Caffeine.newBuilder()
  .expireAfterWrite(10, TimeUnit.SECONDS)
  .weakKeys()
  .weakValues()
  .build(k -> DataObject.get("Data for " + k));
```
- 自定义策略

## 三. 刷新

```java
Caffeine.newBuilder()
  .refreshAfterWrite(1, TimeUnit.MINUTES)
  .build(k -> DataObject.get("Data for " + k));
```

这里我们要明白 expireAfter 和 refreshAfter 之间的区别。当请求过期条目时，执行将发生阻塞，直到 build Function 计算出新值为止。

但是，如果条目可以刷新，则缓存将返回一个旧值，并异步重新加载该值

```
未完待续...关于Cache在SpringBoot上的集成
```
