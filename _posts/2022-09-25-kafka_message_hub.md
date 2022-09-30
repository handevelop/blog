---
title:  "Kafka 활용사례 - 메시지 허브"
excerpt: ""

categories:
  - kafka
last_modified_at: 2022-09-25T15:06:00-05:00
---


# kafka 를 공부해보니 활용사례가 여러가지가 있었다
- 데이터 파이프라인으로 활용 -> 유실없이 데이터 수집, 저장, ETL 등을 수행하는 파이프 라인으로 활용
- 시스템 모니터링으로 활용 -> 아주 간단하게는 어플리케이션에서 로그 라이브러리(log4j 등)로 찍은 로그를 퍼블리싱하여 카프카에서 구독해서 EKS, DB, S3 등으로 저장하는 시스템 모니터링으로 활용
- 메시지 허브로 활용 -> 메시지/이벤트 등을 통해 MSA 도메인간 장애 전파를 막고 결합도를 루즈하게 만들어주게끔 메시지 허브로 활용



# 카프카를 도입한 이유
- 기획 요구사항 중에 `상품평가(리뷰) 사이즈 정보를 기입할 시에 그 회원이 작성한 모든 상품평가에 사이즈 정보를 즉시 업데이트 반영해주세요.` 라는 요건이 있었다.
- 상품평가 작성 API 속에 해당 회원의 모든 상품평가를 가져오고 전부 업데이트 쳐주는걸 넣는건 말이 안됐다.(상품평가 작성 API 안에서 이전 상품평가에 대한 업데이트 작업에 대해서 완료가 되었는지 확인을 하고 안되었으면 다시 재시도를 해야하는 말도안되는 일이다.)
- Thread safe 를 위해 싱글 스레드로 구축된 배치 프로그램으로 하는것도 내키지 않았다.
- 그래서 나는 카프카를 도입하여 메시지 허브로 이 문제를 해결하고자 했다.
    - 왜 간단한 RabbitMQ 나 SQS 가 아니라 카프카를 도입했냐면 우리는 `앞으로 상품평가 데이터를 새로운 DB로 이관하기 위해 카프카를 활용해서 데이터파이프라인을 구축하려는 목표`를 갖고 있었기 때문에 메시지처리만 하지 않을거라는 생각이 있었다.


# 카프카로 메시지 허브 간단하게 구현하여 활용해보기
- 우리의 새로운 서버는 CQRS 패턴을 추구하여 Command / Query 를 분리한 Command / Query 서버로 되어있다.
- Query 서버에는 오로지 view 를 위한 조회 API 만을 작성했기 때문에 여기에 구현하는것은 적절하지 않다는 판단을 했고, 나는 간단하게 Command 서버에 카프카 메시지 허브를 구축해보았다.
- Command 서버에서 사이즈 정보가 포함된 상품평가 작성 API 요청이 들어오면 회원번호, 사이즈 정보 등을 포함한 메시지를 카프카에 pub 하였다.
- 카프카 설정은 카프카 부트 스트랩 서버 설정을 이용했고 메시지를 주고 받을때는 String 시리얼/디시리얼라이저를 활용했다.
- 우리의 3대의 Command 서버에서는 실시간으로 토픽을 구독하여 작성된 사이즈 정보를 실시간 업데이트 할 수 있었다.
- 메시지 sub 시에 해당 회원의 모든 리뷰를 조회하는 API 를 호출하는데, 이 때 실패한 경우 실패 토픽에 다시 쏴주었다.
- 거기에서도 실패하면 DB 에 저장하고, 나중에 DB에 쌓인 녀석들을 보상 로직을 통해 업데이트 해주었다. 멱등성을 처리하는게 중요하다고 느꼈다.
- 이 때 활용된 토픽은 파티션 12개, 레플리케이션 팩터는 3개로 구성했다. (국민 셋팅이라고..)
- 메시지를 활용한 간단한 작업이었기에 국민 셋팅을 따라서 셋팅을 했고, 운영 배포 후 2개월이 지났으나 아무 문제 없어보인다.

```java
@Configuration
public class ExampleKafkaTopicConfig {

    // . . . 
    private final static int TOPIC_PARTITIONS = 12;
    private final static short REPLICATION_FACTOR = 3;

    // 카프카 최초 등록
    @Bean
    public NewTopic newTopic() {
        return new NewTopic("product.review.size", TOPIC_PARTITIONS, REPLICATION_FACTOR);
    }

    // . . .
}


// 카프카템플릿을 활용하여 sender 부분 구현
@Component
public class KafkaMessageSender {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public void send(String topic, String value) {

        var futures = kafkaTemplate.send(topic, value);
        futures.addCallback(new ListenableFutureCallback<>() {
            @Override
            public void onSuccess(SendResult<String, String> result) {
            }

            @Override
            public void onFailure(Throwable ex) {
                log.error("kafka message send failed : {}", futures.get());
            }
        });

    }
}
```