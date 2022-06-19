---
sidebar:
nav: "main"
layout: single title:  "첫 오픈소스 회고록"
categories: [Programming,Dev-diary]
tags: [Programming,Dev-diary,Java,Kotlin]
teaser: /assets/images/teaser/java.jpeg
---

<p align="center"><img src="/assets/images/teaser/git.png" width="100%" height="auto"></p>

https://github.com/sirloin-dev/mtrace-api-client

회사 업무로 공공 오픈API를 사용할 일이 있었다. 오픈 API임에도 오픈소스로 Api Client가 만들어져있지 않아 내가 만들기로 했다. \
오픈소스로 만든다며 좀 헤메서 생각보다 일정이 더 걸렷는데 감안 해준 회사에 감사하다. \
이번 포스팅은 오픈 소스 API를 만들며 느낀점을 회고해보는 시간이다. \
더불어 처음에 코틀린으로 만들다 자바8로 포팅하였는데 정말 불편한점이 많았다 이부분에 대한 내용도 들어갈것이다.

Open Source
---

> 오픈소스란 원래 오픈소스 소프트웨어(Open Source Software, OSS)를 뜻하는 용어입니다. 오픈소스 소프트웨어는 공개적으로 액세스할 수 있게 설계되어 누구나 자유롭게 확인, 수정, 배포할 수 있는 코드입니다.
>
> [출처 : 레드핫 whit-is-open-source](https://www.redhat.com/ko/topics/open-source/what-is-open-source)

말 그대로 오픈소스는 누구나 공개적으로 사용할수 있는 소프트웨어이다.\
이러한 특성덕분에 코드퀄리티에 더 신경을 써야했다. 그리고 코드외에 신경써야할것도 많았다. \
먼저 해당 프로젝트를 그레이들로 추가해서 사용할 경우가 대부분이라 판단되어 외부 라이브러리 사용을 하지않았다. \
라이브러리 중복 import로 인한 오류를 프로젝트 사용자한테서 원천적으로 방지하고 싶었기 때문이다. \
그래서 자바의 기본기능을 최대한 활용하였다. 당연히 http클라이언트 외부라이브러리, xml 파싱라이브러를 사용 할수 없었다. \
(자바의 java.net의 HttpClient는 자바 11에서 추가되었다.) \
평소의 개발환경이랑 제일 크게 다른점중 하나였다. 평소엔 다른 개발자분들이 잘 만든 라이브러리를 사용만 하면되었지만 이번엔 아니었다. \

코드퀄리티 부분으론 java pmd와 checkstyle을 적용하여서 개발하였는데 다행히 몇개 잡히지 않았는데 3가지가 가장 기억에 남는다. \

1. final 키워드다. \
   checkstyle에서는 파라미터로 넘기는 모든 인자에 final을 붙히도록 하고있다. \
   메서드 내부에서 재할당을 못하게 막기 위함으로 알고있는데 불변성을 유지하기 위한 자바의 코딩스타일로 알고있다. \
2. package-info.java 이다. \ 인텔리제이 자바 프로젝트에서 패키지에 들어가 new로 생성할려하면 보던거인데 이게 왜쓰이나 했는데 주석작성용 파일인것같다. \
3. javadoc 에러 checkstyle error중 가장 골치아픈게 javadoc에러였다. \
   public이면 모든 부분에 javadoc을 달아야했다. \
   또한 내부 DTO용 클래스의 필드를 private로 숨겨도 내부 필드에는 달아야했다. \
   이게 외부라이브러리를 못쓰는 부분하고 시너지를 만들었는데 내부의 정보를 담고있는 DTO를 하나 만들었다 가정을 하나 해보겠습니다.

```java
// javadoc 한번
public class anyDTO() {

  // javadoc 두번
  private String name;

  private int age;

  // javadoc 세번
  public String getName() {
    return name;
  }

  public int getAge() {
    return age;
  }
}
```

필드에 하나 필드에 접근하기위한 getter에 하나 javadoc 필드갯수 * 2만큼 작성해야했다. \
개발하는 도중엔 굉장히 스트레스 받은 부분이다.(똑같은 설명 또 적어야하는 이 불편함...)
그래도 checkstyle에서 정해놓은 규칙에 다 따라서 하니 결과물은 굉장히 좋았다. 특히 회사 다른 동료분들이 주석이 상세해 보기 편하다는 말을 하였고 내부 소스코드에 접근하기전 주석만 봐도 어떤일 하는지
알수있어 좋았다하였다.\
추후 업무에서 프로젝트를 열었을때 주석이 상세히 잘 작성되어있어 고생한 보람은 있엇다.

Java와 Kotlin
---
이제는 Kotlin -> java로 바꾸며 느꼇던 불편한점에 대해 좀 기술해 볼려한다.\
일단 코틀린의 val과 null safety는 정말 소중하단걸 깨달았다. \
.?의 문법을 이용한 null check는 진짜 코드를 간결하며 null에 안정적이게 만들어준다. \
(자바에서 저게 안되서 정말 코드가 길어졋다. 개발을 하며 java로 못돌아갈거같단 느낌이 든 이유다)

그러나 제일 크게 java와 Kotlin을 사용하며 느낀점은 코드의 어순의 차이이다.\
어떤 말인지 이해가 잘 안갈것같아 이부분은 코드와 함께 설명하겠다. \ 먼저 코틀린에는 확장함수라는 개념이 있다 일명 Extension Function

```Kotlin
  private fun List<Restaurant>.randomPickOne(): Restaurant = this.shuffled().first()
```

위의 코드와 같이 클래스를 확장하는 함수를 하나 만들어 선언하여 추가적인 기능을 구현한 코드이다.\
그래서 코틀린에서는 다음과 같은 문법이 가능해진다.

```Kotlin
    fun recommendRestaurant(
        address: Address
    ): Restaurant {
         // 레스토랑 api 콜해 가져오고 . Random으로 1개를 뽑는다.
         return getRestaurantCallApi(address).randomPickOne() 
    }
```

```java
  public Restaurant recommendRestaurant(final Address address){
    // 읽게되면 1개를 뽑는다 . 레스토랑api를 콜한것
    return randomPickOne(getRestaurantCallApi(address))
  }
```
아래 자바코드 보다 코틀린의 코드가 훨신더 보기 좋다 생각된다\
즉 코드가 좀더 자연어에 가까운 표현력을 가능하게 해준다\
이부분이 생각보다 생산성에 영향을 많이 주었다. \
코틀린은 정보를 꺼내쓰는 느낌으로 코딩이 되지만 \
자바는 결과물을 정해놓고 맞춰가는 식의 코딩을 하게 되었다.

결론은 코틀린이 짱이라는것이다.




