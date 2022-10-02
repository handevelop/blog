---
title:  "@Transactional 과 self-invocation"
excerpt: ""

categories:
  - java
  - spring
last_modified_at: 2022-09-26T21:06:00-05:00
---


# @Transactional

- Spring 에서 선언적 트랜잭션을 위해 사용하는, 여러 편리한 기능을 제공해주는 애노테이션이다.
- `begin`, `commit` 그리고 `rollback` 까지 개발자가 따로 보일러플레이트 코드를 구현할 필요없이 이 애노테이션만으로 자동 수행해준다.
- 선언적 기능으로는 트랜잭션 중에 다른 트랜잭션이 들어오면 어떻게 처리하는지에 대한 전파, 일관성 없는 데이터 허용 수준에 대한 격리, 읽기 전용 트랜잭션을 위한 리드온리, 타임아웃, 롤백for, no롤백for 가 있다.
![image](https://user-images.githubusercontent.com/25449640/193443930-566b155c-f519-49e0-869e-869dfe1997c9.png)
![image](https://user-images.githubusercontent.com/25449640/193443941-aa20b96a-b52e-4c27-9621-c343b658cc1d.png)



# 문제가 있는 @Transactional 사용법

```java
@Service
public class SomeServiceImpl implements SomeService{

    @Transactional
    @Override
    public void someMethod1() throws Exception{
        try{
            getSomeData(); // DB 조회
            insertSomeData(); // DB 삽입
            updateSomeData(); // DB 업데이트
        }catch (Exception e){
            updateFailLog(); // DB 업데이트
            throw new RuntimeException("someMethod1 fail.");
        }
    }
```

```java
public class BooksImpl implements Books {  
  public void addBooks(List<String> bookNames) {    
    bookNames.forEach(bookName -> this.addBook(bookName));  
    }    
  @Transactional  
  public void addBook(String bookName) {    
    Book book = new Book(bookName);    
    bookRepository.save(book); // JPA save 메서드는 @Transactional 애노테이션이 붙어있다.
    book.setFlag(true);  
  }
}
```
- 위 두 코드들은 문제가 있다. 살펴보자.
- 먼저 첫번째 코드를 보면 조회, 삽입, 수정 세개의 작업을 하나의 트랜잭션으로 묶어놓았다. 만약 예외가 발생해 실패했다면 모든 작업은 롤백이 될것이다. 그리고 catch 문에 의해 fail log 를 업데이트 하려고 하는 모습이다.
- 두번째 코드는 Spring JPA 를 사용하는 모습이다. 아무튼 트랜잭셔널 애노테이션으로 Book 객체의 flag 값을 true 로 설정하고 저장하는 작업을 하고 있다.
- 하지만 결과는 둘 다 제대로 저장되지 않는다.


# 프록시 객체를 사용해야 하는 @Transactional

- `@Transactional` 애노테이션은 스프링에 의해 관리가 되고 있다. 스프링 AOP 에 의해 프록시 객체가 생성되고 이 프록시 객체에 의해서만 `@Transactional` 이 동작한다.
- 하지만 위 코드들은 `$proxy.someMethod1() -> SomeServiceImpl.updateFailLog()`, `$proxy.addBooks() -> BooksImpl.addBook()` 이렇게 동작한다.
- 즉, 프록시로 감싸진 메서드가 아니라 객체 내부의 메서드를 호출하고 있기 때문에 트랜잭셔널 애노테이션이 수행되지 않는다
- 이를 `self-invocation` 이라고 한다.



# 해결 방법

- 가장 근본적인 해결 방법은 위처럼 `@Transactional` 붙은 객체에서 `@Transactional` 가 붙은 메서드를 내부적으로 사용하지 않는것이다.
- 그럼에도 사용해야겠다면 프록시 객체를 사용하겠다고 선언해주어야 한다.

```java
@Service
public class SomeServiceImpl implements SomeService{

    @Autowired
    SomeService self;

    @Transactional
    @Override
    public void someMethod1() throws Exception{
        try{
            getSomeData();
            insertSomeData();
            updateSomeData();
        }catch (Exception e){
            self.updateFailLog(); // DI한 빈 객체을 이용해 외부 메소드 사용
            throw new RuntimeException("someMethod1 fail.");
        }
    }
    // . . .
    @Override
    @Transactional(propagation = Propagation.REQUIRES_NEW,
            noRollbackFor = RuntimeException.class)
    public void updateFailLog(){
        // . . .
    }
```

```java
@Servicepublic class BooksImpl implements Books {  
  @Autowired  private Books self;    
  public void addBooks(List<String> bookNames) {    
    bookNames.forEach(bookName -> self.addBook(bookName)); // this 가 아닌 변수 self 로  
  }    
  @Transactional  
  public void addBook(String bookName) {    
    Book book = new Book(bookName);    
    bookRepository.save(book);    
    book.setFlag(true);  
  }
}
```


# 느낀점

- `@Transactional` AOP 로 구현되어 있고 프록시 패턴이 사용되고 있기에 this 를 사용해 순수한 자기 객체의 메서드를 사용하는게 아니라 프록시 객체의 메서드를 사용하게 해서 `@Transactional` 애노테이션을 동작하게 만들어야한다.
- ref.https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative-annotations
