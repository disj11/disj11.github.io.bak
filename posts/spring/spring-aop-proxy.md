---
title: Spring AOP, Proxy (관점 지향 프로그래밍과 프록시)
date: 2019-11-15T12:40:00+09:00
menu:
  sidebar:
    name: Spring AOP, Proxy (관점 지향 프로그래밍과 프록시)
    identifier: spring-aop-proxy
    parent: spring
    weight: 10
---

## 개요

AOP(관점 지향 프로그래밍)는 문제를 해결하기 위한 관심 사항을 핵심 기능과, 공통 부분(Aspect)으로 나누어 공통적인 부분을 각각의 모듈로 만들어 활용하겠다는 것이다. 스프링에서 사용하는
어노테이션인 `@Transaction`, `@Async`, `@Cacheable` 등은 모두 이러한 AOP를 이용한 것이다.

이해를 돕기위해 코드를 준비했다. 아래의 코드는 모든 메서드들의 실행 시간을 측정하기 위한 코드이다.

```java
public void doSomething() {
    // 시작 시간 체크 (메서드 실행시간 체크를 위한 공통 부분)
    
    // 핵심 기능 (비지니스 로직)
    
    // 끝나는 시간 체크 (메서드 실행시간 체크를 위한 공통 부분)
    // 실행 시간 체크 (메서드 실행시간 체크를 위한 공통 부분)
}
```

우리가 모든 메서드들의 실행시간을 알고 싶다고 한다면, 실제로 메서드가 하는 일인 핵심 기능과, 메서드 실행 시간을 구하기 위한 공통 부분으로 나눌 수 있을 것이다.

위의 코드의 경우,

1. 메서드가 실행되기 시작한 시간 구하기
2. 메서드 핵심 기능 실행
3. 메서드가 완료된 시간 구하기
4. 메서드 실행이 완료된 시간 구하기
5. 메서드 실행시간 구하기 (메서드 실행이 완료된 시간 - 메서드가 실행된 시간)

1, 2, 4, 5 번이 실행 시간을 구하기 위해 모든 메서드에 들어가야 하는 공통 부분이고, 3번은 메서드마다 구현이 달라지는 핵심 부분일 것이다. 우리는 이렇게 핵심 부분과 공통 부분을 나누고, 공통적인 부분을
모듈로 만드는 것을 AOP 라고 한다.

## Proxy

스프링 AOP는 프록시를 이용하여 AOP를 구현하기 때문에, Proxy에 관해서 알아야 한다. 스프링은 공통 부분(Aspect) 적용 대상이 되는 객체에 대한 프록시를 만들어서 제공한다. 비지니스 로직에 접근을 할
때에는 이렇게 만들어진 프록시를 통해 간접적으로 접근하게 된다.

예를들어 Controller 에서 어떤 서비스의 doSomething() 메서드를 실행한 경우, 스프링은 프록시 객체를 생성하고, 생성된 프록시에서 공통 기능을 실행 후 doSomething() 메서드를 호출하거나,
doSomething() 메서드 실행 후 공통 기능을 실행한다.

이러한 프록시를 생성하기 위한 방법으로는 JDK Dynamic Proxy와 CGLIB을 이용하는 두 가지 방식이 있다. 지금부터 두 방식의 차이점을 알아보자.

### JDK Dynamic Proxy

자바 리플렉션 API가 제공하는 `java.lang.reflect.Proxy`를 사용하여 프록시 객체를 생성한다. 이 방식은 인터페이스를 기반으로 프록시 객체를 생성하기 때문에 **인터페이스에 정의되어 있지 않은
메서드는 AOP가 적용되지 않는다.**

### CGLIB

CGLIB는 인터페이스에 정의되어 있지 않더라도 프록시 객체를 생성할 수 있다. 하지만, CGLIB는 대상 클래스를 상속받아 프록시를 구현하기 때문에, **클래스가 final인 경우에는 프록시를 생성할 수 없다.**

## Spring AOP 주의사항

**같은 클래스 내에 있는 메서드를 호출하는 경우** 프록시가 생성되지 않기 때문에, **AOP가 적용되지 않는다.**