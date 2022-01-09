---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern Strategy 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Strategy]
teaser: /assets/images/teaser/designpattern.png
---

<p align="center"><img src="/assets/images/teaser/designpattern.png" width="70%" height="auto"></p>

실무에서 상당히 자주 쓰이는 유용한 strategy패턴에 대한 포스팅입니다.

#### 정의
>알고리즘(이하 로직)을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만든다. 스트래티지 패턴을 활용하면 로직을 사용하는 클라이언트와는 독립적으로 로직을 변경할 수 있다.


예시(해당 예시는 헤드퍼스트 디자인 패턴 책에 나온 예시이다)
<p align="center"><img src="/assets/images/posts/strategy.jpeg" width="70%" height="auto"></p>

해당 예시를 보면 여러 캐릭터들이 있고 또 여러 무기가 있습니다.\
요구사항) 기사는 검을 주로 쓰지만 활도 쏠수 있으며 궁수또한 활이 주력이지만 나이프를 사용할수있습니다.

무기의 교체가 가능하기때문에 직업별로 무기사용법을 구현하는것은 비효율 적입니다.\
이렇게 특정하게 변경을 해야 할 경우 단지 인터페이스의 구현 클래스를 새로 끼우는 방식으로 수정이 가능한 것이 이 패턴의 큰 장점입니다.

```
//먼저 캐릭터 추상클래스를 만듭니다
public abstract class Character {

    // 캐릭터들의 현재 착용 무기입니다.
    WeaponBehavior weapon;
    
    // 캐릭터들은 모두 전투라는 기본적인 기능을 가집니다.
    public abstract void fight();

    //무기의 인터페이스구현클래스를 동적으로 교체하기위해 작성합니다
    public void setWeapon(WeaponBehavior w){
        this.weapon = w;
        };
}

public class Knight extends Character{
    @Override
    public void fight() {
        weapon.useWeapon();
    }
}

//무기 인터페이스
// 모든 무기는 사용한다는 공통 기능을 포함합니다.
public interface WeaponBehavior {
    void useWeapon();
}

public class SwordBehavior implements WeaponBehavior{
    @Override
    public void useWeapon() {
        System.out.println("검으로 찌르다");
    }
}


public class bowBehavior implements WeaponBehavior {
    @Override
    public void useWeapon() {
        System.out.println("활을 쏘다");
    }
}
```
이제 기사가 검을 쓰다 활로 무기를 변경하는 상황을 가정해보겠습니다.
```
public class Main {
    public static void main(String[] args) {
    
        Knight k = new Knight();
		
        //기사가 검을 쓸때
        System.out.println("검 사용");
        k.setWeapon(new SwordBehavior());
        k.fight();
        
        //기사가 활을 쓸때
        System.out.println("활 사용");
        k.setWeapon(new bowBehavior());
        k.fight();

    }
}
```
실행 결과입니다.
<p align="center"><img src="/assets/images/posts/20220109/strategyLog.png" width="70%" height="auto"></p>

이제 어떤 캐릭터든 무기를 교체만 한다면 각 무기를 사용 할 수 있습니다.\
이처럼 변화가 되는 것을 인터페이스로 선언 후 구현 클래스를 작성합니다.\
메인클래스나 실행클래스에서는 인터페이스 선언 후 구현 클래스를 교체하여 사용하는 것이 핵심입니다.


