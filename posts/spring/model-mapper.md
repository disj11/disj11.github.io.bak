---
title: Entity to DTO (ModelMapper)
date: 2021-08-06T10:58+09:00
menu:
  sidebar:
    name: Entity to DTO (ModelMapper)
    identifier: entity-to-dto
    parent: spring
    weight: 10
---

## 개요

DTO와 Entity간의 변환을 편하게 하기 위해 `ModelMapper` 를 사용하는 방법을 알아보자.

## ModelMapper

DTO와 Entity간의 변환을 위해 사용하는 가장 기본적인 라이브러리에는 `ModelMapper` 가 있다.

```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>2.4.4</version>
</dependency>
```

[여기](https://search.maven.org/classic/#search|gav|1|g%3A%22org.modelmapper%22%20AND%20a%3A%22modelmapper%22)에서 `ModelMapper`의 버전을 확인할 수 있다.

다음으로 `ModelMapper` 사용을 위해 Spring Bean으로 등록한다.

```java
@Bean
public ModelMapper modelMapper() {
    return new ModelMapper();
}
```

`ModelMapper` 의 기본적인 AccessLevel은 public이고, 명명규칙으로 자바빈즈를 따르기 때문에 private 필드로 정의하고 setter가 없다면 제대로 작동하지 않는다. 이러한 상황에서도 작동하기를 원한다면 다음과 같이 구성할 수 있다.

```java
@Bean
public ModelMapper modelMapper() {
    ModelMapper modelMapper = new ModelMapper();
    modelMapper.getConfiguration()
            .setFieldAccessLevel(org.modelmapper.config.Configuration.AccessLevel.PRIVATE)
            .setFieldMatchingEnabled(true);
    return modelMapper;
}
```

`ModelMapper` 의 자세한 규칙은 [여기](http://modelmapper.org/user-manual/configuration/)에서 확인할 수 있다.

사용법은 다음과 같이 굉장히 단순하다.

```java
Post post = modelMapper.map(postDto, Post.class);
```

좀 더 다양한 사용법을 확인하고 싶다면, [여기](https://www.baeldung.com/entity-to-and-from-dto-for-a-java-spring-application)를 확인하자.