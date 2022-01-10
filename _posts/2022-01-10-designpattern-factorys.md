---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern 팩토리 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Factory Method,Abstract Factory]
teaser: /assets/images/teaser/designpattern.png
---
<p align="center"><img src="/assets/images/teaser/designpattern.png" width="70%" height="auto"></p>

실무에서 자주 쓰이고 다른 패턴들과도 잘 융합되는 생성패턴인 팩토리 패턴에 대한 포스팅입니다.\
팩토리 메서드 패턴과 추상 팩토리 패턴에 대해 설명합니다.



팩토리 메서드 패턴 (Factory Method)
---

#### 정의
객체를 생성하기 위한 인터페이스를 정의합니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정합니다.

<p align="center"><img src="/assets/images/posts/20220111/factory_method.png" width="70%" height="auto"></p>

객체 생성을 담당하는 부분은 factoryMethod이 부분입니다.\
서브클래스에서 팩토리 메서드를 정의하여 팩토리 메서드 호출로 객체를 반환합니다.
> Allen Holub은 "실용주의 디자인 패턴"에서\
> 
> Factory Method 패턴은 기반 클래스에 알려지지 않은 구체 클래스를 생성하는 Template Method라 할 수 있다. \
> Factory Method의 반환 타입은 생성되어 반환되는 객체가 구현하고 있는 인터페이스이다.\
> Factory Method는 또한 기반 클래스 코드에 구체 클래스의 이름을 감추는 방법이기도 하다(Factory Method는 부적절한 이름이다.\
> 사람들은 객체를 생성하는 모든 메서드를 자연스레 팩토리 메서드라 부르는 경향이 있는데 이러한 생성 메서드가 모두 Factory Method 패턴을 사용하는 것은 아니다.


추상 팩토리 패턴 (Abstract Factory)
---

#### 정의

인터페이스를 이용하여 서로 연관, 의존 객체를 구현 클래스로 지정하지 않고 생성할 수 있습니다.\
객체가 생성되거나 구성, 표현되는 방식과 무관하게 시스템을 독립적으로 만들때 사용됩니다.

```java

public abstract class Computer {
     
    public abstract String getRAM();
    public abstract String getHDD();
    public abstract String getCPU();
     
    @Override
    public String toString(){
        return "RAM= "+this.getRAM()+", HDD="+this.getHDD()+", CPU="+this.getCPU();
    }
}


public class PC extends Computer {
 
    private String ram;
    private String hdd;
    private String cpu;
     
    public PC(String ram, String hdd, String cpu){
        this.ram=ram;
        this.hdd=hdd;
        this.cpu=cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }
 
    @Override
    public String getHDD() {
        return this.hdd;
    }
 
    @Override
    public String getCPU() {
        return this.cpu;
    }
 
}


public class Server extends Computer {
 
    private String ram;
    private String hdd;
    private String cpu;
     
    public Server(String ram, String hdd, String cpu){
        this.ram=ram;
        this.hdd=hdd;
        this.cpu=cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }
 
    @Override
    public String getHDD() {
        return this.hdd;
    }
 
    @Override
    public String getCPU() {
        return this.cpu;
    }
 
}


public interface ComputerAbstractFactory {
 
	public Computer createComputer();
 
}


public class PCFactory implements ComputerAbstractFactory {
 
	private String ram;
	private String hdd;
	private String cpu;
	
	public PCFactory(String ram, String hdd, String cpu){
		this.ram=ram;
		this.hdd=hdd;
		this.cpu=cpu;
	}
	@Override
	public Computer createComputer() {
		return new PC(ram,hdd,cpu);
	}
 
}


public class ServerFactory implements ComputerAbstractFactory {
 
	private String ram;
	private String hdd;
	private String cpu;
	
	public ServerFactory(String ram, String hdd, String cpu){
		this.ram=ram;
		this.hdd=hdd;
		this.cpu=cpu;
	}
	
	@Override
	public Computer createComputer() {
		return new Server(ram,hdd,cpu);
	}
 
}


public class ComputerFactory {
 
	public static Computer getComputer(ComputerAbstractFactory factory){
		return factory.createComputer();
	}
}

```
실행 코드입니다.
```java
public class Factory {
  public static void main(String[] args) {
    Computer pc = ComputerFactory.getComputer(new PCFactory("2 GB", "500 GB", "2.4 GHz"));
    Computer server = ComputerFactory.getComputer(new ServerFactory("16 GB", "1 TB", "2.9 GHz"));
    System.out.println("AbstractFactory PC Config::" + pc);
    System.out.println("AbstractFactory Server Config::" + server);
  }
}
```

<p align="center"><img src="/assets/images/posts/20220111/abstract_factory.png" width="70%" height="auto"></p>

뵈시면 인터페이스(추상팩토리)에서 사용할 객체를 정의합니다.


예제 코드는 <https://github.com/Picbel/TIL.designPattern> 참고부탁드립니다.

