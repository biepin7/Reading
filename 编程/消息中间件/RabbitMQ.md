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

