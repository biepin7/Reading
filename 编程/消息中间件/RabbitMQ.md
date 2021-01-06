[TOC]

# 零 基础介绍 

RabbitMQ 是使用 Erlang 语言开发的开源消息队列系统，基于 AMQP 协议来实现。

AMQP的主要特征是**面向消息、队列、路由(包括点对点和发布/订阅)、可靠性、安全**。

AMQP协议更多用在企业系统内，对数据一致性、稳定性和可靠性要求很高的场景，对性能和吞吐量的要求还在其次。

![](https://gitee.com/biepin/imgurl/raw/master/20201215135113.png)



## AMQP

![](https://gitee.com/biepin/imgurl/raw/master/20201215135649.png)

**Server**: 又称Broker, 接受客户端的连接，实现AMQP实体服务

**Connection**: 连接，应用程序与Broker的网络连接

**Channel**:网络信道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道。客户端可建立多个Channel,每个Channel代表一个会话任务。

**Message**:消息，服务器和应用程序之间传送的数据，由Properties和Body组成。Properties可 以对消息进行修饰，比如消息的优先级、延迟等高级特性; Body则就是消息体内容。

**Virtual host**:虚拟地址，用于进行逻辑隔离，最上层的消息路由。一个Virtual Host里面可以有若干个Exchange和Queue，同一个Virtual Host里面不能有相同名称的Exchange或Queue

**Exchange**:交换机，接收消息，根据路由键转发消息到绑定的队列

**Binding**: Exchange和Queue之间的虚拟连接，binding中可以包含routing key

**Routing key**:一个路由规则，虚拟机可用它来确定如何路由一个特定消息

**Queue**:也称为Message Queue,消息队列，保存消息并将它们转发给消费者

![](https://gitee.com/biepin/imgurl/raw/master/20201215140723.png)

![](https://gitee.com/biepin/imgurl/raw/master/20201215140851.png)

发消息时，指定 Message 要发送到那一个指定的 Exchange 上，且带上 routing key 

## RabbitMQ的安装与使用

### 下载

```
wget www.rabbitmq.com/releases/erlang/erlang-18.3-1.el7.centos.x86_64.rpm
wget http://repo.iotti.biz/CentOS/7/x86_64/socat-1.7.3.2-5.el7.lux.x86_64.rpm
wget www.rabbitmq.com/releases/rabbitmq-server/v3.6.5/rabbitmq-server-3.6.5-1.noarch.rpm
```

### 安装

```
rpm -ivh erlang-18.3-1.el7.centos.x86_64.rpm 
rpm -ivh socat-1.7.3.2-1.1.el7.x86_64.rpm --force --nodeps
rpm -ivh rabbitmq-server-3.6.5-1.noarch.rpm 
```

> [root@bogon ~]# rpm -ivh socat-1.7.3.2-1.1.el7.x86_64.rpm 
> 警告：socat-1.7.3.2-1.1.el7.x86_64.rpm: 头V4 RSA/SHA1 Signature, 密钥 ID 87e360b8: NOKEY
> 错误：依赖检测失败：
>         tcp_wrappers 被 socat-1.7.3.2-1.1.el7.x86_64 需要
>
> 所以在后面加 ： --force --nodeps
>
> “nodeps的意思是忽视依赖关系。因为各个软件之间会有多多少少的联系。有了这两个设置选项就忽略了这些依赖关系，强制安装或者卸载”

### 修改

```
vi /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app
```

loopback_users 中的 <<"guest">>,只保留guest

### 服务启动和停止

启动

```
 rabbitmq-server start &
```

停止

```
 rabbitmqctl app_stop
```

插件启动

```
rabbitmq-plugins enable rabbitmq_management
```

### 命令行操作

```
rabbitmqctl stop_app 
rabbitmqctl start_app
rabbitmqctl status
```

```
rabbitmqctl add user username password
rabbitmqctl list users
rabbitmqctl delete user username
rabbitmqctl clear permissions -p vhostpath username
...
```

```
rabbitmqctl add_vhost vhostpath:创建虚拟主机
rabbitmqctl list vhosts:列出所有虚拟主机
rabbitmqctl list_ permissions -p vhostpath:列出虚拟主机上所有权限
rabbitmqctl delete_ vhost vhostpath:删除虚拟主机
```

```
rabbitmqctl list_queues:查看所有队列信息
rabbitmqctl -p vhostpath purge_ queue blue:清除队列里的消息
```

# 一 极速入门 - 消息生产与消费

### 步骤

1. ConnectionFactory:获取连接工厂
2. Connection：获取连接
3. Channel：数据通信信道，可接受/发送消息
4. Queue：具体的消息存储队列
5. Producer & Consumer 

# -1 官网 python 教程

## -1.1 Hello World ｜ 基础的建立连接 ｜  send and receive messages from a named queue

### 基础知识

 RabbitMQ is **a post box, a post office and a postman.**

***Producing*** means nothing more than sending. A program that sends messages is a ***producer*** 

*A **queue*** is the name for a post box which lives inside RabbitMQ. 

Although messages flow through RabbitMQ and your applications, they can **only be stored inside a *queue***.

消息只能存放在 queue 里

 A *queue* is only bound by the host's memory & disk limits, it's essentially a large **message buffer**. 

queue 本质是一个message buffer / 消息缓冲器

Many *producers* can send messages that go to one queue, and many *consumers* can try to receive data from one *queue*. 

*Consuming* has a similar meaning to receiving. A ***consumer*** is a program that mostly waits to receive messages

### Sending

以下是官网的`send.py`:

```
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()
```

其中 

```
# 建立与RabbitMQ服务器的连接
connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
```



> before sending we need to make sure the **recipient queue exists**

发送时要确认 接受者队列的存在

> If we send a message to non-existing location, RabbitMQ will just drop the message. 

如果我们将消息发送到不存在的位置，RabbitMQ只会删除该消息

```
# 创建一个队列 用来接受消息
channel.queue_declare(queue='hello')
```

**In RabbitMQ a message can never be sent directly to the queue, it always needs to go through an *exchange*.**



## -1.2 Work Queues | 

