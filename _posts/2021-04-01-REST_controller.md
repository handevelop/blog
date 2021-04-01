---
title:  "REST Controller"
excerpt: "SpringBoot/JPA"

categories:
  - Java
tags:
  - Java
last_modified_at: 2021-04-01T00:00:00-05:00
---

SpringBoot/JPA 스터디 내용 정리1

# @Controller VS @RestController

## @Controller

- Spring MVC의 컨트롤러인 @Controller는 주로 View를 반환하기 위해 사용함. 
- 아래와 같은 과정을 통해 Spring MVC Container는 Client의 요청으로부터 View를 반환함.

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @Resource(name = "userService")
    private UserService userService;

    // @ResponseBody 어노테이션 활용
    @PostMapping(value = "/info")
    public @ResponseBody User info(@RequestBody User user){
        return userService.retrieveUserInfo(user);
    }
    
    @GetMapping(value = "/infoView")
    public String infoView(Model model, @RequestParam(value = "userName", required = true) String userName){
        User user = userService.retrieveUserInfo(userName);
        model.addAttribute("user", user);
        return "/user/userInfoView";
    }

}
```

  1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
  2. Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.
  3. Controller가 요청을 처리한 후에 응답을 DispatcherServlet으로 반환하고, DispatcherServlet은 View를 사용자에게 반환한다.

- 컨트롤러가 반환 할 때 ViewResolver가 사용되며, ViewResolver 설정에 맞게 View를 찾아 렌더링함.
- View 를 반환할 때도 있지만 Data 를 반환 하려면 ? @ResponseBody 어노테이션을 통해 json 으로 반환함.


## @RestController

- 보통은 위와 같이 @Controller 안에 @ResponseBody 를 사용하여 작성하지 않고 RestController를 따로 두어 개발함.
- @RestController는 Spring MVC Controller에 @ResponseBody가 추가된 것이라 생각하면 됨. 
- RestController의 주용도는 Json 형태로 객체 데이터를 반환함.

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @Resource(name = "userService")
    private UserService userService;

    @PostMapping(value = "/info1")
    public User info1(@RequestBody User user){
        return userService.retrieveUserInfo(user);
    }

    @PostMapping(value = "/info2")
    public ResponseEntity<User> info2(@RequestParam(value = "userName", required = true) String userName){
        User user = userService.retrieveUserInfo(userName);

        if(user == null){
            return ResponseEntity.noContent().build()
        }

        return ResponseEntity.ok(user)
    }

    @PostMapping(value = "/info3")
    public ResponseEntity<User> info3(@RequestParam(value = "userName", required = true) String userName){
        return Optional.ofNullable(userService.retrieveUserInfo(userName))
                .map(user -> ResponseEntity.ok(user))
                .orElse(ResponseEntity.noContent().build());
    }
}
```
