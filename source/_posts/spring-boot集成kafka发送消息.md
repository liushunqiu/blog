---
title: spring-boot集成kafka发送消息
date: 2018-04-29 21:52:26
tags: 
	- Java
	- kafka
---
1.引入spring-kafka.jar文件
```
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>2.0.0.RELEASE</version>
</dependency>
```
2.在application.yml里面加入kafka配置
```
spring:
    kafka:
        bootstrap-servers: 192.168.1.90:9092
```
3.引入KafkaTemplate ，发送消息使用kafkaTemplate.send("test", "bootcwnao", JSON.toJSONString(data));

备注：
1.启动脚本
```
nohup bin/zookeeper-server-start.sh config/zookeeper.properties > zookeeper.log 2>&1 &
nohup bin/kafka-server-start.sh config/server.properties > kafka.log 2>&1 &
```
2.注意kafka是否开放了host,port端口
修改server.properties
```
advertised.listeners=PLAINTEXT://192.168.1.90:9092
```
2.查看消息
```
bin/kafka-console-consumer.sh --bootstrap-server 192.168.1.90:9092 --topic test  --from-beginning
```