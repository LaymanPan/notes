# Redis

## 概述

> Redis 是什么

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API

![image-20210815101204445](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815101204445.png)



> Redis能做什么

1、内存存储、持久化内存中是断电即失， 所以持久化很重要（rdb, aof）

2、效率高，可用于高速缓存

3、发布订阅系统

4、地图信息分析

5、计数器

。。。

>  特性

1、多样的数据类型

2、持久化

3、集群

4、事务

> 学习中需要用到的东西

1、官网： https://redis.io/

2、中文网： http://www.redis.cn/

## Linux安装

> 1、下载安装包

![image-20210815201656918](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815201656918.png)

>  2、 上传文件并解压

![image-20210815201723050](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815201723050.png)

> 3、在解压出来的文件夹中进行编译

![image-20210815202027118](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202027118.png)

![image-20210815202012957](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202012957.png)



> 4、安装 make PREFIX=/usr/local/redis install

![image-20210815202125409](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202125409.png)

> 5、配置redis配置文件

![image-20210815202613210](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202613210.png)

> 6、启动redis服务 redis-server [配置文件路径]

![image-20210815202813413](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202813413.png)

> 7、使用redis-cli进行连接 redis-cli -p 6379

![image-20210815202852985](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815202852985.png)

> 8、关闭redis关闭服务器 redis-cli连接下 执行shutdown

![image-20210815203148418](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815203148418.png)



## 基础知识



>  1、 redis默认有16个数据库默认使用第0个

![image-20210815204500124](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815204500124.png)

```bash
127.0.0.1:6379> select 1 	#切换到数据库1
OK
127.0.0.1:6379[1]> DBSIZE	# 查看数据库大小
(integer) 0
127.0.0.1:6379[1]> set name zgl	
OK
127.0.0.1:6379[1]> get name 
"zgl"
127.0.0.1:6379[1]> 

127.0.0.1:6379[1]> keys * # 查看当前库所有KEY
1) "name"
127.0.0.1:6379[1]> 


127.0.0.1:6379[1]> FLUSHDB # 删除当前库的所有KEY
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> 

127.0.0.1:6379[1]> FLUSHALL # 删除所有库
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> 
```

> redis 是单线程

明白redis是很快的，官方表示 Redis 是基于内存操作，CPU不是Redis的瓶颈，Redis 的瓶颈是根据机器的内存和网络带宽 既然可以使用单线程使用 就用单线程 所以就使用了单线程！

redis 是 C语言写的 10W QPS 完全不比Memcache 差

Redis 为什么单线程还这么快

误区1、高性能服务器一定是多线程的

误区2、多线程（CPU）一定比单线程效率高

核心：Redis是将所有数据放到内存中，所以说使用单线程操作效率更高



## 五大数据类型

> 官网

![image-20210815214707739](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210815214707739.png)

### Redis-Key

```bash
27.0.0.1:6379[1]> set name panym
OK
127.0.0.1:6379[1]> get name 
"panym"
127.0.0.1:6379[1]> EXISTS name # 判断KEY=name 是否存在
(integer) 1
127.0.0.1:6379[1]> EXPIRE name 10 # 设置过期时间为10S
(integer) 1
127.0.0.1:6379[1]> ttl name	# 查看剩余时间
(integer) 8
127.0.0.1:6379[1]> ttl name
(integer) -2
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> set name zgl 
OK
127.0.0.1:6379[1]> keys *
1) "name"
127.0.0.1:6379[1]> move name 0 # 移动 name 到数据库0
(integer) 1
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> del name # 删除Key值
(integer) 1
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> 

```



### String

