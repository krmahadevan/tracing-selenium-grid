# tracing-selenium-grid

TL;DR: A simple project to showcase Selenium 4's Tracing capability using OpenTelemetry APIs(Jaeger) with Selenium Grid.

## Observability 
> *It is a measure of how well internal states of a system can be inferred from knowledge of its external outputs. It helps bring visibility into systems.* – Wikipedia 

Tracing is one of the important pillars for measuring observability along with logs and metrics. Its a way of thinking about the system, a way of being able to ask the system questions, that you need the answer to when you didn't know the questions beforehand. On the other hand, alerting is a way of asking questions when you do know the question beforehand.

Distributed Tracing can be quite helpful in answering questions for your distibuted system like,

* Root cause analysis
* Performance optimisation
* dependency analysis of services

## Selenium-4 
It has number of new exciting features and one such is a cleaner code for Selenium Grid along with support for distributed tracing via OpenTelemetry APIs. This is pretty exciting and important feature for the admins and devops engineers to trace the flow of control through the Grid for each and every command.

Distributed tracing has two parts:

1. Code instrumentation: Adding instrumentation code in your application to produce traces. The major instrumentation parts are done neatly by the developers of Selenium project, which leaves us to consume it by using Selenium Grid start-up command along with Jaeger.

2. Collection of data and providing meaning over it, Jaeger has a UI which allows us to view traces visually well.

Steps to start tracing your tests,

* Start Jaeger via docker(as its easy)

* Instrument your Selenium Grid start-up command with Jaeger tracing as in start-grid.sh and start-grid-standalone.sh for standalone mode.

* Now execute your tests tests, and navigate to `http://localhost:16686/` to view the outputs.

## Start Jaeger via docker server
The simplest way to start the all-in-one is to use the pre-built image published to DockerHub (a single command line).

```
$ docker run --rm -it --name jaeger \
    -p 16686:16686 \
    -p 14250:14250 \
    jaegertracing/all-in-one:1.17
  ```
  You can then navigate to http://localhost:16686 to access the Jaeger UI.

## Instrument your Selenium Grid
We're going to add support for Open Telemetry API's(one of the many ways to do distributed tracing) using Coursier

The selenium server will inform you that it has found a tracer on
stdout.
```
  java -DJAEGER_SERVICE_NAME="selenium-standalone" \
       -DJAEGER_AGENT_HOST=localhost \
       -DJAEGER_AGENT_PORT=14250 \
       -jar selenium.jar \
       --ext $(coursier fetch -p \
          io.opentelemetry:opentelemetry-exporters-jaeger:0.2.4 \
          io.grpc:grpc-okhttp:1.26.0) \
       standalone
```
```curl http://locahost:4444/status``` to check if your grid deployment is ready

![Grid status](/images/grid-ready.png)

## Execute your tests
Point your tests as in tracingexample.java and navigate to http://localhost:16686/ to view the outputs.

Under services look up for Selenium-router and notices actions for each calls made by your tests. An example below

![Traces](/images/route-traces.png)

References:
* [Tracing Info command](https://github.com/SeleniumHQ/selenium/blob/master/java/server/src/org/openqa/selenium/grid/commands/tracing.txt)
* [Jaeger guide](https://www.jaegertracing.io/docs/1.17/getting-started/)

A special thanks to @shs96c Simon Stewart for being patience in answering my dumb questions.
