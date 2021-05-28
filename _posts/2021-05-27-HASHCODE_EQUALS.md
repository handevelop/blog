---
title:  "hashCode(), equals() 차이"
excerpt: ""

categories:
  - java
  - 스터디
last_modified_at: 2020-05-27T08:06:00-05:00
---


# Object 클래스의 equals() 과 == 

- == 는 프리미티브 타입에 사용 할 때 그 값이 같은지 물어본다.

- == 는 참조타입에 사용 할 때 그 참조변수의 주소값이 같은지 물어본다.

- equals() 또한 프리미티브 타입에 사용 할 때 그 값이 같은지 물어보고 객체의 주소값을 비교하여 같은지 물어본다.

- 즉 Object 클래스의 == 와 equals() 는 기본적으로 같은 결과를 보여준다.

- 하지만 equals() 는 오버라이딩하여 객체더라도 그 안의 값이 같은지 만드는 경우가 많다.

```java
@Override
public boolean equals(Object obj) {
    if (this == obj)
        return true;
    if (obj == null)
        return false;
    if (getClass() != obj.getClass())
        return false;
    Person other = (Person) obj;
    if (age != other.age)
        return false;
    if (name == null) {
        if (other.name != null)
            return false;
    } else if (!name.equals(other.name))
        return false;
    return true;
}

```


# hashCode()

- 자바 공식 문서에 다음과 같이 설명되어 있다.

- Returns a hash code value for the object. This method is supported for the benefit of hash tables such as those provided by HashMap

- 객체의 해시 코드 값을 반환한다. 이 메소드는 HashMap에서 제공하는 것과 같은 해시 테이블의 이점을 위해 지원된다. (10진수로 반환함)

- 즉 HashMap, HashTable 과 같은 자료구조에서 hashCode() 를 이용해 해시 코드 값을 얻어 바로 해당 key 에 대한 value 를 찾을 수 있도록 한다.(O(1)의 시간복잡도)

- 객체마다 다른 해시 코드 값을 가지고 있는건 자명한 사실. 그러므로 hashCode() 는 각 객체에 대응하는 고유한 정수값(주소값)을 리턴한다.

- IDE 에 보면 auto generate 부분에 equals() 와 hashCode() 를 같이 generate 하도록 묶여있는 것을 볼 수 있다.

- 이는 equals() 만 오버라이딩하면 부작용이 있을 수 있기 때문이다. 그래서 hashCode() 도 같이 오버라이딩 해서 아래와 같은 부작용을 피해야 한다.

```java
Set<Person> hset = new HashSet<>();
Person person1 = new Person("jdk", 27);
Person person2 = new Person("jdk", 27);
System.out.println("person1 : "+person1.hashCode());//2018699554
System.out.println("person2 : "+person2.hashCode());//1311053135
System.out.println(person1.equals(person2));//true
hset.add(person1);
hset.add(person2);
System.out.println(hset.size());// 2 -> 같은 내용인데 주소값이 다르므로 add 가 되어 버려 Set 자료구조의 특징인 중복이 안된다는 것을 깨뜨려버렸다 !
```


# String 의 equals(), hashCode()

![image](https://user-images.githubusercontent.com/25449640/119794344-6b925200-bf12-11eb-8541-786d62ddf36a.png)


```java
String a = new String("동진");
String b = new String("동진");

String c = "동진";
String d = "동진";

```

- 프리미티브 타입과 다르게 참조 타입 변수는 `스택` 영역에 변수와 그 변수가 `heap` 영역의 주소값을 담아둔다. 그리고 실제로 "a" 라는 값은 `heap` 영역에 저장되어 있는 구조이다.

- 그러나 특별하게도 `String` 에서 문자열 리터럴은 스트링 상수 풀을 이용하여 특별한 취급을 받는다.

- 문자열 리터럴 방식으로 선언하게 되면 내부적으로 `intern()` 메소드를 호출하여 문자열 상수 풀에 있는지 검색하고 없으면 문자열 상수 풀에 해당 리터럴을 추가한다.

- 그 이유는 똑같은 문자열을 반복해서 사용할 때 새로운 `문자열 리터럴` 을 생성하지 않고 호율적인 메모리 사용을 위함이다.

- a == b -> `false` : 문자열 상수 풀이 아닌 heap 에 저장되어 있음

- a.equals(b) -> `true` : 주소 값이 아닌 값을 비교한다.

- a == c -> `false` : c 가 가리키는 "동진" 은 문자열 상수 풀에 있고 a 가 가리키는 "동진" 은 heap 에 있다.

- c == d -> `true` : c, d 모두 문자열 상수 풀의 "동진" 을 가리킨다.

- c.equals(d) -> `true`

- c 와 d 에서 만든 `"동진"` 은 문자열 상수 풀에 저장되어 1개만 존재하게 된다. 그리고 c, d 는 문자열 상수 풀에 있는 동일한 녀석을 참조하게 되는 것이다. 그래서 c == d 가 `true` 가 된다.


# String 의 hashCode()

```java
    /**
     * Returns a hash code for this string. The hash code for a
     * {@code String} object is computed as
     * <blockquote><pre>
     * s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
     * </pre></blockquote>
     * using {@code int} arithmetic, where {@code s[i]} is the
     * <i>i</i>th character of the string, {@code n} is the length of
     * the string, and {@code ^} indicates exponentiation.
     * (The hash value of the empty string is zero.)
     *
     * @return  a hash code value for this object.
     */
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

```java
String a = "hello world";
String b = "hello world";

log.info(a == b ? "스트링 상수 풀에 의해 두 객체는 같다!" : "뭔소리? 다르지!"); // 스트링 상수 풀에 의해 두 객체는 같다
log.info(a.hashCode()); // 1794106052
log.info(b.hashCode()); // 1794106052
log.info(b.equals (a) == true ? "true" : "false"); // true
```

- 위와 같이 String 의 hashCode() 는 Object 클래스의 hashCode() 를 사용하지 않고 재정의 했다.

- 같은 문자열은 같은 해시 코드 값을 같도록 했다는게 주요한 차이이다. 그 이유는 JVM 의 `heap 영역` 과 관련있다.

- 우리는 개발을 할 때 수많은 문자열 리터럴을 사용한다. 그 중에서도 같은 문자열 리터럴을 여러번 반복해서 사용하는 경우가 있다. 이럴 때 너무 많은 문자열이 `heap` 메모리 영역에 올라가기 때문에 효율적인 메모리 사용을 위해 `String Constant Pool` 을 만들어 두고 여기에 문자열 리터럴 들을 두어 관리하게 되었다.