```bash
127.0.0.1:6379> set name panym 		 # 设置值
OK
127.0.0.1:6379> get name 			# 获取值
"panym"
127.0.0.1:6379> EXISTS name			# 判断是否存在KEY
(integer) 1
127.0.0.1:6379> append name zgl		 # 指定KEY值追加，如果KEY不存在相当于Set 
(integer) 8
127.0.0.1:6379> get name
"panymzgl"
127.0.0.1:6379> STRLEN name			# 获取值长度
(integer) 8
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views			# 自增 1
(integer) 1
127.0.0.1:6379> decr views			# 自减 1
(integer) 0
127.0.0.1:6379> INCRBY views 4		# 增加指定值
(integer) 4
127.0.0.1:6379> DECRBY views 5		# 减少指定值
(integer) -1

127.0.0.1:6379> set name suanwukong	
OK
127.0.0.1:6379> GETRANGE name 0 3	# 获取指定长度的值
"suan"
127.0.0.1:6379> GETRANGE name 0 -1  # 获取全部字符串
"suanwukong"

127.0.0.1:6379> SETEX today 10  0816 # 设置值并设置过期时间 （set  with expire）
OK
127.0.0.1:6379> ttl today
(integer) 7
127.0.0.1:6379> ttl today
(integer) 6
127.0.0.1:6379> ttl today
(integer) 5
127.0.0.1:6379> get today
"0816"
127.0.0.1:6379> get today
"0816"
127.0.0.1:6379> get today
(nil)
127.0.0.1:6379> get today
(nil)
127.0.0.1:6379> SETNX today 0815 # 如果不存在 则设置 分布式锁 (set if not exist)
(integer) 1
127.0.0.1:6379> SETNX today 0815
(integer) 0
127.0.0.1:6379> SETNX today 0810
(integer) 0

127.0.0.1:6379> MSET k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> keys *
1) "name"
2) "today"
3) "k3"
4) "k1"
5) "k2"
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> MSETNX k1 v1 k4 v4
(integer) 0
127.0.0.1:6379> MSETNX k4 v4 k1 v1 	# msetnx 是一个原子性的操作 要么一起成功 要么一起失败
(integer) 0
127.0.0.1:6379> keys *
1) "name"
2) "today"
3) "k3"
4) "k1"
5) "k2"


127.0.0.1:6379> MSET user:1:name zhangsan user:1:age 11
OK
127.0.0.1:6379> get user:1:name
"zhangsan"
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "11"

127.0.0.1:6379> getset today sunday # 如果不存在则返回nil 并设置为新的值
"0815"
127.0.0.1:6379> get today
"sunday"
```



### List

```bash
127.0.0.1:6379> lpush list 1 # 从左边插入数据
(integer) 1
127.0.0.1:6379> lpush list 2
(integer) 2
127.0.0.1:6379> lpush list 3
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1 # 通过区间获取具体的值
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> rpush list 4 # 从右边插入数据
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "3"
2) "2"
3) "1"
4) "4"


127.0.0.1:6379> LPOP list 	# 弹出第一个元素
"3"
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"
3) "4"
127.0.0.1:6379> rpop list # 弹出最后一个值
"4"
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "1"


127.0.0.1:6379> LLEN list	# 获取list 的长度
(integer) 2
127.0.0.1:6379> LINDEX list  1 # 去除 1号位数据
"1"

# LREM 
# count > 0: Remove elements equal to element moving from head to tail.
# count < 0: Remove elements equal to element moving from tail to head.
# count = 0: Remove all elements equal to element.
127.0.0.1:6379> rpush list 1 2 3 4 
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> LREM list 1 "1"
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "3"
3) "4"
127.0.0.1:6379> lpush list 2 
(integer) 4
127.0.0.1:6379> rpush list 2
(integer) 5
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "2"
3) "3"
4) "4"
5) "2"
127.0.0.1:6379> LREM list 2 2
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "4"
3) "2"
127.0.0.1:6379> lpush list 2 2
(integer) 5
127.0.0.1:6379> lrange list 0 -1
1) "2"
2) "2"
3) "3"
4) "4"
5) "2"
127.0.0.1:6379> lrem list 0 2
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "3"
2) "4"


# Ltrim 只保留指定区间的数据
127.0.0.1:6379> rpush list 1 2 3 4 5 
(integer) 5
127.0.0.1:6379> ltrim list 1 2
OK
127.0.0.1:6379> lrange list 0 -1
1) "2"

# RpopLpush 
127.0.0.1:6379> rpush list1 1 2 3 4 5 
(integer) 5
127.0.0.1:6379> lrange list1 0 -1
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> RPOPLPUSH list1 list2
"5"
127.0.0.1:6379> lrange lis1 0 -1 
(empty list or set)
127.0.0.1:6379> lrange list1 0 -1 
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> lrange list2 0 -1
1) "5"

# LSET [key] [index] [value]更新指定下标的值 如下标不存在则会报错
127.0.0.1:6379> LSET list1 0 5
OK
127.0.0.1:6379> lrange list1 0 -1
1) "5"
2) "2"
3) "3"
4) "4"

# LINSERT 
127.0.0.1:6379> LINSERT list1 BEFORE 5 0
(integer) 5
127.0.0.1:6379> lrange list1 0 -1
1) "0"
2) "5"
3) "2"
4) "3"
5) "4"

# 如果KEY值非空 才会插入
127.0.0.1:6379> lpush list 1 2 
(integer) 2
127.0.0.1:6379> LPUSHX list 3 
(integer) 3
127.0.0.1:6379> lrange list 0 -1 
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> LPUSHX list1 0
(integer) 0
127.0.0.1:6379> lrange list1 0 -1
(empty list or set)


```



