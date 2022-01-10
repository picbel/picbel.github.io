---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern Strategy 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Singleton]
teaser: /assets/images/teaser/designpattern.png
---

<p align="center"><img src="/assets/images/teaser/designpattern.png" width="70%" height="auto"></p>

프레임워크나 라이브러리를 공부 하실때 자주 만나는 패턴인 싱글턴 패턴에 대한 포스팅입니다.

#### 정의
해당 클래스의 인스턴스가 하나만 생성되고 어디서든지 그 인스턴스에 접근할 수 있도록 하기 위한 패턴\

해당 예시 코드들은 head first design patterns에서 추천하는 예시코드를 가져왔습니다.

인스턴스를 미리 만들지 않고 필요할 때 생성하는 코드.
---
```java
public class Singleton {

  private volatile static Singleton uniqueInstance;

  private Singleton() {}

  public static Singleton getInstance() {
    if (uniqueInstance == null) {
      synchronized (Singleton.class){
        if (uniqueInstance == null) {
          uniqueInstance = new Singleton();
        }
      }
    }
    return uniqueInstance;
  }
}
```
단순히 static으로 선언하여 get으로 인스턴스를 가져오는 경우는 멀티쓰레딩 환경에서 싱글턴 패턴을 적용하다 보면 큰 문제가 있습니다.\
(하나만 생성되어야 할 인스턴스가 두 개 생성될 수도 있다)\
그래서 DCL(Double Checking Locking) 방식을 써서 getInstance에서 동기화 영역을 줄일 수 있습니다.\
하지만 이 방법은 단점이 있습니다 멀티코어 환경에서 동작할 때 하나의 cpu를 제외하고는 다른 cpu가 lock에 걸린다고 합니다.

인스턴스를 처음부터 생성하는 코드
---
제가 애용하는 방식입니다.
```java
public class Singleton {

	private static Singleton uniqueInstance = new Singleton();
    
    private Singleton() {}
    
	pubilc static Singleton getInstance() {
    
    	return uniqueInstance;
    }
}
```
애플리케이션에서 반드시 Singleton의 인스턴스를 생성해야 하고 그 인스턴스를 항상 사용한다면 또는 인스턴스를 실행 중에 수시로 만들고 관리하기가 어렵다면 처음부터 Singleton인스턴스를 생성하는 것도 좋은 방법입니다.\
클래스가 로딩될 때 JVM에서 Singleton의 유일한 인스턴스를 생성해줍니다.\
JVM에서 유일한 인스턴스를 생성하기 전에는 그 어떤 스레드도 uniqueInstance 정적 변수에 접근할 수 없습니다.



------
추가로\
__Voliate__
멀티 쓰레딩 환경에서 동기화를 해주는 키워드입니다.\
<u>java 변수를 메인 메모리에 저장하겠다</u>를 명시하는 키워드 입니다.\
Multi Thread 환경에서 하나의 Thread만 read & write 하고 나머지 Thread가 read 하는 상황에서 가장 최신의 값을 보장합니다.

예제 코드는 <https://github.com/Picbel/TIL.designPattern> 참고부탁드립니다.

