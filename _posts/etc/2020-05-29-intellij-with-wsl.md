---
layout: post
current: post
navigation: True
title: Intellij 에서 WSL 사용
date: 2020-05-29 13:00
tags: [Intellij, WSL]
class: post-template
subclass: 'post'
author: saturday
---

### 소개
Intellij에서 기본 터미널로 WSL 을 사용하는 방법입니다.
WSL은 이미 설치되어 있다고 가정하였습니다.

### 방법
1. File - Settings - Tools - Terminal
2. Shell path 에 아래 내용 입력  
`"C:\Users\<username>\AppData\Local\Microsoft\WindowsApps\ubuntu2004.exe" run`
3. WSL 에 접속하여 `~/.profile` 파일의 PATH 부분을 주석 처리 하고, 아래 내용 추가  
`PATH="$HOME/bin:$HOME/.local/bin:/usr/bin:$PATH"`  
왜 수정이 필요한지는 [https://github.com/microsoft/WSL/issues/1512#issuecomment-303517504](https://github.com/microsoft/WSL/issues/1512#issuecomment-303517504) 참고

### 노드를 사용하는 경우
1. File - Settings - Languages & Frameworks - Node.js and npm
2. Node Interpreter에 Add WSL 선택
3. WSL에 설치한 노드 선택
![참고 이미지](https://d3nmt5vlzunoa1.cloudfront.net/webstorm/files/2018/06/select-wsl-in-preferences.png)


### 참고한 사이트
[https://github.com/microsoft/WSL/issues/1512#issuecomment-303517504](https://github.com/microsoft/WSL/issues/1512#issuecomment-303517504)
[https://stackoverflow.com/questions/51912772/how-to-use-wsl-as-default-terminal-in-webstorm-or-any-other-jetbrains-products](https://stackoverflow.com/questions/51912772/how-to-use-wsl-as-default-terminal-in-webstorm-or-any-other-jetbrains-products)
[https://d3nmt5vlzunoa1.cloudfront.net/webstorm/files/2018/06/select-wsl-in-preferences.png](https://d3nmt5vlzunoa1.cloudfront.net/webstorm/files/2018/06/select-wsl-in-preferences.png)