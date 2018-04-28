














# 数据流状态不稳定或者解析特别慢！！！






#### 如何定义解析慢，如何确定是否是解析慢？
+ lag的定义：endoffset - readoffset。理想情况lag应该为0或者是接近0的一个数字

+ lag是如何产生的？

  + 数据采集正常，会不断的增加 endoffset。
  + 数据解析慢，因此会读取消息慢，因此readoffset增长会慢。

+ 如何查看是否有lag

  + $kafkabin/kafka-consumer-groups.sh —bootstrap-server kafkaserver —group groupId —describe
  + groupId可以通过 streamdir/logs/worker/worker-datasetName.log里面头部查看


![lag](https://github.com/yinchuanwang/sharplookQA/blob/master/groupdetails.png)