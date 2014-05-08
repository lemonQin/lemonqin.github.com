---
layout: post
title: mapreduce设置map,reduce数
description: 
tags: [hadoop，mapreduce]
image:
  feature: abstract-9.jpg
comments: true
share: true
---

用mahout来执行任务，发现map数和ruduce数目总是为1， 运行十分缓慢，于是想怎样调整mapreduce的map和reduce数目，通过查阅资料和实验，得到如下：

* 集群的map/reduce数设置

map设置：map的任务数是有splits的个数决定的，splitsSize的大小由以下规则决定

minSize=max{minSplitSize,mapred.min.split.size} （minSplitSize大小默认为1B）
maxSize=mapred.max.split.size（不在配置文件中指定时大小为Long.MAX_VALUE）
splitSize=max{minSize,min{maxSize,blockSize}}

一个输入文件按照splitSize进行分割，分成多少份，就会起多少个map task

-Dmapred.max.split.size=10 000 000 单位为B

reduce设置：

直接设置reduce数即可

-Dmapred.reduce.tasks=4

* 单个节点的map/reduce数设置

单个节点的map、reduce数可以直接再hadoop的配置文件mapred-site.xml中设置
{% highlight html %}
  <property>
    <name>mapred.tasktracker.map.tasks.maximum</name>
    <value>11</value>
  </property>

  <property>
    <name>mapred.tasktracker.reduce.tasks.maximum</name>
    <value>11</value>
  </property>
{% endhighlight %}