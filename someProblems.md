#### java

* Java8 Stream

* optional

* Nio （网络io 同步阻塞 非阻塞 异步）

* 使用过jdk8提供的几个功能性接口吗FunctionalInterface（consumer,Supplier,预言）

* fullgc触发条件

* hashmap

  * size(kv个数),capacity(容量,默认16,集合的最大容量),loadFactor(负载因子,默认0.75f),threshold(临界,超过这个值就会扩容, 该值由 capacity*loadFactory所得)
  * 初始化构造指定容量,取最近的2^n次方, 方法是 几次无符号右移 + 或运算,  目的是将二进制第一个不为0的位置开始,后面全设置为1，比如 ```0000 0101``` (运算完成) ->```0000 0111```, 0000 0111最后再+1,就得到最近的2^n次方了。
    * 设置多少容量合适? （需要存储的个数/负载因子）+ 1；guava里面也是这么用的。目的是减少扩容次数。 
  * 关于hash()和取模，取模用位运算,  除数是2^n次方情况下， A% (2^n) = A&(n-1) ,{相当于取a的后n位}，hash()里面为了防止哈希冲突进行了扰动计算。 （比如0000 0000 1100 1010和1111 1100 1100 1010虽然高位不同,但是低位相同，这样在和 0000 0000 1111 1111 与的时候，高位就没用了相当于，只拿低位的数，hash冲突很容易发生）
  * Put()方法过程简述, 先根据hash找到数组位置,null直接放进去, 否则判断和目前位置是否hash,equal一样，一样的话覆盖。 如果不一样走链表这一段，（如果是treenode节点的话，走treeput方法）,不是treenode节点,走链表，遍历链表往尾部放,（如果在遍历过程中发现hash，equal相等的相同元素，直接break）。
  * hashmap,hollis,(((https://mp.weixin.qq.com/s/Ky4pWzdJtpdpJqKBxn71Nw

* spi,可动态插拔（解耦）的实现类

* 线程状态

  * new， runnable，blocked(syschronized) ，wait(join,park，lock.lock()), time wait，terminal
  * lock.lock()底层使用的aqs->park
  * ```阻塞io方法,线程状态是什么？ 是runnalbe，在操作系统层面io等待内容到达才会获得执行，在java层面,认为 等待io和等待cpu执行权都是一样的。```

  ##### 线程池

  * 任务处理流程和最佳实践  美团最佳线程池实践>>（https://mp.weixin.qq.com/s/baYuX8aCwQ9PP6k7TDl2Ww）
  * 阻塞队列，ArrayBlockingQueue在并发下,由于锁竞争,会影响性能。(在rpc课程里有一个队列多线程模型分析, 单队列多线程,多队列单线程/)，ArrayBlockingQueue还存在着伪共享的问题。

* 伪共享的问题

  * 一个对象的各个变量存在一个cacheline里面，多线程情况下,a线程修改变量1,导致cacheline失效,b线程想要拿变量2,需要从主存里面拿。
  * 解决方案就是扩充一个对象的量



#### 并发编程

* 可见性,原子性，有序性 导致的多线程问题
* java提供的 synchronized解决原子性问题，(修饰静态方法,锁对象 x.class,修饰一般方法，或者 syn(obj) )
  *  '对一个锁解锁 Happens-Before 后续对这个锁的加锁', 可见性有保障
  * 如果有get方法，也需要加syn，保障可见性

#### redis

* redis使用场景
* 缓存击穿穿透雪崩，处理方案
* 缓存一致性
* 分布式锁的问题
  * 数据库行锁,X
  * ``redis master节点获取到锁,宕机``——> `slave节点升级为master`— —> `锁丢失`
  * `client A获取到master锁（此时slave已经同步了锁）`—>`master宕机`—>`slave升级为master`—>`client B获取到锁`  造成竞技态 多个client持有相同lock的问题



#### mybatis

* mybatis插件原理有了解吗
* mybatis怎么实现新增返回主键

#### spring

* aop的原理和使用场景





#### mysql

* mysql 日志，redo undo 
* mysql mvcc
* 事务隔离级别，每种隔离级别会出现什么问题。 RR级别和RC级别针对MVCC的不同点在哪。
* 索引下推
* 回表,覆盖索引
* mysql8
  * ```SELECT * from performance_schema.data_locks```
  * 设置完隔离级别 新打开会话生效
  * 

##  

* 知道java SPI机制吗？
* 有了解或者用过graphql吗？
* 什么是两阶段提交和三阶段提交，两阶段缺陷在什么地方？



#### rocketmq

* 2pc,3pc提交

* rocketmq消息存储(commitLog,consumeQueue,indexFile,)

* rocketmq的事务消息有了解吗（半消息,需要回查，业务侵入大）

* 消息消费pull push方式，两者有什么弊端, rocketmq怎么解决这种弊端的

* **rocketMq延时消息,事务消息,重试消息 都是利用替换主题和队列这种方法进行处理的。**

  * 事务消息侵入大，延时消息可用性不高（只有特定level场景有限），应该怎么办？




#### 应用型数据结构和算法

* 过滤敏感词 前缀树算法

* hystrix- 滑动窗口

* 大批量超时任务 ，比如动态心跳，用到的时间轮算法，熔断hystrix的滑动窗口

  ###### leetcode

  * 反转链表,k个一组反转链表



#### 设计

* 模版模式，rpc生成动态桩stup的代码逻辑, 宝洁报告也可以使用

* 观察者, 主题notify观察者。
  * ad活动或者点位变更需要重算，可以采用这种设计模式
  * 活动修改发邮件，增加数据库操作记录可以异步
* 策略模式, （95,99,1折）
* 责任链模式.mybatis 拦截。 业务中比如从babel同步数据, 经过几层校验，可以使用。
  * 开闭原则指的是扩展代码的时候,或者增加业务功能的时候,不在原有代码修改,通过新增类来实现的一种原则。
* 



