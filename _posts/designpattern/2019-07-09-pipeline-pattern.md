---
layout: post
current: post
navigation: True
title: Pipeline Pattern (파이프라인 패턴)
date: 2019-07-09 23:33
tags: [Design Pattern]
class: post-template
subclass: 'post'
author: saturday
---

### 사용 이유
이니시스 빌링 결제 구현 중 `본인 인증 -> 빌링 키 발급 -> 빌링 키로 결제`
이렇게 세 가지 단계로 진행되는 파이프라인을 구성해야 했다.
본인 인증 결과가 빌링 키 발급의 input 이 되고, 빌링 키 발급의 결과가 빌링 키 결제의 input 이 되는 식이었다.
또 해당 파이프라인 전후로 다른 작업들의 추가 / 삭제가 간편해야 했다. 위의 조건을 만족하는 방법을 찾던 중
[이 글](https://medium.com/@deepakbapat/the-pipeline-design-pattern-in-java-831d9ce2fe21){:target="_blank"}을
발견하여, 시스템에 적용하였는데 꽤나 유용한 것 같아 글을 남기기로 하였다.

### Step Interface
파이프라인 각각의 단계에 해당하는 인터페이스

```java
public interface Step<I, O> {
    O process(I input);
}
```

### Pipeline Class
각각의 단계를 실제로 실행하는 파이프라인 클래스

```java
public class Pipeline<I, O> {
    private final Step<I, O> current;
    
    public Pipeline(Step<I, O> current) {
        this.current = current;
    }

    public <O2> Pipeline<I, O2> pipe(Step<O, O2> next) {
        return new Pipeline<>(input -> next.process(current.process(input)));
    }
    
    public O execute(I input) {
        return current.process(input);
    }
}
```

### Example
문자열 형태의 숫자를 int 형으로 변경 후 2를 곱하는 과정을 파이프로 구성해보자.

```java
public class ExamplePipeline {
    static class StringToIntStep implements Step<String, Integer> {
        public Integer process(String input) {
            return Integer.parseInt(input);
        }
    }
    
    static class calculationStep implements Step<Integer, Integer> {
        public Integer process(Integer input) {
            return input * 2;   
        }
    }
    
    public static void main(String[] args) {
        Pipeline<String, Integer> pipeline = new Pipeline<>(new StringToIntStep())
            .pipe(new calculationStep());
        System.out.println(pipeline.execute("3")); // 6 출력
    }
}
```