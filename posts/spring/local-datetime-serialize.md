---
title: LocalDateTime Serialize
date: 2021-08-06T10:22+09:00
menu:
  sidebar:
    name: LocalDateTime Serialize
    identifier: local-datetime-serialize
    parent: spring
    weight: 10
---

## 개요

RestController로 `LocalDateTime`을 응답할 때의 포맷을 정해보자.

## 문제점

아무런 설정 없이 `LocalDateTime`을 응답하면 기본적으로 아래와 같은 형식이 응답된다.

```json
{
  "startDate": {
    "year": 2010,
    "month": "JANUARY",
    "dayOfMonth": 1,
    "dayOfWeek": "FRIDAY",
    "dayOfYear": 1,
    "monthValue": 1,
    "hour": 2,
    "minute": 2,
    "second": 0,
    "nano": 0,
    "chronology": {
      "id": "ISO",
      "calendarType": "iso8601"
    }
  }
}
```

## 해결 방안

`LocalDateTime`을 원하는 형식으로 응답하기 위해서 `@JsonFormat` 을 사용할 수 있다. 사용 방법은 다음과 같으며, pattern 인수에 사용되는 데이터 형식은 `SimpleDateFormat`
을 사용할때와 동일하다.

```java
@JsonFormat(pattern = "yyyy-MM-dd")
public LocalDateTime getStartDate() {
    return startDate;
}
```

대부분의 경우 `@JsonFormat` 을 사용하여 응답 형식을 지정할 수 있지만, 간혹 다른 설정들과 충돌이 발생하여 포맷이 적용되지 않는 문제가 발생할 수 있다. 이러한 경우에는 `@JsonSerialize`
어노테이션으로 serializer를 직접 지정하여 문제를 해결할 수 있다. `@JsonSerialize`가 적용된 코드는 다음과 같다.

```java
@JsonSerialize(using = LocalDateTimeSerializer.class)
@JsonFormat(pattern = "yyyy-MM-dd")
private LocalDateTime registerDatetime;
```

위와 같은 방식으로 사용 시 `LocalDateTime` 필드를 작성할 때마다 어노테이션을 붙여야 하는 번거로움이 발생한다. 만약 해당 프로젝트의 모든 응답을 통일하고 싶다면, 다음과 같은 구성을 통하여 번거로움을 해소할 수 있다.

```java
@Bean
public ObjectMapper objectMapper() {
    JavaTimeModule module = new JavaTimeModule();
    module.addSerializer(LocalDateTime.class,new LocalDateTimeSerializer(DateTimeFormatter.ISO_DATE_TIME));
    module.addDeserializer(LocalDateTime.class,new LocalDateTimeDeserializer(DateTimeFormatter.ISO_DATE_TIME));
    return new ObjectMapper()
        .setSerializationInclusion(JsonInclude.Include.NON_NULL)
        .registerModule(module);
}

@Bean
public MappingJackson2HttpMessageConverter jackson2HttpMessageConverter() {
    MappingJackson2HttpMessageConverter converter=new MappingJackson2HttpMessageConverter();
    converter.setObjectMapper(objectMapper());
    return converter;
}
```