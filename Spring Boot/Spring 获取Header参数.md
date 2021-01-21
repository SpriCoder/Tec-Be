Spring 获取Header参数
---

<!-- TOC -->

- [1. 方法一:对应的部分加入@RequestHeader获取](#1-方法一对应的部分加入requestheader获取)
- [2. 方法二:类级别注入HttpServletRequest](#2-方法二类级别注入httpservletrequest)
- [3. 参考](#3-参考)

<!-- /TOC -->

# 1. 方法一:对应的部分加入@RequestHeader获取
```java
@Controller
public class HelloController {
    @RequestMapping(value = "/hello")
    public String hello(@RequestHeader(value="User-Agent") String userAgent){
    }
}
```

# 2. 方法二:类级别注入HttpServletRequest
```java
@Controller
@RequestMapping("/hello")
public class HelloController {
     @Autowired
     private HttpServletRequest request;

     @RequestMapping(value="/printname/{name}", method=RequestMethod.GET)
     public String printName(@PathVariable String name,
              @RequestHeader HttpHeaders headers) {
          System.out.println("from request:" + request.getHeader("code"));
          System.out.println("from parameter:" + headers.getFirst("code"));
          return "hello";
     }
}
```

# 3. 参考
1. <a href = "https://www.cnblogs.com/yuwentims/articles/9721953.html">spring mvc获取header</a>