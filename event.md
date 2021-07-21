deals with subscriptions to real-time events.

event是一个订阅与发布模块。需求是耦合，设计/实现是解耦。

> 相对比较独立的工具/库。

流程：

* 订阅某主题 - Start a subscriber.
* 向某主题发布消息 - Post some events.
* 停止订阅 - Stop closes all subscription channels.
* 等待通知 - Wait for subscriber to return.



