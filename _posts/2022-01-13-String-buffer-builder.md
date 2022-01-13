---
sidebar:
nav: "main"
layout: single
title:  "String, StringBuffer, StringBuilder"
categories: [Java]
tags: [Java,String,StringBuffer,StringBuilder]
teaser: /assets/images/teaser/java.jpeg
---

<p align="center"><img src="/assets/images/teaser/java.jpeg" width="70%" height="auto"></p>

자바에서 문자열을 다루는 클래스로 크게 3가지가 있습니다.\
각각의 차이점과 장단점에 대한 포스팅입니다.

String
---
String의 가장 큰 특징은 불변(immutable)하다 는 것입니다.
  - String클래스는 불변의 속성을 가지고있기 때문에 값을 바꿀수 없습니다.
  > 처음 접할때 하는 String에 대한 오해\
  > String str = "A"\
  > str = "B"\
  > 다음과 같은 코드는 변수 str에 A의 값을 수정하는게 아닌 "B"라는 값을 가진 새로운 String인스턴스를 생성하여 메모리 영역을 교체하는것입니다.
  - 문자열의 수정, 삭제 등 값을 변형해야 하는경우가 잦은 경우에는 매번 새로운 인스턴스를 생성하고 교체하는 것이기때문에 비효율적입니다.
  - 이러한 한계때문에 java에서는 StringBuffer와 StringBuilder를 도입합니다.

StringBuffer
---
StringBuffer의 특징은 가변성을 가지며 멀티쓰레드 환경에서 안정성(thread-safe)을 가집니다.(동시성문제가 없습니다.)

StringBuilder
---
StringBuilder는 가변성도 가지지만 멀티쓰레드 환경에서 안정성을 보장하지 않습니다. 다만 덕분에 위 두개의 클래스보다 성능면에서 우월합니다.


##한줄 요약 정리
- String : 문자열 수정이 필요없을때
- StringBuffer : 문자열 연산이 많지만 멀티쓰레드 환경에서 사용해야할때
- StringBuilder : 문자열 연산이 많고 멀티쓰레드 환경이 아닐때, 동시성문제를 고려하지 않아도 될때