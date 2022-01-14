---
sidebar:
nav: "main"
layout: single
title:  "Design-pattern Observer 패턴"
categories: [Programming,design pattern]
tags: [Programming,design pattern,GOF,Decorator]
teaser: /assets/images/teaser/designpattern.png
---

<p align="center"><img src="/assets/images/teaser/designpattern.png" width="100%" height="auto"></p>

옵저버 패턴에 대한 포스팅입니다.

#### 정의
한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다 의존성을 정의합니다.


옵저버 패턴의 경우 신문구독을 생각하면 이해하기 쉽습니다.\
신문이 발행(객체의 정보 변경)되면 그 정보가 다른 객체들 에게 전달되는 구조라 생각하시면 됩니다.

자바에서 옵저버API를 제공하긴 하나 직접 구현하는 예제코드로 준비하였습니다.

<p align="center"><img src="/assets/images/posts/20220114/observer.jpeg" width="100%" height="auto"></p>

```java
public interface Subject {
	
    //옵저버를 등록하고 제거하는 역할의 메서드
    public void registerObserver(ObserverH o);
    public void removeObserver(ObserverH o);
    
    //주제 객체의 상태가 변경되었을때 모든 옵저버들에게 알리기위해 호출되는 메서드
    public void notifyObservers();

}

public interface ObserverH {
    public void update(float temp, float humidity, float pressure);
}

public interface DisplayElement {
    public void display();
}


//구현 클래스
public class WeatherData implements Subject{

  private ArrayList observers;
  private float temperature;
  private float humidity;
  private float pressure;

  public WeatherData() {
    observers = new ArrayList(); // 일대다 관계를 위해 옵저버를 ArrayList로 관리합니다.
  }

  //subject의 인터페이스를 구현하는 코드
  @Override
  public void registerObserver(ObserverH o) {
    observers.add(o);
  }

  @Override
  public void removeObserver(ObserverH o) {
    int i = observers.indexOf(o);
    if(i >= 0){
      observers.remove(i);
    }
  }

  @Override
  public void notifyObservers() {
    for (int i = 0; i < observers.size(); i++) {
      ObserverH observerH = (ObserverH) observers.get(i);
      observerH.update(temperature, humidity, pressure);
    }
  }

  public void measurementsChanged(){
    notifyObservers();
  }

  public void setMeasurements(float temperature, float humidity, float pressure){
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    measurementsChanged();

  }
}
public class CurrentConditionsDisplay implements ObserverH, DisplayElement{

  private float temperature;
  private float humidity;
  private Subject weatherData;

  public CurrentConditionsDisplay(Subject weatherData){
    this.weatherData = weatherData;
    weatherData.registerObserver(this);
  }

  @Override
  public void display() {
    System.out.println("Current conditions: "+ temperature + "F degrees and" + humidity + "% humidity");

  }

  @Override
  public void update(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    display();
  }
  //옵저버객체에서 삭제한후를 보려고 작성한코드입니다
  public void removeOb(Subject weatherData){
    this.weatherData = weatherData;
    weatherData.removeObserver(this);
  }

}
```
실행 코드입니다.

```java
public class WeatherStation {
    public static void main(String[] args) {
        WeatherData weatherData = new WeatherData();

        CurrentConditionsDisplay currentConditionsDisplay =
                new CurrentConditionsDisplay(weatherData);

        weatherData.setMeasurements(80, 60, 30.4f);
        weatherData.setMeasurements(50, 42, 29.0f);
        
        //옵저버 삭제후 작동하는지
        currentConditionsDisplay.removeOb(weatherData);
        weatherData.setMeasurements(33, 12, 26.3f);
    }
}
```
<p align="center"><img src="/assets/images/posts/20220114/observer_run.jpeg" width="100%" height="auto"></p>

실행 결과입니다

주제 객체의 정보를 set으로 수정 시 CurrentConditionsDisplay에 정보가 전달됩니다.\
옵저버가 삭제된 후에는 CurrentConditionsDisplay의 display() 메서드가 실행되지 않았습니다.
이런 옵저퍼 패턴의 방식을 푸시(push)라고 합니다.\
주제 객체가 변경될 때마다 observer로 데이터를 전달합니다.


자바에서도 observer클래스로 옵저버패턴을 지원하지만 인터페이스가 아닌 클래스로 되어있어 상속을 받아야 하기 때문에 다중 상속을 지원하지 않는 자바에서는 옵저버패턴이 필용한클래스가 다른 클래스를 상속받아야 하는 경우 사용이 불가능한 걸로 알고 있습니다.



예제 코드는 <https://github.com/Picbel/TIL.designPattern> 참고부탁드립니다.