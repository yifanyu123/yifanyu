---
layout: post
title: 'spirng-boot-controller'
date: 2019-06-28
author: yifanyu
color: '#f9fce1'
cover: '../assets/cover/4.JPG'
tags: spring-boot coding
---
# spirng-boot Controller
## spring 中 Bean

http://www.voidcn.com/article/p-peghajxt-bpb.html
https://www.awaimai.com/2596.html
https://juejin.im/post/5b1b841de51d4506d47df108

## org.springframework.web.bind.annotation

### reference: https://www.baeldung.com/spring-mvc-annotations
- @Controller: used with @requestMapping

  ```java
  @Controller
  @RequestMapping("books")
  public class SimpleBookController {
      @GetMapping("/{id}", produces = "application/json")
      public @ResponseBody Book getBook(@PathVariable int id) {
          return findBookById(id);
      }
      private Book findBookById(int id) {
          // ...
      }
  }
  ```
- @RestController==@Controller@ResponseBody: render the result string directly back to the caller
- serialize the return objects into http response

  ```java
  @RestController
  @RequestMapping("books-rest")
  public class SimpleBookRestController {    
      @GetMapping("/{id}", produces = "application/json")
      public Book getBook(@PathVariable int id) {
          return findBookById(id);
      }
      private Book findBookById(int id) {
          // ...
      }
  }
  ```

- @RequestMapping: map specific http requests to specific method
  ```java
  @Controller
  class VehicleController {
  @RequestMapping(value = "/vehicles/home", method = RequestMethod.GET)
  String home() {
      return "home";
  }
  }
  ```
    - path
    - method
    - params
    - headers
    - consumes
    - produces(media type that can be produced)
	- @Getmapping
	- @Postmapping
	- @PutMapping
	- @DeleteMapping
	- @PatchMappinge
- @ResponseBody: 直接将返回的对象输出到客户端，如果是字符串，则直接返回；否则spring-boot默认使用jackson serialize 成 json字符串
- @RequestBody: Controller方法带有@RequestBody注解的参数，意味着request 内容是json, spring-boot默认使用jackson deserialize

    __maps the body of the Http request to an object automatically__
    ```java
    @PostMapping("/save")
    void saveVehicle(@RequestBody Vehicle vehicle) {
        // ...
    }
    ```




- @ModelAttribute
  ```java
  @Controller
  @ControllerAdvice
  public class EmployeeController {
      private Map<Long, Employee> employeeMap = new HashMap<>();
      @RequestMapping(value = "/addEmployee", method = RequestMethod.POST)
      public String submit(
        @ModelAttribute("employee") Employee employee,
        BindingResult result, ModelMap model) {
          if (result.hasErrors()) {
              return "error";
          }
          model.addAttribute("name", employee.getName());
          model.addAttribute("id", employee.getId());
          employeeMap.put(employee.getId(), employee);
          return "employeeView";
      }
      @ModelAttribute
      public void addAttributes(Model model) {
          model.addAttribute("msg", "Welcome to the Netherlands!");
      }
  }
  ```
- @EnableAutoConfiguration
- @RequestParam
  ```java
  @RequestMapping("/buy")
  Car buyCar(@RequestParam(defaultValue = "5") int seatCount) {
      // ...
  }
  ```
- @PathVariable
  ```java
  @RequestMapping("/{id}")
  Vehicle getVehicle(@PathVariable("id") long id) {
      // ...
  }
  ```
- @CookieValue
- @RequestHeader
- @ResponseBody:treats the return of the method as the response itself

## Other Annotations
- @Autowired
- reference:
    - https://www.jianshu.com/p/9062a92fbf9a
    - https://blog.csdn.net/HEYUTAO007/article/details/5981555
- @Data
