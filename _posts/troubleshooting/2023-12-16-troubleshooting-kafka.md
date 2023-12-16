---
title: "[Kafka] Exception in thread 'main' joptsimple.UnrecognizedOptionException: zookeeper is not a recognized option"
date: 2023-12-16 18:00:00 +09:00
categories: [IT, Kafka, TroubleShooting]
tags: [TIL, kafka, TroubleShooting]
image: /assets/img/posts/trouble-shooting.png
---

## 발생 원인
Kafka에서 토픽을 생성하던 도중 
`.\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factore 1 --partitions 1 --topic testv` 아래와 같은 에러가 생겼다.

```sh
Exception in thread "main" joptsimple.UnrecognizedOptionException: replication-factore is not a recognized option
        at joptsimple.OptionException.unrecognizedOption(OptionException.java:108)
        at joptsimple.OptionParser.handleLongOptionToken(OptionParser.java:510)
        at joptsimple.OptionParserState$2.handleArgument(OptionParserState.java:56)
        at joptsimple.OptionParser.parse(OptionParser.java:396)
        at kafka.admin.TopicCommand$TopicCommandOptions.<init>(TopicCommand.scala:558)
        at kafka.admin.TopicCommand$.main(TopicCommand.scala:49)
        at kafka.admin.TopicCommand.main(TopicCommand.scala)
```

<br/>

정말 별거아닌 에러인데 시간이 너무 투자되었다.

## 해결

구글링을 통해 답을 찾았다.
`.\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic testv`     

위와 같이 명령어를 변경해줘야한다고 한다. 

<br/>

Kafka는 CLI명령이 거의 사용되지 않는다고한다. 공식문서를 읽고 사용하도록 하고, 블로그에서 사용하는 명령어는 되도록이면 사용하지 말아야한다.

## 참고

+ [스택오버플로우](https://stackoverflow.com/questions/69297020/exception-in-thread-main-joptsimple-unrecognizedoptionexception-zookeeper-is)
+ [공식문서](https://kafka.apache.org/30/documentation.html#quickstart)

