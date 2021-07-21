deals with subscriptions to real-time events.

event是一个订阅与发布模块。需求/业务是耦合的，但设计/实现可以解耦。

> 不直接引入、直接调用即为解耦。

流程：

* 订阅某主题 - Start a subscriber.
* 向某主题发布消息 - Post some events.
* 停止订阅 - Stop closes all subscription channels.
* 等待通知 - Wait for subscriber to return.

以太坊的使用：

* event.Feed 实例对象发布消息

* event.Subscription 实例对象订阅消息
* 其它，如 SubscriptionScope，TypeMuxSubscription



> 相对比较独立的工具/库。



