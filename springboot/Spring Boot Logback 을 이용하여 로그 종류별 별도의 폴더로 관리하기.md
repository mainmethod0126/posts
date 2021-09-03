Spring Boot 프로젝트를 진행하면서 모든 종류의 로그(info, error, warning)를 하나의 파일로 관리를 하였더니 로그 보기가 너무 번거롭기에 이를 각 폴더별로 분리하여 관리하는 방법을 찾아보고 그 기록을 남깁니다.

# Logback 이란?
자바 오픈소스 로깅 프레임워크이며 SLF4J 의 구현체이며 Spring Boot에서는 기본으로 탑재됩니다.

# Spring Boot Logback 설정 파일 참조 순서
1. classpath(resource 디렉토리 밑)에 **logback-spring.xml** 파일이 있으면 설정파일을 읽어갑니다.
2. logback-spring.xml 파일이 없다면 **.yml(.properties)** 파일의 설정을 읽어갑니다.
3. logback-spring.xml 파일과 **.yml(.properties)** 파일이 동시에 있으면 **.yml(.properties)** 설정 파일을 적용 후 xml 파일이 적용됩니다


# 로그 레벨

로그에서 설정할 수 있는 레벨은 다섯 종류 입니다.

좌측 부터 큰 범위입니다

~~~
TRACE > DEBUG > INFO > WARN > ERROR
~~~

1. **TRACE** : 가장 상세한 정보를 나타내며, 그냥 찍을 수 있는 내용중 웬만한건 다 찍힌다고 보면 됩니다.
2. **DEBUG** : 프로그램 디버깅을 위한 정보를 표시합니다
3. **INFO** : 상태변경과 같은 정보성 로그를 표시합니다.
4. **WARN** : 처리 가능한 문제, 향후 시스템 에러의 원인이 될 수 있는 경고성 메세지를 나타냅니다.
5. **ERROR** : 요청을 처리하는 중 요류가 발생한 경우를 표시합니다.

예를들어 로그 설정 레벨을 INFO로 하였다면 TRACE와 DEBUG 는 무시되고 INFO, WARN, ERROR 로그만이 출력됩니다.


# Logback-spring.xml을 통한 설정

## 기본 특징
- **대소문자 구별하지 않습니다**.
- **name attribute를 반드시 지정해야합니다**.
- **appender**와 **logger** 두개로 구분됩니다.
- **dynamic reloading** 기능을 지원됩니다. (프로그램 재시작 필요 없음)

~~~xml
<?xml version="1.0" encoding="UTF-8"?> 
<!-- 60초마다 설정 파일의 변경을 확인 하여 변경시 갱신 -->
<configuration scan="true" scanPeriod="60 seconds">
  로그백 설정 부분 
</configuration>
~~~

## appender
log의 형태를 설정하며, 로그 메시지를 콘솔에 출력할지, 파일에 출력할지 등의 설정을 지정할 수 있습니다.

### appender의 class종류
1) **ch.qos.logback.core.ConsoleAppender** : 콘솔에 로그를 찍음, 로그를 OutputStream에 작성하여 콘솔에 출력되도록 한다.
2) **ch.qos.logback.core.FileAppender** : 파일에 로그를 찍음, 최대 보관 일 수 등를 지정할 수 있다.
3) **ch.qos.logback.core.rolling.RollingFileAppender** : 여러개의 파일을 롤링, 순회하면서 로그를 찍는다.(FileAppender를 상속 받는다. 지정 용량이 넘어간 Log File을 넘버링 하여 나누어 저장할 수 있다.)
4) **ch.qos.logback.classic.net.SMTPAppender** : 로그를 메일에 찍어 보낸다.
5) **ch.qos.logback.classic.db.DBAppender** : DB(데이터베이스)에 로그를 찍는다. 







# 참고 자료
https://goddaehee.tistory.com/206
