---
layout: post
current: post
navigation: True
title: Bit Masking (비트마스킹)
date: 2019-06-01 10:47
tags: [Data Structure, Bit Masking]
class: post-template
subclass: 'post'
author: saturday
---

### 소개
> 비트는 0, 1의 값을 가질 수 있는 데이터의 최소 단위이다.
비트 마스크는 이러한 비트에 마스크를 씌워 비트의 값에 변화를 주는 행위이다.
이 설명으로는 비트마스킹이 무엇인지 표현이 잘 안되는데, 아래에서 상세히 다뤄보자.

### AND 연산
> AND 연산은 비트의 값과 마스크의 값이 모두 1일 때에는 1, 그 외의 경우는 0이 된다.

| :---  | :-------- |
| VALUE | 0000 0101 |
| MASK  | 0000 1111 |
| RESULT| 0000 0101 |

### OR 연산
> OR 연산은 비트의 값과 마스크의 값중 하나라도 1일 때에는 1, 모두 0일 때에는 0이 된다.

| :---  | :-------- |
| VALUE | 0000 0101 |
| MASK  | 0000 1111 |
| RESULT| 0000 1111 |

### XOR 연산
> XOR 연산은 비트의 값과 마스크의 값이 값으면 0, 다르면 1이 된다.

### 자주 사용하는 연산
1. k번 비트가 1인지 0인지 확인   
`status & (1 << k)`
2. k번 비트를 1로 만들고 싶다.   
`status = status | (1 << k)` or `status |= (1 << k)`
3. k번 비트를 0으로 만들고 싶다.   
`status = status & ~(1<<k)` or `status &= ~(1<<k)`

위의 내용을 모두 읽어도 1~3번 이해가 안 간다면,
비트 NOT 연산, 비트 시프트 연산에 대하여 찾아보면 좋다.

### 참고
JAVA의 경우는 EnumSet이라고 하는 데이터 타입을 제공하여,
특별한 경우가 아니라면 비트 연산을 사용하는 경우가 드물다.
EnumSet의 자세한 정보가 궁금하다면, [https://www.baeldung.com/java-enumset](https://www.baeldung.com/java-enumset)
사이트를 참고하면 된다.

[참고 사이트]   
[https://kks227.blog.me/](https://kks227.blog.me/){:target="_blank"}