### Set

set中的值不能重复

```bash
127.0.0.1:6379> sadd myset hello    # set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset wordl
(integer) 1
127.0.0.1:6379> SMEMBERS myset		# 查看指定Set的所有值
1) "wordl"
2) "hello"
127.0.0.1:6379> SISMEMBER myset hello # 判断值是否在集合中
(integer) 1
127.0.0.1:6379> SISMEMBER myset world
(integer) 0
127.0.0.1:6379> 

127.0.0.1:6379> scard myset			# 获取集合中值个数
(integer) 2
127.0.0.1:6379> sadd myset panym
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> srem myset  hello	# 删除指定元素
(integer) 1
 
 127.0.0.1:6379> SRANDMEMBER myset # 随机选择一个元素
"panym"
127.0.0.1:6379> SRANDMEMBER myset
"wordl"
127.0.0.1:6379> SRANDMEMBER myset
"wordl"
127.0.0.1:6379> SRANDMEMBER myset 2 # 随机选择指定个数元素
1) "wordl"
2) "hello"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "panym"
2) "hello"


127.0.0.1:6379> spop myset 2 # 随机移除指定个数的元素
1) "panym"
2) "wordl"
127.0.0.1:6379> SMEMBERS myset
1) "hello"

127.0.0.1:6379> sadd myset1 world
(integer) 1
127.0.0.1:6379> SMOVE myset myset1 hello # 移动元素到指定set
(integer) 1
127.0.0.1:6379> SMEMBERS myset
(empty array)
127.0.0.1:6379> SMEMBERS myset1
1) "world"
2) "hello"
127.0.0.1:6379> 

数字集合类
- 差集 sdiff
- 交集 sinter
- 并集 sunion

127.0.0.1:6379> sadd myset a b c
(integer) 3
127.0.0.1:6379> sadd myset1 c d e
(integer) 3
127.0.0.1:6379> sdiff myset1 myset
1) "d"
2) "e"
127.0.0.1:6379> SINTER myset myset1
1) "c"
127.0.0.1:6379> SUNION myset myset1
1) "b"
2) "c"
3) "e"
4) "a"
5) "d"

```

### Hash

```bash
127.0.0.1:6379> hset person_1 name panym age 25  # 设置值
(integer) 2

127.0.0.1:6379> hget person_1 name	# 取值
"panym"

127.0.0.1:6379> HDEL person_1 name # 删除值
(integer) 1

127.0.0.1:6379> HEXISTS person_1 name # 判断 key值是否存在
(integer) 0
127.0.0.1:6379> HEXISTS person_1 age
(integer) 1

127.0.0.1:6379> hset person_1 name panym school nk 
(integer) 2
127.0.0.1:6379> hgetall person_1 # 获取全部值
1) "age"
2) "25"
3) "name"
4) "panym"
5) "school"
6) "nk"

127.0.0.1:6379> HINCRBY person_1 age 1 # 递增值
(integer) 26

127.0.0.1:6379> HKEYS person_1 # 获取所有key值
1) "age"
2) "name"
3) "school"
127.0.0.1:6379> HVALS person_1 # 获取所有Value
1) "26"
2) "panym"
3) "nk"
4) "fline"

127.0.0.1:6379> HLEN person_1 # key值数量
(integer) 3

127.0.0.1:6379> HSETNX  person_1 age 26 # key值不存在则添加
(integer) 0
127.0.0.1:6379> HSETNX  person_1 corp fline
(integer) 1


```

