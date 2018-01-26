# Channel Handler

### 功能

Channel Handler的功能, 如在javadoc中所说:

> 处理I/O事件或者拦截I/O操作, 并转发给它所在ChannelPipeline中的下一个handler.

### 子类型 {#subtype}

ChannelHandler本身并不提供很多方法, 但是通常需要实现它的子类型之一:

* ChannelInboundHandler 用于处理inbound I/O事件
* ChannelOutboundHandler 用于处理outbound I/O事件

或者, 为了方便使用, 提供了下面的adapter 类:

* ChannelInboundHandlerAdapter 用于处理inbound I/O事件
* ChannelOutboundHandlerAdapter 用于处理outbound I/O事件
* ChannelDuplexHandler 用于同时处理 inbound 和 outbound事件



