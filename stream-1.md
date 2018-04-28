


















# 数据流启动成功，但是目标topic并没有消息输出！！！



















![kstream 内部](https://github.com/yinchuanwang/sharplookQA/blob/master/streaminside.001.jpeg)

#### 诊断分析
+ Filter构造失败的原因是？
+ kstream构造失败的原因是?
+ kstream的状态一直无法切换到running的原因是？


















#### 诊断过程
+ 查看数据流对应的日志文件。$streamdir/logs/worker/worker-dataSetName.log
    + log文件里面有filter的构造字符串
    + log文件里面有kstream的构造字符串
    + log文件里面会打印kstream的状态
+ kstream无法变为running的原因
    + kafka状态不稳定
        + IO, Network
            + 磁盘不能与其他数据库混用
            + 万兆卡
    + 从 $streamdir/logs/internal.log来看
        + 正常情况: joining group-> join complete -> assgin partitions
        	 异常情况: joining group之后会hang住， 会有不间断的make coordinator dead日志打印 	
