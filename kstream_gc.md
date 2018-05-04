#  jvm gc导致的kstream状态异常
#### kstream的执行逻辑
+ kstream包含多干个kthread，每个kthread包含一个kafka consumer，kafka consumer会定期与服务端发送心跳请求。

#### 故障现象
+ 当kstream进程发生full gc时，如果stw的时间超过了心跳的时间，会造成当前进程里包含的kafka consumer被剔除出group，进而导致kstream状态转为rebalancing。所以如果kstream进程频发发生超过心跳时间的stw，会极大影响kstream的性能，极端情况下，有可能导致几十分钟的解析停止

#### 故障诊断 
+ kstream会从kafka消费消息，处理完毕后，再写回提交给kafka，因此理论上在内存里面的主要数据应该就是ConsumerRecord以及ProducerRecord，大小应该和提交的时间间隔以及poll的记录条数相关。一般情况下，提交的时间间隔应该在10-20秒之间，记录不会在内存中存活很长时间，因此一般情况不会有问题。
+ 但是当并行运行的数据流的数量逐步增长时，例如同时存在的kthread数量在500时，内存中的记录数量显然和只有几个kthread是相差很大的，因此当新增数据流时，要及时注意kstream进程的内存使用情况以及gc的统计，当出现有kstream状态不稳定(会转换为rebalancing)或者full gc时，要及时调整内存大小，当内存超过8g时，建议调整gc算法为g1。
+ 除了数据流增长导致的gc问题外，还有可能是单条记录异常增大，例如单条记录的大小超过了1m，这不光可能导致kstream状态不稳定，还可能会导致kafka 服务端内存溢出，因此也需要定期检查kafka 服务端的日志以及gc情况，及时调整heap大小.


