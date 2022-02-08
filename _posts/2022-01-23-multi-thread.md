---
sidebar:
nav: "main"
layout: single
title:  "자바의 멀티스레딩과 @Async"
categories: [Spring]
tags: [Java,Spring,Async]
teaser: /assets/images/teaser/spring.png
---

<p align="center"><img src="/assets/images/teaser/spring.png" width="100%" height="auto"></p>

회사 업무를 하던 도중 외부 API를 호출할 일이 있었는데 영상을 등록하는 API다 보니 많이 느렸습니다.\
그래서 API 호출을 멀티스레딩을 적용하여 병렬적 처리를 하여 속도를 증가 시켰습니다.

ThreadPoolTaskExecutor
---
ThreadPoolTaskExecutor은 자바에서 멀티스레드를 위한 스레드 풀을 사용하기 쉽게 구현하여놓은 클래스입니다.
```java
public class AsyncThreadConfiguration {
  public Executor videoAsyncExecutor() {
    ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();

    // JVM의 사용 가능 코어 개수로 설정하였습니다.
    threadPoolTaskExecutor.setCorePoolSize(Runtime.getRuntime().availableProcessors());
    threadPoolTaskExecutor.setMaxPoolSize(100);

    // 기본이 Interger.max라 최대 사이즈를 지정하는 것이 성능에서 좋습니다
    threadPoolTaskExecutor.setQueueCapacity(1000);  // Queue 사이즈
    threadPoolTaskExecutor.setThreadNamePrefix("video-async-thread-");
    
    // 큐에 들어 있는 작업이 전부 종료되어야 스레드가 종료됩니다.
    threadPoolTaskExecutor.setWaitForTasksToCompleteOnShutdown(true);
    
    threadPoolTaskExecutor.setAwaitTerminationSeconds(20);
    threadPoolTaskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
    threadPoolTaskExecutor.initialize();
    return threadPoolTaskExecutor;
  }
}
```
주석으로 설명하였지만 다시 한번 확인하며 몇 가지 주의점을 확인하여보겠습니다.

- CorePoolSize : 스레드풀에서 기본으로 생성할 기본 스레드 수
- MaxPoolSize : 스레드풀에서 최대로 생성할 스레드 수
- QueueCapacity : 처리속도가 요청이 오는 속도보다 느려서 다음 스레드 완료를 기다리는 요청 대기 큐 입니다. 기본 설정이 Interger.MAX라라 사이즈를 지정하는 것이 성능 면에서 좋습니다
- ThreadNamePrefix : 스레드의 이름을 정합니다
- WaitForTasksToCompleteOnShutdown : 메인 스레드가 종료요청이 와도 큐에 있는 요청까지 처리 후 종료되는 설정입니다.
- AwaitTerminationSeconds : 종료 대기시간을 설정할 수 있습니다.
- RejectedExecutionHandler : 큐에 있는 요청 이상으로 왔을 때 예외처리를 할 수 있습니다. CallerRunsPolicy의 경우 요청한 스레드에서 직접 처리합니다.

설정 후 execute나 submit를 이용하여 사용할 수 있습니다.\
하지만 매번 ThreadPoolTaskExecutor 구현하여 멀티스레딩을 이용하는 건 핵심로직과 멀티스레딩 로직이 강하게 결합이 일어날 수도 있고
번거롭죠. 그래서 스프링에선 @Async를 제공합니다

@Async
---
```java
@Configuration // 스프링에 설정파일로 ThreadPoolTaskExecutor 정의 하여준다
@EnableAsync // async를 사용한다는 어노테이션 필수!
public class AsyncThreadConfiguration {
  @Bean(name = "videoAsyncExecutor") // 해당설정의 ThreadPoolTaskExecutor 빈으로 등록
  public Executor videoAsyncExecutor() {
    ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();

    threadPoolTaskExecutor.setCorePoolSize(Runtime.getRuntime().availableProcessors());
    threadPoolTaskExecutor.setMaxPoolSize(100);
    threadPoolTaskExecutor.setQueueCapacity(1000); 
    threadPoolTaskExecutor.setThreadNamePrefix("video-async-thread-");
    threadPoolTaskExecutor.setWaitForTasksToCompleteOnShutdown(true);
    threadPoolTaskExecutor.setAwaitTerminationSeconds(20);
    threadPoolTaskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
    threadPoolTaskExecutor.initialize();
    return threadPoolTaskExecutor;
  }
}
```
위에서 사용한 방법은 메서드레벨에서 정의하였습니다. AsyncConfigurer 상속하여 애플리케이션레벨에서 정의하는 방법도있는데 해당 링크 <https://www.baeldung.com/spring-async>를 참고하여주세요.\
애플리케이션레벨이 아닌 메서드레벨을 이용한 이유는 다음과 같습니다.
> 추후 ThreadNamePrefix이나 코어갯수 종료 시간 등 상세 설정을 바꿔야 하는 변경 사항이 있을 때 Bean을 등록하는 메서드레벨의 경우 Bean 하나만 더 등록하는 방법으로 상세한 설정이 자유로워집니다.

자가 호출 문제
---
하지만 @Async에도 문제가 있습니다. AOP를 사용하는 기술이기 때문에 자가 호출이 불가능하단 한계가 있습니다.
그래서 자가 호출 문제를 회피하는 방법을 하나 소개합니다.
```java
@Service
public class AsyncService {
    
    @Async("videoAsyncExecutor")
    public void videoAsyncRun(Runnable runnable){
        runnable.run();
    }

}

@Service
@RequiredArgsConstructor
public class videoService {
    private final AsyncService asyncService;
  
    // asyncService를 이용하여 메서드에 비동기처리를 합니다.
    public void runVideoConvert(Video video){
        asyncService.vimeoAsyncRun(() -> this.videoConvert(video));
    }
    
    // 비동기 적용 메서드
    public void videoConvert(Video video){
      // 비디오 변환로직 및 API 호출로직
    }
}
```
AsyncService를 하나 만들고 해당 서비스는 Runnable를 파라미터로 받아 실행만 해주는 메서드를 선언하여줍니다.\
그리고 비동기로 실행돼야 할 메서드를 파라미터로 넘겨서 호출합니다.
