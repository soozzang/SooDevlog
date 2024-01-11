---
title: "SDKMAN으로 자바버전 관리하기"
date: 2023-10-02
tags:
  - Java
---

> 예전에 자바 11과 17에서 버전을 왔다갔다하면서 번거롭게 변경해주었던 기억이 있는데, 버전관리를 간편하게 해주는 SDKMAN을 알게되어 사용법을 정리하고자 한다.

### SDKMAN
SDKMAN!은 커맨드 라인에서 다양한 종류의 Open JDK나 gradle, maven 등 Java 기반 개발 도구를 설치하고 관리할 수 있게 해준다.


### 설치(mac os)
터미널에 아래 명령을 입력.   
``` bash
$ curl -s "https://get.sdkman.io"
```    

설치완료 후, 터미널에 아래 명령 실행.    
``` bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
``` 

SDKMAN!의 버전을 확인하여 설치완료 확인.    
``` bash
$ sdk version
```

### Java 설치하기

설치할 Java 버전 조회.    

``` bash
$ sdk list java | bash    

================================================================================
Available Java Versions for macOS ARM 64bit
================================================================================
 Vendor        | Use | Version      | Dist    | Status     | Identifier
--------------------------------------------------------------------------------
 Corretto      |     | 21           | amzn    |            | 21-amzn
               |     | 21.0.1       | amzn    |            | 21.0.1-amzn
               |     | 20.0.2       | amzn    |            | 20.0.2-amzn
               |     | 20.0.1       | amzn    |            | 20.0.1-amzn
               |     | 17.0.9       | amzn    |            | 17.0.9-amzn
               |     | 17.0.8       | amzn    |            | 17.0.8-amzn
               |     | 17.0.7       | amzn    |            | 17.0.7-amzn
               |     | 11.0.21      | amzn    |            | 11.0.21-amzn
               |     | 11.0.20      | amzn    |            | 11.0.20-amzn
               |     | 11.0.19      | amzn    |            | 11.0.19-amzn
               |     | 8.0.392      | amzn    |            | 8.0.392-amzn
               |     | 8.0.382      | amzn    |            | 8.0.382-amzn
               |     | 8.0.372      | amzn    |            | 8.0.372-amzn
 Gluon         |     | 22.1.0.1.r17 | gln     |            | 22.1.0.1.r17-gln
               |     | 22.1.0.1.r11 | gln     |            | 22.1.0.1.r11-gln
 GraalVM CE    |     | 21           | graalce |            | 21-graalce
               |     | 20.0.2       | graalce |            | 20.0.2-graalce
               |     | 20.0.1       | graalce |            | 20.0.1-graalce
               |     | 17.0.8       | graalce |            | 17.0.8-graalce
               |     | 17.0.7       | graalce |            | 17.0.7-graalce
 GraalVM Oracle|     | 21           | graal   |            | 21-graal
               |     | 21.0.1       | graal   |            | 21.0.1-graal
               |     | 20.0.2       | graal   |            | 20.0.2-graal
               |     | 20.0.1       | graal   |            | 20.0.1-graal
               |     | 17.0.9       | graal   |            | 17.0.9-graal
               |     | 17.0.8       | graal   |            | 17.0.8-graal
               |     | 17.0.7       | graal   |            | 17.0.7-graal
 Java.net      |     | 22.ea.20     | open    |            | 22.ea.20-open
               |     | 22.ea.19     | open    |            | 22.ea.19-open
               |     | 22.ea.18     | open    |            | 22.ea.18-open
               |     | 22.ea.17     | open    |            | 22.ea.17-open
               |     | 22.ea.16     | open    |            | 22.ea.16-open
               |     | 22.ea.15     | open    |            | 22.ea.15-open
               |     | 21           | open    |            | 21-open
               |     | 21.ea.35     | open    |            | 21.ea.35-open
               |     | 21.0.1       | open    |            | 21.0.1-open
               ...
================================================================================
Omit Identifier to install default version 21-tem:
    $ sdk install java
Use TAB completion to discover available versions
    $ sdk install java [TAB]
Or install a specific version by Identifier:
    $ sdk install java 21-tem
Hit Q to exit this list view
================================================================================
(END) | bash
```
설치가 완료되면 방금 설치한 버전을 기본 값으로 설정할건지 묻는다.

> **설치된 Java의 위치: ~/.sdkman/candidates/java/**
{: .prompt-info }

### 설치된 Java 버전 삭제

``` bash
$ sdk uninstall java 8.0.192-zulu
``` 

### 설치 가능한 Java 버전 조회

``` bash
$ sdk list java
``` 

### '설치된' Java 버전 변경

바꾸고 싶은 버전으로 변경하기    
``` bash
$ sdk use java [변경하고 싶은 버전]
``` 
현재 사용 버전 확인      
``` bash
$ sdk current
``` 

### default 버전 지정

``` bash
$ sdk default java [지정하고 싶은 버전]
``` 

### 버전 업그레이드

``` bash
$ sdk upgrade java 
``` 

### 🔗 Reference 
**[SDK manager - SDKMAN으로 JAVA 관리하기](https://steady-hello.tistory.com/134)**    
**[SDK! 으로 Java 버전 관리하기](https://phoby.github.io/sdkman/)**  