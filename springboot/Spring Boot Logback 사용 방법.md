# logback

## appender

log의 형태 및 어디에 출력할지 설정하기 위한 영역

대포적인 Appender 형식은 아래와 같음

- ConsoleAppender : 콘솔에 로그를 출력
- FileAppender : 파일에 로그를 저장
- RollingFileAppender : 여러 개의 파일을 순회하며 로그를 저장
- SMTPAppender : 로그를 메일로 보냄
- DBAppender : 데이터베이스에 로그를 저장

보통 RollingFileAppender 를 가장 많이 사용하게 됩니다.
RollingFileAppender 을 사용하면 예를들어 각 log level 별로 파일을 저장할 수 있습니다.

error log 따로, warn log 따로, info log 따로.. 등

### Filter

임계 값(log level) 보다 낮은 level은 거부하겠다 라는 설정을 지정해준다.

```
<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
    <level>INFO</level>
</filter>
```

위와 같은 설정을 하게 되면 log level이 info 보다 낮은 (여기서 낮다는 말은 중요도가 낮다는 말) trace, debug 등은 출력 및 기록되지 않습니다.

### File

출력된 로그를 어떤 경로에 어떠한 파일 이름으로 저장을 할 것인지를 설정합니다.

```
<file>${logdir}/${moduleId}/${logback}/info_${type}.log</file>
```

### append

뒤에 붙이는 의미 (? 이게 뭔 왈왈소린가)

```
<append>true</append>
```

### rollingPolicy

```
<appender name="INFO_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
```

rollingPolicy 는 위와 같이 RollingFileAppender 를 사용할 경우 지정할 수 있으며
fileNamePattern 을 통하여 주기적으로 생성될 압축 파일의 경로와 이름을 지정할 수 있으며 maxHistory를 통하여 로그파일의 저장기간을 지정할 수 있습니다.

```
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    <fileNamePattern>${logdir}/${moduleId}/${logback}/info_${}type.%d{yyyy-MM-dd}.gz</fileNamePattern>
    <maxHistory>30</maxHistory>
</rollingPolicy>
```

### encoder

pattern을 사용하여 원하는 형식으로 로그를 표현할 수 있습니다.

```
<encoder>
    <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}][%-5level][%thread]%logger%msg%n</pattern>
</encoder>
```

### root

설정한 appender를 참조하여 로그의 레벨을 설정할 수 있습니다.
root는 전역 설정이며, 지역 설정을 하기 위해서는 logger를 사용하면됩니다.


appender-ref ref="APPENDER NAME"
을 통하여 appender를 참조할 수 있습니다.

```
<root level="DEBUG">
    <appender-ref ref="INFO_LOG" />
</root>
```