hash 存放变更数据 经常变动的信息 Hash适合存放对象 String 存字符串

### Zset(有序集合)

在Set的基础上增加了一个值 set k1 v1 set k1 score1 v1

```bash
127.0.0.1:6379> ZADD stu_score 1 lilei # 添加元素
(integer) 1
127.0.0.1:6379> ZADD stu_score 2 hanmeimei
(integer) 1
127.0.0.1:6379> ZRANGE stu_score  0 -1 # 获取全部元素
1) "lilei"
2) "hanmeimei"

127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf  # 根据score排序 递增
1) "lisi"
2) "zhangsan"
3) "wangwu"

127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores limit 0 2 # 根据score排序 递增 限制长度
1) "lisi"
2) "500"
3) "zhangsan"
4) "1000"

127.0.0.1:6379> ZREVRANGEBYSCORE salary +inf -inf withscores # 降序
1) "wangwu"
2) "1500"
3) "zhangsan"
4) "1000"
5) "lisi"
6) "500"

127.0.0.1:6379> ZREM salary wangwu # 移除元素
(integer) 1

127.0.0.1:6379> ZPOPMAX salary # 弹出最大值
1) "zhangsan"
2) "1000"

127.0.0.1:6379> ZPOPMIN salary # 弹出最小值
1) "lisi"
2) "500"

127.0.0.1:6379> ZCOUNT salary 2000 4000  # score区间值 个数
(integer) 2

同样支持
ZDIFF ZINTER ZUNION
```



## 三种特殊数据类型

### geospatial

![image-20210907213031186](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210907213031186.png)

```bash
127.0.0.1:6379> GEOADD china:city 120.153576 30.287459 hangzhou   # 添加坐标
(integer) 1
127.0.0.1:6379> GEOADD china:city 121.472644 31.231706 shanghai 
(integer) 1
127.0.0.1:6379> GEOADD china:city 116.405285 39.904989 beijing 
(integer) 1

127.0.0.1:6379> GEODIST china:city hangzhou shanghai # 两地之间的距离
"164086.7048"
127.0.0.1:6379> GEODIST china:city hangzhou shanghai km
"164.0867"
127.0.0.1:6379> GEODIST china:city hangzhou beijing km
"1122.4833"

127.0.0.1:6379> GEOPOS china:city hangzhou # 详细经纬度
1) 1) "120.15357345342636108"
   2) "30.28745790721532671"

127.0.0.1:6379> GEORADIUS china:city 118.767453 32.041544 200 km # 指定经纬度 半径内的城市
(empty array)
127.0.0.1:6379> GEORADIUS china:city 118.767453 32.041544 400 km
1) "hangzhou"
2) "shanghai"

127.0.0.1:6379> GEORADIUSBYMEMBER china:city hangzhou 200 km # 指定元素 半径内的地点
1) "hangzhou"
2) "shanghai"


127.0.0.1:6379> GEOHASH china:city hangzhou shanghai # 二维经纬度 转为 hash字符串
1) "wtmkq1tjjp0"
2) "wtw3sjt9vg0"

```



### hyperloglog



### bitmaps



### 事务

Redis 事务本质: 一组命令的集合 ！一个事务中的所有命令都会被序列化， 在事务执行的过程中，会按照顺序执行！

一次性、顺序性、排他性！

```bash
----- 队列 set set set ------
```

Redis 事务没有隔离级别的概念！

所有的命令在事务中，并没有直接被执行！

Redis 单条命令保证原子性，但是事务不保证原子性

redis的事务：

1、开启事务(multi)

2、命令入队(.....)

3、执行事务(exec)

4、取消事务（discard)

锁： Redis可以实现乐观锁，

> 正常执行事务！

```bash

```

