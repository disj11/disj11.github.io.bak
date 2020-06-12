---
layout: post
current: post
navigation: True
title: 자바 이미지 리사이징 후 색상이 변경되는 문제
date: 2020-05-06 18:00
tags: [Java, Image]
class: post-template
subclass: 'post'
author: saturday
---

### 소개
java 에서 jpg 이미지를 리사이징 후 색상이 이상하게 변경되어 기록

### 내용
1. 파일 업로드 중 업로드된 이미지의 색상 값이 이상하게 변경되는 것을 확인
2. 이미지 리사이징 로직을 제거하니 정상적으로 작동
3. [OpenIMAJ](https://mvnrepository.com/artifact/org.openimaj/image-processing/1.3.10) 를 사용했던 리사이징 로직을 [Thumbnailator](https://mvnrepository.com/artifact/net.coobird/thumbnailator/0.4.11) 를 사용하도록 변경
4. 문제가 해결되지 않음.
5. 확인 결과 리사이징 후 이미지에 alpha 채널이 추가되는 것을 확인함.

### 처리
알파 채널이 존재하는 경우 png 로 저장되도록 변경함.
```
// MultipartFile file
String ext = FilenameUtils.getExtension(file.getOriginalFilename());

// BufferedImage resizeImage
if (resizeImage.getColorModel().hasAlpha()) {
    ext = "png";
}
```
알파 채널이 존재하는 경우 알파 채널을 제거 후 jpg 로 저장하여도 됨
```
if (resizeImage.getColorModel().hasAlpha()) {
    resizeImage = dropAlphaChannel(resizeImage);
}

public BufferedImage dropAlphaChannel(BufferedImage src) {
     BufferedImage convertedImg = new BufferedImage(src.getWidth(), src.getHeight(), BufferedImage.TYPE_INT_RGB);
     convertedImg.getGraphics().drawImage(src, 0, 0, null);

     return convertedImg;
}
```

[참고 사이트]   
[https://github.com/rkalla/imgscalr/issues/82](https://github.com/rkalla/imgscalr/issues/82){:target="_blank"}