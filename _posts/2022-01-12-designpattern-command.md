---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern Command 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Command]
teaser: /assets/images/teaser/designpattern.png
---

<p align="center"><img src="/assets/images/teaser/designpattern.png" width="100%" height="auto"></p>


#### 정의
커맨드 패턴은 요구사항(정보)을 객체로 캡슐화할 수 있으며 매개변수를 써서 여러 가지 다른 요구사항을 집어넣을 수도 있습니다.
또한 요청내역을 큐에 저장하거나 로그로 기록할 수 있으며 작업 취소 기능도 지원합니다.

> 커맨드 패턴에는 명령(command), 수신자(receiver), 발동자(invoker), 클라이언트(client)의 네개의 용어가 항상 따른다.
> 커맨드 객체는 수신자 객체를 가지고 있으며, 수신자의 메서드를 호출하고, 이에 수신자는 자신에게 정의된 메서드를 수행한다. \
> 커맨드 객체는 별도로 발동자 객체에 전달되어 명령을 발동하게 한다.
> 발동자 객체는 필요에 따라 명령 발동에 대한 기록을 남길 수 있다. 
> 한 발동자 객체에 다수의 커맨드 객체가 전달될 수 있다. 
> 클라이언트 객체는 발동자 객체와 하나 이상의 커맨드 객체를 보유한다. 
> 클라이언트 객체는 어느 시점에서 어떤 명령을 수행할지를 결정한다. 
> 명령을 수행하려면, 클라이언트 객체는 발동자 객체로 커맨드 객체를 전달한다.
>
> 출처 : <https://ko.wikipedia.org/wiki/%EC%BB%A4%EB%A7%A8%EB%93%9C_%ED%8C%A8%ED%84%B4>

설명만 봐서는 잘 이해가 되지않네요.\
바로 예제코드를 보도록 하죠.
<p align="center"><img src="/assets/images/posts/20220112/command.jpeg" width="70%" height="auto"></p>

```java

// 명령을 전달 받는 수신자(receiver) 역할 
public class Light {
  public void on(){
    System.out.println("Light on");
  }
  public void off() {
    System.out.println("Light off");
  }
}


public interface Command {
  public void execute();
}

// 구체적 명령을 내리는 객체이다. 실행시 light.on 시킨다.
public class LightOnCommand implements Command{

  Light light;

  public LightOnCommand(Light light) {
    this.light = light;
  }
  @Override
  public void execute() {
    light.on();
  }
}

// 명령을 발동시키는 발동자(invoker) 역할
public class SimpleRemoteControl {

  Command slot;

  public SimpleRemoteControl () {}

  public void setCommand(Command command){
    slot = command;
  }

  public void buttonWasPressed() {
    slot.execute();
  }

}
```
이제 실행시켜볼 코드입니다.
```java
public class testCode {
    public static void main(String[] args) {
        // 발동자
        SimpleRemoteControl remote = new SimpleRemoteControl();
        
        // 명령을 받는 수신자
        Light light = new Light();
        
        // 명령을 실행시킬 객체와 수신자를 조립합니다.
        LightOnCommand lightOn = new LightOnCommand(light);

        // 발동자에게 구체적인 명령을 조립합니다.
        remote.setCommand(lightOn);
        
        // 버튼을 누릅니다. (명령 실행)
        remote.buttonWasPressed();
    }
}
```

<p align="center"><img src="/assets/images/posts/20220112/command_run.png" width="70%" height="auto"></p>

버튼을 누르니 조립하여둔 명령이 실행되어 불이 켜집니다.



예제 코드는 <https://github.com/Picbel/TIL.designPattern> 참고부탁드립니다.
