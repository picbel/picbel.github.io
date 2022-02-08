---
sidebar:
nav: "main"
layout: single
title:  "Memory Structure (메모리 구조) 요약"
categories: [Programming]
tags: [Programming,CS,Memory Structure]
teaser: /assets/images/teaser/programming.jpeg
---


<p align="center"><img src="/assets/images/teaser/programming.jpeg" width="100%" height="auto"></p>

프로그래밍의 가장 기초가 되는 메모리 구조에 대한 요약 포스팅입니다.


프로그램이 실행되기 위해서는 메모리에 로드(load)되어야 합니다.\
또한 사용하는 변수들을 저장할 메모리도 필요합니다.\
여기서 프로그램은 운영체제로부터 할당을 받습니다.

운영체제로부터 할당받는 대표적인 메모리 공간은

- 코드(code)
- 데이터(data)
- 스택(stack)
- 힙(heap)

입니다.

<p align="center"><img src="/assets/images/posts/20220111/memory-structure.png" width="70%" height="auto"></p>

### 코드(code) 영역

메모리의 코드 영역은 실행할 프로그램의 코드가 저장되는 영역으로 텍스트 영역이라고도 부릅니다.\
cpu는 코드영역에 저장된 명령어를 하나씩 가져가서 처리하게 됩니다.\
프로그램이 종료될때까지 계속 유지되는 영역입니다.

### 데이터(data) 영역

메모리의 데이터영역은 프로그램의 전역 변수와 정적(static)변수가 저장되는 영역입니다.\
데이터 영역은 프로그램의 시작과 함께 할당되며 프로그램이 종료되면 소멸합니다.

### 스택(stack) 영역

메모리의 스택 영역은 함수의 호출과 관계되는 지역변수와 매개변수가 저장되는 영역입니다.\
스택 영역은 함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸합니다.\
이렇게 스택영역에 저장되는 함수의 호출 정보를 스택 프레임이라고 합니다.\
스택 영역은 푸시 동작으로 데이터를 저장하고 팝 동작으로 데이터를 인출합니다.\
스택은 후입선출(LIFO,Last-in First-out) 방식에 따라 동작하므로 가장 늦게 저장된 데이터가 먼저 인출됩니다.\
스택 영역은 메모리의 높은 주소에서 낮은 주소의 방향으로 할당됩니다.

###  힙(heap) 영역

메모리의 힙영역은 사용자가 직접 관리할 수 있는 메모리 영역입니다.\
힙 영역은 메모리의 낮은 주소에서 높은 주소의 방향으로 할당됩니다.\
자바에선 garbage collector 가 관리를 해줍니다.