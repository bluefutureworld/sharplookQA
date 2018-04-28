### 夏洛克框架.   
夏洛克所用到的组件列表如下：      
#### *开源组件*.  
* elastic search, kafka, zookeeper, mysql, ngix
#### *自研组件*.  
* itoa, stream, persistent, event, curator, cell, router, agent, dist

#### **组件相互关系**

![架构图](https://github.com/yinchuanwang/sharplookQA/blob/master/arc.001.jpeg)
##### 夏洛克的25个数据流向解说

###### 系统启动后(API调用)

+ ##### 创建系统通讯需要的topic

  + 1 ITOA初次启动之创建相关topic之请求。
    + 创建哪些topic？
      + 在itoaService.conf里面进行配置 。
    + 怎么创建？
      + 需要连接zookeeper。
  + 2 ITOA初次启动之创建相关topic之响应。
    + 查看itoaService.log查看topic是否创建成功，当返回创建成功或者已存在，都是正常的。

+ ##### Curator开始采集kafka topic对应的偏移量

  + 3 Curator启动后获取topic列表，开始采集group对应的消费偏移量
    + 查看zookeeper是否配置正确，获取topic列表
    + 查看curator的日志，看zookeeper和kafka是否连接成功
  + 4 Curator启动后把定期采集的topic的信息写入elastic search
    + 查看elastic search的地址配置是否正确
    + 查看日志，查看往elastic search的数据写入是否成功

###### 数据采集
+ ##### 创建数据流采集对应的Topic
  + 5 ITOA发送消息给Curator创建相关topic之请求
  + 6 Curator调用zk接口创建相关topic请求
  + 7 Curator调用zk接口创建相关topic响应
  + 8 Curator发送消息给ITOA创建相关topic之响应

+ ##### 下发采集指令给采集客户端

  + 9 ITOA发送采集消息给Cell之请求 - kafka message
  + 10 Cell下发采集指令给对应服务器的Mave进程 - tcp message
  + 11 Mave根据下发的采集指令生成新的采集配置文件，重启Flow进程 - 进程间通讯
  + 12 Flow开始采集数据并且上报给Router - tcp message
  + 13 Router把收到的消息写入对应的Topic - Kafka message

###### 数据解析(Kafka消息)
+ ##### 预览数据解析对应的源Topic

  + 14 ITOA发送预览消息给Curator
  + 15 Curator创建Kafka Consumer消费对应topic的数据，发送预览响应消息给ITOA
  
+ ##### 创建数据解析对应的目标Topic
  + 14 ITOA发送创建Topic消息给Curator
  + 15 Curator连接Zookeeper创建topic，发送创建topic响应消息给ITOA

+ ##### 启动数据流解析任务

  + 16 ITOA发送创建数据流解析任务给Stream，界面状态置为启动中。

  + 17 Stream内部各节点启动对应的Kafka Stream，发送启动结果响应消息给ITOA，ITOA收到消息后更新状态为启动成功或者启动失败

  + 18 Kafka Stream线程中的Kafka Consumer开始从kafka读取消息，并执行对应的解析逻辑，生成新的消息

  + 19 Kafka Stream线程中的Kafka Producer把生成的新的消息，定期写入新的目标Topic

###### 数据存储(Kafka消息)


+ ##### 启动数据流存储任务
  + 20 ITOA发送创建数据流存储任务给Persist，界面状态置为启动中。
  + 21 Persist内部各节点启动对应的存储任务，发送启动结果响应消息给ITOA，ITOA收到消息后更新状态为启动成功或者启动失败
  + 22 存储任务中的Kafka Consumer开始从Kafka 读取消息，并转化为对应的存储结构(es doc)
  + 23 存储任务中的Es Client把转化好的存储结构批量写入elastic search


###### 数据查询(Http请求)
  + 24 前端把用户输入的spl查询语句发送给ITOA后，ITOA转发给Query服务
  + 25 Query服务把用户输入的spl语句转化为dsl后，调用es进行实际查询，并把es返回的查询结果返回给ITOA,前端


