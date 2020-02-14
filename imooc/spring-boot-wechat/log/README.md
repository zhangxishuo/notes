# 日志

## 日志框架的能力

- 定制输出目标
- 定制输出格式
- 携带上下文信息
- 运行时选择性输出
- 灵活的配置
- 优异的性能

## 技术选型

日志接口选择`Slf4j`，日志实现选择`LogBack`。

## 基本使用

```java
private static final Logger logger = LoggerFactory.getLogger(Demo.class);
```

```java
logger.info("name: {}, password: {}", name, password);
```

## 日志级别

```java
package org.slf4j.event;

public enum Level {
    ERROR(40, "ERROR"),
    WARN(30, "WARN"),
    INFO(20, "INFO"),
    DEBUG(10, "DEBUG"),
    TRACE(00, "TRACE");
}
```

## LogBack 配置

### application.yml

配置日志文件路径，生成`spring.log`日志文件：

```yml
logging:
  path: /var/log/tomcat/
```

自定义日志文件：

```yml
logging:
  file: /var/log/tomcat/demo.log
```

自定义日志级别，默认为`info`：

```yml
logging:
  level: debug
```

### logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<configuration>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                %d - %msg%n
            </pattern>
        </layout>
    </appender>

    <appender name="fileInfoLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>DENY</onMatch>
            <onMismatch>ACCEPT</onMismatch>
        </filter>
        <encoder>
            <pattern>
                %msg%n
            </pattern>
        </encoder>
        <!--滚动策略-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--路径-->
            <fileNamePattern>/var/log/tomcat/sell/info.%d.log</fileNamePattern>
        </rollingPolicy>
    </appender>

    <appender name="fileErrorLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
        <encoder>
            <pattern>
                %msg%n
            </pattern>
        </encoder>
        <!--滚动策略-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--路径-->
            <fileNamePattern>/var/log/tomcat/sell/error.%d.log</fileNamePattern>
        </rollingPolicy>
    </appender>

    <logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE"/>

    <root level="info">
        <appender-ref ref="consoleLog" />
        <appender-ref ref="fileInfoLog" />
        <appender-ref ref="fileErrorLog" />
    </root>

</configuration>
```