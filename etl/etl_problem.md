# ETL要解决的关键问题

ETL就是在做数据同步的同时做数据转换，相当于路由器。
ETL有两个问题需要得到根本解决。第一个是增量的问题，第二个是实时同步的问题。

## 增量问题

### A方案，清空表

原始或不考虑性能的ETL不做增量，仅仅是查出A表，写入B表，需要更新B的时候就清空B表，
重新重复一次`A->B`的过程。

A方案的弊端是，在`A->B`的过程中数据量大的时候，从A读取数据，传输数据，到B写入数据都可能很慢。
其中`A->B`的大部分数据可能是重复传输的。

### B方案，时间戳

解决A方案的弊端的好方法是不要重复传输数据。那么如何知道`A->B`中，A和B的数据异同呢？可以根据
A的时间戳来判断。给A的每一条数据都加上一个`update_time`字段,更新时间。如果A增加或者修改了某一条数据，
就要修改`update_time`为`now`，`A->B`加上查询逻辑，A表的`update_time`大于等于B表的最大的`update_time`(如果B表没有数据，直接插入)。
这时候会选出A改变了的那部分，然后只更新A改变的部分到B就可以了。

B方案只选出A改变的部分，更新数据给B。A中选出的那部分，有一些记录可能已经在B中存在了（只是数据的某个字段有变化），也可能不存在（纯新增的数据）。
要区分这两种情况，需要某些数据更新，某些数据插入。这是一个[upsert问题-todo]()。

## 实时同步问题

### A方案，批量执行

### B方案，近实时执行

### C方案，流实时

