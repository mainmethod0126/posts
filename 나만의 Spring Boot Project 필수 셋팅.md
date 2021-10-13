안녕하세요?
이번 포스팅은 제가 Spring Boot Project 진행 시 필수적으로 셋팅하는 몇가지들을 소개드리고자합니다.

</br>

## 1. 버전 자동화 (feat.시멘틱 버저닝)
프로젝트의 버전관리는 굉장히 중요한 요소중 하나입니다

</br>

이 소프트웨어가 어떠한 동작을 하는지, 과거의 버전과 무엇이 달라졌는지, 과거에 제공하던 기능을 아직도 제공하는지 아니면 이제는 제공을 안하는지 등이 구분되어야 합니다.

</br>

이러한 구분을 버저닝을 통하여 규칙성있게 관리할 수 있습니다.


### 방법
이전에 올렸던 게시글의 링크로 대체합니다
링크 : https://shinwusub.tistory.com/138?category=404917

</br>

## 2. REST API 문서 자동화 (feat.Swagger)
대부분의 Spring Boot 프로젝트는 Front-End 개발자와의 협업이 이루어집니다(혼자 다 하는 경우도 있습니다..)

</br>

이럴 경우 매번 백엔드의 API를 메일상으로 설명하거나 매번 수기로 작성된 REST API 문서를 열람하여 수정 후 전달하면 너무나도 번거롭습니다 이럴때 필요한 것이 **Swagger** 라는 프레임워크이며 이러한 REST API 문서를 몇가지의 어노테이션을 통하여 **WEB**으로 쉽게 확인할 수 있도록 만들어 줍니다.

</br>

### 방법
Swagger 버전에는 2.x과 3.x이 있는대 최신인 3.x 을 사용하였습니다.
추가적으로 상세한 정보는 아래 링크를 참고해주시면 감사하겠습니다.
링크 : https://www.baeldung.com/spring-rest-openapi-documentation

#### 1. gradle에 의존성 추가
~~~groovy
dependencies {
    // https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-ui
    implementation group: 'org.springdoc', name: 'springdoc-openapi-ui', version: '1.5.9'
}
~~~

</br>

#### 2. application.properties 또는 application~~.yml 수정
독자분의 프로젝트에 따라 Spring Boot 외부 설정을 주입하는 파일에 명시해주시면 되겠습니다.
저는 application-dev.yml 을 통하여 외부 설정을 주입하도록 하였습니다.

~~~yml
springdoc:
  default-consumes-media-type: application/json
  default-produces-media-type: application/json
  swagger-ui:
    operations-sorter: alpha
    tags-sorter: alpha
    path: /swagger-ui.html
    disable-swagger-default-url: true
    display-query-params-without-oauth2: true
~~~

</br>

#### 3. 동작 확인 
위 설정을 완료 후 gradle 프로젝트를 실행시킵니다.
그 다음 .yml파일에 명시하였던 path: /swagger-ui.html 정보를 참고하여
웹 브라우저를 통해 
~~~
http://IP:PROT/swagger-ui.html
~~~
으로 접속하여 Swagger가 만들어준 REST API 문서 페이지가 뜨는지 확인합니다.
**IP** : 서비스가 실행되는 PC IP 자신의 로컬 PC라면 localhost 사용 가능
**PORT** : Spring Boot 내장 톰캣(WAS)이 실행될 PORT

</br>

## 3. Log 범위별 별도의 파일 관리 (feat.Loggback)
보통 소프트웨어를 개발하다보면 코드가 길어지고 내부적으로 많은 로직이 들어갑니다.

</br>

이러한 소프트웨어서 오류가 발생하였을때 오류에대한 힌트가 없다면 개발자는 사막에서 바늘찾기를 해야합니다..

</br>

이럴때 Log 가 찍혀있고 그 Log 파일이 보기좋게 분리되어있다면 오류 해결하는데 상당히 큰 도움이 됩니다.

예를들어 current.log 라는 파일에 error,warn,info,trace등 모든 로그가 찍힌다면 단일파일의 용량도 많아질 뿐더러 내가 원하는 로그만 찾기가 쉽지 않을겁니다.

이럴때
error 로그는 error.log
warn 로그는 warn.log
info 로그는 info.log
와 같이 별도의 파일로 분리되면 훨씬 보기 편하겠죠

</br>

### 방법
이전에 올렸던 게시글의 링크로 대체합니다
링크 : https://shinwusub.tistory.com/137?category=404917


</br>

</br>


해당 포스팅에서 문제가 발생한다면 언제든지 댓글 부탁드리겠습니다.

감사합니다