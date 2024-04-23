1.rabbitmq好处：解耦，异步，削封

2.vhost作用：作为独立的权限管理系统，用于用户控制

3.消息队列如何持久化：①、将queue的持久化标识durable设置为true,则代表是一个持久的队列 ②、发送消息的时候将deliveryMode=2

