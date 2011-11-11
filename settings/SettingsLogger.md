# Configuring logs

## Configuration logging level in application.conf

The easiest way to configure the logging level is to use the `logger` key in your `conf/application.conf` file.

Play defines a default `application` logger for your application, that it is automatically used when you use the default `Logger` operations.

```properties
# Root logger:
logger=ERROR

# Logger used by the framework:
logger.play=INFO

# Logger provided to your application:
logger.application=DEBUG
```

The root logger configuration affects all log call, rather than requiring custom logging levels. Additionally, if you want to enable the logging level for a specific library, you can specify it here. For example to enable `TRACE` log level for spring framework, you could add:

```properties
logger.org.springframework=TRACE
```

## Configuring logback

Play 2.0 uses [[logback | http://logback.qos.ch/]] as its logger engine. The default is defining two appenders, one dispatched to the standard out stream, and the other into the `logs/application.log` file.

If you want to fully customize logback, just define a `conf/logger.xml` configuration file. Here is the default configuration file used by Play:

```xml
<configuration>
    
  <conversionRule conversionWord="coloredLevel" converterClass="play.api.Logger$ColoredLevel" />
  
  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
     <file>${application.home}/logs/application.log</file>
     <encoder>
       <pattern>%date - [%level] - from %logger in %thread %n%message%n%xException%n</pattern>
     </encoder>
   </appender>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%coloredLevel %logger{15} - %message%n%xException{5}</pattern>
    </encoder>
  </appender>
  
  <logger name="play" level="INFO" />
  <logger name="application" level="INFO" />

  <root level="ERROR">
    <appender-ref ref="STDOUT" />
    <appender-ref ref="FILE" />
  </root>
  
</configuration>
```