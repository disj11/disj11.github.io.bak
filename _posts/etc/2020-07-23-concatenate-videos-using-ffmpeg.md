---
layout: post
current: post
navigation: True
title: FFmpeg를 사용하여 동영상 합치기
date: 2020-07-23 19:30
tags: [ffmpeg]
class: post-template
subclass: 'post'
author: saturday
---

### 소개
FFmpeg를 사용하여 동영상을 합치는 방법을 소개

### 방법
1. concat demuxer
    * 트랜스 코딩을 피하므로 매우 빠름
    * 동영상의 비디오 및 오디오 인코딩이 모두 동일한 경우에만 작동
2. file level concatenation
    * 이 방법을 사용할 수 있는 인코딩은 거의 없음 (MPEG-2 Transport Stream codec (.ts)일때에만 사용 가능)
3. complex filtergraph with the concat filter
    * 다른 방식으로 인코딩된 동영상을 합칠때 사용 가능
    * 트랜스코딩이 발생하므로 품질이 저하되고, 느릴 수 있음
    * 문법을 이해하기가 힘듦

### 예제
concat demuxer:
```
ffmpeg -f concat -i list.txt -c copy out.mp4
```
대부분의 ffmpeg 며령광 달리 이 명령은 연결하려는 동영상을 텍스트 파일에서 가져옴.
텍스트 파일은 다음과 같음
```
file 'video1.mp4'
file 'video2.mp4'
```

file level concatenation:
```
ffmpeg -i "concat:video1.ts|video2.ts" -c copy out.ts
```

complex filtergraph with the concat filter:
```
ffmpeg -i video1.mp4 -i video2.flv -filter_complex \
"[0:v][0:a][1:v][1:a] concat=n=2:v=1:a=1 [outv] [outa]" \
-map "[outv]" -map "[outa]" out.mp4
```
의미를 하나씩 해석해보자.

`[0:v][0:a][1:v][1:a]`

[index_of_input : stream_type]

첫 번째 입력 소스의 비디오 스트림, 첫 번째 입력 소스의 오디오 스트림, 두 번째 입력 소스의 비디오 스트림, 두 번째 입력 소스의 오디오 스트림을 사용

---

`concat=n=2:v=1:a=1`

`concat`: 필터의 이름

`n=2`: 두 개의 입력 소스가 있음을 지정

`v=1`: 출력 비디오 스트림의 수가 하나라고 설정

`a=1`: 출력 오디오 스트림의 수가 하나라고 설정

---

`[outv] [outa]`

필터로 만든 데이터 스트림에 레이블을 지정함.  
나중에 나올 `-map` 에서 필요

---

`-map "[outv]"-map "[outa]"`

입력 파일에서 스트림을 사용하지 말고, 대신 필터 그래프에서 생성한 데이터 스트림을 사용하도록 지정

[concat 필터 문서 참조](https://ffmpeg.org/ffmpeg-filters.html#concat){:target="_blank"}

### 참고 사이트
[https://dev.to/dak425/concatenate-videos-together-using-ffmpeg-2gg1](https://dev.to/dak425/concatenate-videos-together-using-ffmpeg-2gg1){:target="_blank"}
