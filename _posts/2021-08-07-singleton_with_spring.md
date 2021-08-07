---
title:  "스프링에서의 싱글톤 객체"
excerpt: ""

categories:
  - java
  - spring
last_modified_at: 2020-08-07T08:06:00-05:00
---


# 스프링 IoC 컨테이너
- IDE 에서 클래스 옆에 녹색콩으로 되어 있는 애들은 스프링 IoC 컨테이너 안에 빈 등록되어 있다.
- 이 녀석들은 사용하고자 하는 클래스에서 직접 의존성을 주입하지 않고 외부(스프링)에서 주입하도록 스프링에서 기능을 제공한다.
- 이러한 모습 때문에 `제어의 역전`(IoC) 라고 한다.


# IoC 컨테이너와 싱글톤 객체
- IoC 컨테이너로 등록된 빈들은 싱글톤 객체이다.
- 한번 등록 후에 어플리케이션 전체에서 그 인스턴스 하나를 가지고 재사용하여 처리한다.
- 스프링의 IoC컨테이너 덕분에 멀티 쓰레드 환경에서 쉽고 안전하게 사용 가능해졌다.


# 코드로 보는 IoC 컨테이너의 싱글톤 객체
```java
@Controller
class OwnerController {
  private final OwnerRepository owners;
  private final ApplicationContext context;

  public OwnerController(OwnerRepository o, ApplicationContext c) {
    this.owners = o;
    this.context = c;
  }
  
  @GetMapping("/bean")
  @ResponseBody
  public String bean() {
    return "ApplicationContext 에서 직접꺼낸 객체의 해쉬값 ": + context.getBean(OwnerRepository.class)
    + "알아서 주입 되어 IoC 컨테이너에 있는 객체의 해쉬값 " + this.owners;
  }
  // 결과 : ~~~.SimpleJpaRepository@30cfd10e 로 둘은 같은 인스턴스이다.
}
```