####                                                     我们的工具

##### *1 kafka consumer*
+ 只要是通过kafka传递的消息，都是可以旁路的，可以看到所有组件之间的消息交互。
+ 通讯用的topic列表，都在itoaService.conf里面

##### *2 各组件log*
+ Common sense，一般各组件log都在各组件的logs目录下，要熟练掌握怎么从庞大的log文件中找到自己需要的信息，各种命令: head, more, vi, grep [-v], tail....
+ 特别需要注意的是，对于流处理和存储这两个组件，对应每个任务都会有单独的log文件，在logs/worker/目录下，log文件名是以dataSetName为名字进行命名的。

##### *3 Chrome*
+ 对于前端和itoa之间的请求，可以通过chrome的网络控制台观察的非常清楚，因此请用好它。

##### *4 ps/jps以及Netstat以及firewalld*
+ 对于所有的调用失败，首先要确认被调用的那个是否活着，是否等待着，是否无隔离的等待着。

##### *5 top -[H]p*
+ 当觉得慢的时候，对各个组件进程检查一下，如果发现持续CPU居高不下，你就离真相不远了

##### *6 iostat -dkx 2
+ iops的表现，会非常大影响es的写入和查询

##### *7 jstat -gcutil pid 1000*
+ 当没事的时候，当没有方向时，针对所有的java组件，看看gc的情况，说不定就能发现虽然它看起来活着，但实际已经死了。

##### *8 jmap -histo:live pid*
+ 当发现已经死亡，且重生后还会不间断死亡的时候，要看看到底是怎么死的。java堆对象统计。

##### *9 jstack  pid*
+ 当发现很慢，很卡，cpu很高时，可以看看是谁在搞事情。 java 堆栈统计。

##### *10 kafka metrics
+ jmx: [kafka monitor](https://docs.confluent.io/current/kafka/monitoring.html)
  + broker metric
    + UnderReplicatedPartitions
    + OfflinePartitionsCount
    + ActiveControllerCount
    + BytesInPerSec
    + BytesOutPerSec
    + NetworkProcessorAvgIdlePercent
    + RequestQueueSize
    + ......
  + zk metric
    + ZooKeeperExpiresPerSec
    + ZooKeeperDisconnectsPerSec
    + ......
  + producer metric ....
  + consumer metric .....
  + consumer group metric ......

+ command: 
	+ lag: ./kafka-consumer-groups.sh --bootstrap-server [broker] --group [group] --describe

+ log: 
	+ [stream|persistent]/logs/[kafka.log, consumer.log, producer.log]

##### *11 elastic search metrics
+ cat [es cat](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)
  + health
  + nodes
  + indices/shards/segments
  + allocation/thread pool
#### *12 our brain*
+ 最关键的还是靠靠AI我们的大脑，靠我们细致缜密的逻辑推断。



