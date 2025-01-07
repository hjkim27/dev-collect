---
title: jni header 파일 생성
date: 2024
type: 생성방법
---


### *.h 파일 생성

* File > Settings > Tools > External Tools > [+]
```properties
    Name        : javac -h
    Group       : java
    Description : Java Native Interface C Header and Stub File Generator

    # Tool Settings
    Progream    : ${JAVA_HOME}/bin/javac.exe
    Arguments   : -encoding utf-8 -h . $FileDir$/$FileName$
    Workingdirectory : $SourcepathEntity$
```
* ComtrueJNI.java 우클릭 > java > javac -h   
  ```properties
    ComtrueJNI.class    : ComtrueJNI.java 와 동일경로 생성
    com_comtrue_jni_ComtrueJNI.h    : java 하위 생성
  ```
  * *.h파일은 C 개발자에게 전달,라이브러리 작업 시 필요한 헤더파일 

