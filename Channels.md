# Channels

## Channel简介
Channel 是netty中提供的对网络操作的抽象类,类似于NIO中的Channel \(java.nio.SocketChannel/java.nio.ServerSocketChannel\). Netty的javadoc中时这样描述的:

> A nexus to a network socket or a component which is capable of I/O operations such as read, write, connect, and bind.**对网络套接字或者有I/O操作能力\(例如读,写,连接和绑定\)的模块的关联\(抽象?\)**

channel为使用者提供的功能:
* channel当前的状态\(例如是不是打开? 是不是连接上了?\)
* channel的配置参数\(例如接收缓冲区大小\)
* 提供I/O操作\(例如读,写,连接和绑定\)
* 提供ChannelPipeline,用于处理所有的I/O事件和关联到channel的请求

### 注意事项

netty channel的一些注意事项:

1. 所有的I/O操作都是异步的

   netty中所有的I/O操作都是异步的. 这意味着任何I/O调用都将立即返回而不保证请求的I/O操作在调用结束时已经完成. 替代的是,你将得到一个返回的**ChannelFuture**实例, 当请求的I/O操作成功,失败或者取消时将通知你.

2. channel是有等级的\(hierarchical\)

   channel可以有一个parent, 取决于它是如何创建的. 例如, SocketChannel 是 ServerSocketChannel 在接受连接时创建的. SocketChannel 的parent\(\)方法就会返回这个ServerSocketChannel.

   等级结构的语义取决于channel所属的**transport**的实现. 例如, 可以写一个新的Channel实现, 创建子channel用于分享一个socket连接, 像BEEP 和 SSH 那样.

3. 向下溯型来访问传输特有\(transport-specific\)操作

   某些transport 暴露transport特有的一些额外的操作. 将Channel向下溯型为子类型来调用这些操作. 例如, 对于老的I/O datagram transport, DatagramChannel 提供了 multicast join / leave 操作.

4. 释放资源

   有一个非常重要的事情, 当你完成channel的处理之后, 需要调用close\(\) 或 close\(ChannelPromise\) 方法来释放所有的资源. 这样可以确保所有的资源以正确的方式被释放, 例如文件操作等.

## Channle Future {#channel-future}
Netty的所有操作都是I/O操作都是异步的，我们不能立即知道操作的结果，所以需要一种在将来的某个时间点获取操作结果的办法
> 可以将 ChannelFuture 看作是将来要执行的操作的结果的 占位符。它究竟什么时候被执行则可能取决于若干的因素，因此不可能准确地预测，但是可以肯 定的是它将会被执行。此外，所有属于同一个 Channel 的操作都被保证其将以它们被调用的顺序 被执行。


## Channel Handler 和 Channel Pipline {#channel-handler-and-channel-piplin}

### 功能

Channel Handler的功能, 如在javadoc中所说:

> 处理I/O事件或者拦截I/O操作, 并转发给它所在ChannelPipeline中的下一个handler.

事实上， ChannelHandler 可专 门用于几乎任何类型的动作，例如将数据从一种格式转换为另外一种格式，或者处理转换过程 中所抛出的异常。

### ChannelPipline
ChannelPipline是ChannelHandler的容器，并定义了入站和出站事件处理的顺序
```uml
@startuml
skinparam ParticipantPadding 20
skinparam BoxPadding 10

participant "sokcet/tranport" as s
box "ChannelPipeline"
participant ChannelInboudHandler1 as in1
participant ChannelInboudHandler2 as in2
participant ChannelOutboudHandler1 as out1
participant ChannelOutboudHandler2 as out2
end box
s --> in1 :
in1 --> in2:
out2 -> out1:
out1 -> s:

@enduml
```


