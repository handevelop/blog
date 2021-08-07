---
title:  "스프링 빈"
excerpt: ""

categories:
  - java
  - spring
last_modified_at: 2020-08-07T14:06:00-05:00
---


# 스프링 빈?
- 스프링 빈이란 `스프링 IoC 컨테이너가 관리하는 객체` 이다.
- 다시말해 ApplicationContext 가 만들어서 그 안에 담고있어, 알고있는 객체이다.

# 코드로 보는 빈
```java
public class OwnerControllerTests {
  @MockBean
  private OwnerRepository owners;

  @Autowired
  ApplicationContext context;

  public void getBean() {
    OwnerController notBean = new OwnerController(); // 빈이 아니다
    OwnerController bean = context.getBean(OwnerController.class); // 빈
  }
```


# 어떻게 빈 등록하지?
- `컴포넌트 스캔`과 직접 `일일히 XML 이나 자바 설정 파일에 등록`해서 빈 등록하는 방법이 있다.
- 컴포넌트 스캔은 `@Component` 메타 어노테이션이 붙은 클래스를 찾아 빈등록 하는 방법이다. 
- 또한 `@Controller`, `@Repository`, `@Service`, `@Configuration` 등의 어노테이션들도 속을 까보면 `@Component` 가 선언되어 있어 이 녀석들도 포함이 된다.
- 이러한 어노테이션이 붙은 클래스들을 IoC 컨테이너가 컴포넌트 스캔을 하여 빈 등록 된다.


# 라이프사이클 콜백
- 어노테이션 프로세서 중에 IoC 컨테이너가 사용하는 인터페이스들이 있는데 이를 라이프사이클 콜백이라 한다.
- 라이프사이클 콜백 중에는 컴포넌트라는 어노테이션을 찾아서 모든 클래스를 빈으로 등록하는 어노테이션 프로세서가 등록되어 있다.
- 어노테이션 프로세서는 `@Component` 어노테이션이 붙은 모든 클래스를 찾아서 그 클래스들의 인스턴스를 빈으로 등록하는 일을 한다.
- 어노테이션 프로세서는 어디에 있느냐하면 `@SpringBootApplication` 에는 `@ConponetScan` 어노테이션이 있다.
- `@SpringBootApplication` 는 어플리케이션 클래스에 선언되어 있다.
- 즉, 어플리케이션이 구동되면 `@SpringBootApplication` 하위의 패키지에서 모든 클래스를 찾아보며 컴포넌트들을 찾아 빈 등록을 하게 된다.


# 코드로 보는 직접 자바 설정 파일로 빈 등록
```java
@Configuration
public class SampleConfig {

  @Bean
  public SampleController sampleController() {
    return new SampleController();
  }
}

//@Controller 
//SampleConfig 빈 설정 파일로 인해 IoC 컨테이너 안에 빈 등록되었기 때문에 필요없다.
public SampleController {

}

public class SampleControllerTest {
  
  @Autowired
  ApllicationContext context;

  @Test
  public void testDI() {
    SampleController bean = context.getBean(SampleController.class);
    assertThat(bean).istNotNull();
  }
}
```