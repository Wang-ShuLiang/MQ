## 一、准备环境
1.1 安装文件：http://www.oracle.com/technetwork/java/javase/downloads/index.html 下载JDK<br/>
1.2 安装完成后需要添加以下的环境变量（右键点击“我的电脑” -> "高级系统设置" -> "环境变量" ）：<br/>
JAVA_HOME: C:\Program Files\Java\jdk1.8.0_171 (jdk的安装路径)<br/>
Path: 在现有的值后面添加"; %JAVA_HOME%\bin"<br/>
1.3 打开cmd运行 "java -version" 查看当前系统Java的版本。

## 二、安装ZOOKEEPER
2.1 下载安装文件： http://zookeeper.apache.org/releases.html 下载zookeeper-3.4.14<br/>
2.2 解压文件 <br/>
2.3 打开zookeeper-3.4.13\conf，把zoo_sample.cfg重命名成zoo.cfg<br/>
2.4 从文本编辑器里打开zoo.cfg   把dataDir的值改成“./zookeeper-3.4.13/data”<br/>
2.5添加如下系统变量：<br/>
ZOOKEEPER_HOME: C:\Users\localadmin\CODE\zookeeper-3.4.13 (zookeeper目录)<br/>
Path: 在现有的值后面添加 ";%ZOOKEEPER_HOME%\bin;"<br/>
2.6 运行Zookeeper: 打开cmd然后执行 zkserver<br/>
cmd 窗口不要关闭<br/>
## 三、安装kafka
3.1 下载安装文件： http://kafka.apache.org/downloads.html<br/>
3.2 解压文件<br/>
3.3 打开kafka_2.11-2.0.0\config<br/>
3.4 从文本编辑器里打开 server.properties<br/>
3.5 把 log.dirs的值改成 “./logs”<br/>
3.6 打开cmd<br/>
3.7 进入kafka文件目录: cd C:\Users\localadmin\CODE\kafka_2.11-2.0.0(kafka目录)<br/>
3.8 输入并执行:  .\bin\windows\kafka-server-start.bat .\config\server.properties<br/>
cmd 窗口不要关闭<br/>
## 四、创建一个主题
4.1 打开cmd 并进入cd C:\Users\localadmin\CODE\kafka_2.11-2.0.0\bin\windows<br/>
4.2 创建一个topic： kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test<br/>
## 五、创建生产者
cd C:\Users\localadmin\CODE\kafka_2.11-2.0.0\bin\windows<br/>
kafka-console-producer.bat --broker-list localhost:9092 --topic test<br/>
## 六、创建消费者
cd C:\Users\localadmin\CODE\kafka_2.11-2.0.0\bin\windows<br/>
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning<br/>
