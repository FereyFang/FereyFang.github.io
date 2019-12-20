---
layout:     post
title:      "Hadoop搜索引擎"
subtitle:   "分布式系统课程作业记录"
date:       2019-12-19
author:     "Ruiyu"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - Hadoop
    - HDFS
    - MapReduce
    - class
---

## Hadoop 在 Mac上的安装和启动

主要参考了这一篇[Mac OS X 上搭建 Hadoop 开发环境指南-知乎专栏](https://zhuanlan.zhihu.com/p/33117305)，由于我的环境都已经安装完成了，所以下面只简单记录一下Hadoop的启动命令。

#### 1.启动NameNodegi
```shell
hadoop namenode -format
```

#### 2. 启动HDFS
进入 HADOOP_HOME/sbin, 运行如下的文件
```shell
./start-dfs.sh
```

#### 3. 启动yarn
进入 HADOOP_HOME/sbin, 运行如下的文件
```shell
./ start-yarn.sh
```

#### 4. 验证
其实上面的2，3两步也可以简化成
```shell
./ start-all.sh
```
启用jps，查看系统中所有的java进程，一定要有如下进程才算完成
```shell
NameNode
NodeManager
DataNode
SecondaryNameNode
```
还可以通过调用hadoop文件系统的命令来进行验证是否成功
```shell
hadoop fs -mkdir /search
hadoop fs -ls /
hadoop fs -put ~/test.txt /search 
```
注意命令后面跟着的路径必须是绝对路径，不可是相对路径

#### 5.故障排除
在启动经常出现没有启动DataNode的情况，在jps里可以看到其他所有进程，但是没有DataNode，我在启动的时候这一步经常出错。出错之后hadoop fs的操作涉及mkdir和ls都没有问题，但是put就会报错，因为没有DataNode存储数据。

##### 解决办法
* 首先需要确保现有的dfs上没有任何必须要保存的信息，因为接下来所有的信息都会被删除。然后执行如下命令停止所有hadoop进程：
```shell
./stop-all.sh
```
* 然后我们需要留意执行命令 hadoop namenode -format 时终端上输出的信息，其中一般有这样两条：

```shell
2019-12-20 10:46:50,702 INFO common.Util: Assuming 'file' scheme for path /Users/ruiyu/hadoop-3.2.1/datanode in configuration.
2019-12-20 10:46:50,703 INFO common.Util: Assuming 'file' scheme for path /Users/ruiyu/hadoop-3.2.1/datanode in configuration.
```
这两条信息显示了hadoop的设置中datanode的存储位置，一般来说datanode同级文件夹下也会存在一个namenode对应的文件夹，例如按照上述例子，namenode的位置就在`/Users/ruiyu/hadoop-3.2.1/namenode`,datanode的位置在`/Users/ruiyu/hadoop-3.2.1/datanode`

*  进入namenode和datanode对应的文件夹，清空文件夹内的所有内容
*  重新执行如下命令
```shell
hadoop namenode -format
./start-all.sh
```
*  验证：除了jps确定所有进程都正常运行，还可以通过namenode和datanode文件夹下对应的`/current/VERSION`中的clusterID是不是相等，来判断是否单机启动成功。


---

## Hadoop文件系统操作

常见的请见网址[hadoop HDFS常用文件操作命令-SegmentFault](https://segmentfault.com/a/1190000002672666)










































