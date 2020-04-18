#### AMQP核心概念

1. **Server:** 又叫做Broker,接收客户端连接的，实现AMQP实体服务
2. **Connection:**连接，应用程序与Broker的网络连接
3. **Channel:**网络信道，几乎所有的操作都是在Chanel中进行，是消息进行读写的通道，客户端可以建立多个chanel，每个chanel代表一个会话服务
4. **Message:**消息，服务器和应用程序之间传输的数据，由Properties和Body组成。Properties可以对消息进行修饰，比如消息的优先级，延迟等高级特性，Body则就是消息体内容
5. **VirtualHost**:虚拟主机，由于进行逻辑上的隔离，最上层的消息路由，一个VirtualHost里面可以有若干个Exchange和Queue。同一个Virtual Host里面不能有名称相同的Exchange和Queue
6. **Exchange:**交换机，接收消息，根据路由键将对应的信息转发到对应的绑定队列
7. **Binding：**Exchange和Queue之间的虚拟连接
8. **Routing key**：路由规则，虚拟机用它来确定如何路由一个特定的消息
9. **Queue**:保存信息，并将他转发给消费者

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/data/TIM%E6%88%AA%E5%9B%BE20200405141356.png)





#### RabbitMQ

 **provider使用默认的Exchange**

​          provider只要负责将数据发往exchanger并指定routingKey,comsumer负责创建队列和监听队列，

在MQ中要先开启comsumer，由comsumer创建队列后，exchanger才能根据routingKey将数据发往队列

**provider使用指定的Exchange**

​     provider要声明Exchange的名字和类型、comsumer要声明相同的Exchange的名字和类型并绑定到某一体队列，这样才能将provider的数据发往指定的队列中



#### Exchange

1. **Direct Exchange**:所有发送到该交换机的消息将会被转发到RouteKey指定的Queue中

   ​      Direct模式可以使用RabbitMQ自带的Default Exchange ,所以不需要进行任何的binding操作。消息传递时，RouteKey必须完全匹配才能被队列接收，否则会被丢弃

2. **Topic Exchange:**所有发送到Topic Exchange的消息被转发到所有关心RouteKey中指定Topic的Queue中



#### 如何保证消息的可靠性投递

保证消息的成功发出，保障MQ节点的成功接受，发送方收到MQ节点的确认应答  完善的消息进行补偿机制

**解决方案**

1.  

   ​     ![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/data/TIM%E6%88%AA%E5%9B%BE20200406085820.png)

​         在发送数据之间将数据保存一份到数据库中，并设立标识位为0，发送后如果接收到接收方的响应就把数据库中对应的记录的标识位改为1，使用分布式定时任务，每隔一段时间检查数据库中那些依然是0的数据进行重新发送（设立一个最大的重试次数）

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/confirm1.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/confirm2.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/return1.png)



![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/return2.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E6%B6%88%E8%B4%B9%E7%AB%AF%E9%99%90%E6%B5%812.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E9%99%90%E6%B5%81.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E9%87%8D%E5%9B%9E%E9%98%9F%E5%88%97.png)



![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E6%AD%BB%E4%BF%A1%E9%98%9F%E5%88%97.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E6%AD%BB%E4%BF%A1%E9%98%9F%E5%88%972.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/mq/%E6%AD%BB%E4%BF%A1%E9%98%9F%E5%88%973.png)











rabbitMQ生产者将数据放入队列后消费者开启之后会一直监听生产者，所以不能将Demo至于单元测试中使用

队列的持久化是保存队列，想要队列和队列的数据一起持久化那么要进行额外设置