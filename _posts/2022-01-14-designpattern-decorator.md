---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern Decorator 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Decorator]
teaser: /assets/images/teaser/designpattern.png
---

<p align="center"><img src="/assets/images/teaser/designpattern.png" width="100%" height="auto"></p>

SOLID중 O(Open-Closed Principle)에 가장 어울리는 디자인 패턴인 데코레이터 패턴에 대한 포스팅입니다.

#### 정의
객체에 추가 요소를 동적으로 더할 수 있다. 데코레이터 패턴을 사용 시 서브클래스를 만드는 경우에 비해 훨씬 유연하게 기능을 확장할 수 있다.

<p align="center"><img src="/assets/images/posts/20220114/decorator.jpeg" width="100%" height="auto"></p>

간단하게 코드로 보시죠
```java
public abstract class Beverage {

    String description = "제목없음";

    public String getDescription() {
        return description;
    }
    public abstract double cost();
}


public abstract class CondimentDecorator extends Beverage{
    public abstract String getDescription();
}


public class Espresso extends Beverage{

    public Espresso() {
        description = "에스프레소";
    }

    @Override
    public double cost() {
        return 1.99;
    }
}


public class HouseBlend extends Beverage{

    public HouseBlend() {
        description = "하우스 블렌드 커피";
    }

    @Override
    public double cost() {
        return 0.89;
    }
}


public class Mocha extends CondimentDecorator {

    Beverage beverage;

    public Mocha (Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public double cost() {
        return .20 + beverage.cost();
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 모카";
    }
}


public class Soy extends CondimentDecorator {

    Beverage beverage;

    public Soy(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public double cost() {
        return .15 + beverage.cost();
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 두유";
    }
}


public class Whip extends CondimentDecorator {

    Beverage beverage;

    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public double cost() {
        return .10 + beverage.cost();
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 휘핑";
    }
}
```


```java
public class testCode {

    public static void main(String[] args) {
        //에소프레소
        Beverage beverage = new Espresso();
        System.out.println(beverage.getDescription() + " $" + beverage.cost());

        //에소프레소에 휘핑하고 모카 추가
        Beverage beverage2 = new Espresso();
        beverage2 = new Mocha(beverage2);
        beverage2 = new Whip(beverage2);
        System.out.println(beverage2.getDescription() + " $" + beverage2.cost());

        //하우스블랜드커피에 모카와 두유추가
        Beverage beverage3 = new HouseBlend();
        beverage3 = new Mocha(beverage3);
        beverage3 = new Soy(beverage3);
        System.out.println(beverage3.getDescription() + " $" + beverage3.cost());

    }
}
```
<p align="center"><img src="/assets/images/posts/20220114/decorator_run.jpeg" width="100%" height="auto"></p>

데코레이터 클래스로 객체를 감싸고 추가 요소가 필요할시 감싼 객체와 함께 데코레이터로 감쌉니다.(마트료시카가 생각나네요.)

예제 코드는 <https://github.com/Picbel/TIL.designPattern> 참고부탁드립니다.