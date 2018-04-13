# Kafka安装
## Kafka单机安装
**基于CentOS7搭建Kafka单机环境**

**安装准备**
[1] 更新CentOS 7的系统<br>
[root@localhost ~]# yum update -y<br>
[root@localhost ~]# reboot<br>

[2] 安装JDK8<br>
略<br>
注意：需要设置JAVA_HOME和JRE_HOME<br>


**第一步：下载Kafka**<br>
[1] 下载<br>
[root@localhost ~]# wget http://mirror.bit.edu.cn/apache/kafka/1.1.0/kafka_2.11-1.1.0.tgz<br>

[2] 解压<br>
[root@localhost ~]# tar zxf kafka_2.11-1.1.0.tgz<br>

[3] 移到/opt目录<br>
[root@localhost ~]# mv kafka_2.11-1.1.0 /opt/<br>

**第二步：启动Kafka**<br>
[1] 进入kafka目录<br>
[root@localhost ~]# cd /opt/kafka_2.11-1.1.0/<br>

[2] 启动zookeeper<br>
[root@localhost kafka_2.11-1.1.0]# bin/zookeeper-server-start.sh -daemon config/zookeeper.properties<br>

[3] 检查zookeeper端口2181是否正常监听<br>
[root@localhost kafka_2.11-1.1.0]# netstat -an|grep 2181<br>
tcp6       0      0 :::2181                 :::*                    LISTEN     <br>

[4] 检查kafka默认的JVM参数配置是否需要修改<br>
Kafka默认设置1G，即"-Xmx1G -Xms1G"。如果你的测试机内存较低，需要修改才能成功启动。参数配置位于：bin/kafka-server-start.sh<br>

[5] 启动Kafka<br>
[root@localhost kafka_2.11-1.1.0]# bin/kafka-server-start.sh config/server.properties<br>

如果一切顺利，就会看到如下启动成功的日志：<br>
[2018-04-13 10:29:16,041] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)<br>

[6] 检查Kafka的端口9092监听是否正常<br>
[root@localhost ~]# netstat -an|grep 9092<br>
tcp6       0      0 :::9092                 :::*                    LISTEN<br>

**第三步：测试Kafka**<br>
[1] 创建一个测试主题<br>
[root@localhost ~]# cd /opt/kafka_2.11-1.1.0/<br>
[root@localhost kafka_2.11-1.1.0]# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".<br>

[2] 查看刚刚创建的test主题<br>
[root@localhost kafka_2.11-1.1.0]# bin/kafka-topics.sh --list --zookeeper localhost:2181<br>
test<br>

[3] 向刚刚创建的test主题中写入数据<br>
[root@localhost kafka_2.11-1.1.0]# bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test<br>
>Hello, Dennis!<br>

"Hello Dennis!"为写入的数据。<br>

[4] 查看刚刚写入的数据<br>
另外开一个ssh tab连接，然后执行如下命令<br>
[root@localhost kafka_2.11-1.1.0]# bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning<br>
Using the ConsoleConsumer with old consumer is deprecated and will be removed in a future major release. Consider using the new consumer by passing [bootstrap-server] instead of [zookeeper].<br>
Hello, Dennis!<br>

此外，还可以在上一个ssh连接实时写入数据，查看的ssh里可以看到实时的输出。<br>


**参考资料：**<br>
[1] https://www.vultr.com/docs/how-to-install-apache-kafka-on-centos-7<br>

## Kafka集群安装
