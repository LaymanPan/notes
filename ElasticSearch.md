## ElasticSearch 安装

### 1.下载

https://www.elastic.co/cn/start

windows 下载完成后解压缩 运行bin目录下的elasticsearch.bat即可

![image-20220313135716365](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313135716365.png)

### 2.文件目录

![image-20220313140823504](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313140823504.png)

### 3.熟悉目录

```
bin 启动文件
config 配置文件
	log4j2.properties 日志配置
	jvm.options 虚拟机配置
	elasticsearch.yml elasticsearch的配置文件 默认9200端口
lib 相关jar包
modules 功能模块
plugins 插件
```

### 4.启动

运行elasticsearch.bat

![image-20220313141502972](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313141502972.png)

> 中文乱码 修改jvm.options

```
增加
-Dfile.encoding=GBK
```



![image-20220313141522244](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313141522244.png)

## 安装可视化界面 es head的创建

下载地址：https://github.com/mobz/elasticsearch-head

解压后在文件目录下分别执行

```
npm install
npm run start
```

默认会有跨域问题

![image-20220313144528338](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313144528338.png)

修改elasticsearch.yaml增加跨域支持

```
http.cors.enabled: true
http.cors.allow-origin: "*"
```

重启服务器即可

![image-20220313144810616](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313144810616.png)

可以把索引比作 数据库 ， 文档比作 数据

> head 可以作为数据展示工具 ，后面使用kibana作为查询工具



## 了解ELK

https://www.elastic.co/cn/what-is/elk-stack

 “ELK”是三个开源项目的首字母缩写，这三个项目分别是：Elasticsearch、Logstash 和 Kibana。Elasticsearch 是一个搜索和分析引擎。Logstash 是服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到诸如 Elasticsearch 等“存储库”中。Kibana 则可以让用户在 Elasticsearch 中使用图形和图表对数据进行可视化。

Elastic Stack 是 ELK Stack 的更新换代产品。



## Kibana

下载地址：https://www.elastic.co/cn/downloads/past-releases#kibana

Kibana版本需要和ElasticSearch版本一致

### 1.启动测试

解压完成后

![image-20220313150417914](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313150417914.png)

运行 bin\kibana.bat 默认端口 5601

![image-20220313150547252](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313150547252.png)

### 2.页面访问

> 遇到报错 TypeError: Invalid character in header content ["kbn-name"]

![image-20220313150814800](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313150814800.png)

> 解决方法

```
kibana.yaml 增加配置
server.name: "kbn"
```

> 页面中文

```
kibana.yaml 增加配置
i18n.locale: "zh-CN"
```



## ES核心概念

1. 索引
2. 字段类型（mapping）
3. 文档（document）



### 1.概述

在前面的学习中，我们已经掌握了es是什么，同时也把es的服务已经安装启动，那么Es是怎么去存储数据，数据结构又是什么，又是如何去实现搜索的呢？

### 2.Es是面向文档的，与关系型数据库的对比 均为JSON

| 关系型数据库      | ES           |
| ----------------- | ------------ |
| 数据库（database) | 索引（index) |
| 表（table)        | types        |
| 行（rows)         | documents    |
| 字段（columns)    | fields       |

es（集群）中可以包含多个索引，每个索引中可以包含多个类型，每个类型下又包含多个文档，每个文档中又包含多个字段

### 3.物理设计

es在后台把每个索引分成多个分片，每个分片可以在集群的不同服务器间迁移

单个服务就是一个集群

![image-20220313152617125](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313152617125.png)

### 4.逻辑设计

一个索引类型中，包含多个文档，如果文档1，2，3。当我们索引一个文档时，可以通过这样的一个顺序找到它：索引>类型>文档ID，通过这个组合就能索引到具体的文档。注意：ID不必是整数，实际上是字符串

> 文档

之前说ES是面向文档的，那么就意味着索引和搜索数据的最小单位是文档，es中文档有几个重要属性：

自我包含：一篇文档同时包含字段和对应的值 也就是同时包含key:value

可以是层次型的：一个文档中包含自文档，复杂的逻辑实体就是这么来的（json数据）

灵活的结构：文档不依赖预先定义的模式，我们知道关系型数据库中，要提前定义字段才能使用，在es中对于字段是非常灵活的，有时候我们可以忽略该字段，有时候可以动态添加一个字段

尽管我们可以随意的新增或者忽略某个字段，但是，每个字段的类型非常重要，比如一个年龄字段类型，可以是字符，也可以是整型。因为es会保存字段和类型之间的映射及其他的设置。这种映射具体到每个映射的每种类型，这也是为什么在es中类型也称为映射类型

> 类型

类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。类型中对于字段的定义称为映射，比如name映射为字符串类型。我们说文档是无模式的，它们不需要拥有映射中所定义的所有字段，比如新增一个字段，那么es是怎么做的呢？es会自动将新字段加入映射，但是这个字段不确定它是什么类型，es就开始猜测，如果这个值是18，那么es就认为它是整型，但es也有可能猜错，所以最安全的方式就提前定义好所需要的映射，这点跟关系型数据库殊途同归，先定义好字段然后再使用

> 索引

就是数据库！

索引是映射类型的容器，es中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。然后它们被存储到了各个分片上了。

**物理设计：节点和分片是如何工作**

一个集群至少有一个节点，而一个节点就是一个es进程，节点可以有多个索引默认的，如果你创建索引，那么索引将会有5个分片（primary shard，又称为主分片）构成的，每一个分片会有一个副本（replica shard，又称为复制分片）

![image-20220313154132395](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313154132395.png)

![image-20220313154830241](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313154830241.png)

如上图就是一个有3个节点的集群，可以看到主分片和对应的复制分片都不会在同一个节点，这样有利于一个节点挂掉了，数据也不至于丢失。实际上一个分片就是一个Lucene索引，一个包含 **倒排索引** 的文件目录，倒排索引的结构使得es在不扫描全部文档的情况下，就能告诉你那些文档包含了特定的关键字。

> 倒排索引





### 5.mappings



> 字段类型

![Springboot2.x整合ElasticSearch7.x实战（三）_经验分享_05](https://s8.51cto.com/images/blog/202106/21/fd33ea72950ef35d9dea9bfe4f7e2fc3.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)



## IK分词器

分词：即把一段中文或者别的划分成一个个的关键字，我们在搜索的时候会把自己的信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一个匹配操作，默认的中文分词器是把每个字看成一个词，并不是很符合要求所以需要安装中文分词器IK来解决这个问题。

IK提供了两个分词算法：ik_smart和ik_max_word, 其中**ik_smart**为最少切分，**ik_max_word**为最细粒度划分

### 1.安装

IK 的版本和ES保持一致

下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases

下载解压成文件夹ik 到 es/plugins

![image-20220313163118479](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313163118479.png)

### 2.重启ES

![image-20220313163221583](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313163221583.png)

查看安装好的插件

![image-20220313163313800](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313163313800.png)

### 3.使用kibana测试

> ik_smart最少切分

![image-20220313163917658](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313163917658.png)

> ik_max_word 最细粒度

![image-20220313163803813](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313163803813.png)



> 发现名字被拆分成单个字，需要手动添加进IK分词字典

![image-20220313164159151](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313164159151.png)



> 增加额外的字典

![image-20220313164518980](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313164518980.png)

> 修改 ik /config/IKAnalyzer.cfg

![image-20220313164533290](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313164533290.png)

重启ES进行测试

![image-20220313165009780](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313165009780.png)

## 基础测试

API地址：https://www.elastic.co/guide/cn/elasticsearch/guide/current/_dealing_with_null_values.html

| 方法   | URL地址                                         | 描述                   |
| ------ | ----------------------------------------------- | ---------------------- |
| PUT    | localhost:9200/索引名称/类型名称/文档ID         | 创建文档（指定文档ID） |
| POST   | localhost:9200/索引名称/类型名称                | 创建文档（随机文档ID） |
| POST   | localhost:9200/索引名称/类型名称/文档ID/_update | 修改文档               |
| DELETE | localhost:9200/索引名称/类型名称/文档ID         | 删除文档               |
| GET    | localhost:9200/索引名称/类型名称/文档ID         | 通过文档ID查询文档     |
| POST   | localhost:9200/索引名称/类型名称/_search        | 查询所有数据           |

### 1.创建一个索引并存入值

```
PUT /索引名/类型名/文档ID
{
请求体
}
```

![image-20220313170443669](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313170443669.png)

![image-20220313170533447](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313170533447.png)

### 2.指定数据类型创建索引

![image-20220313170933635](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313170933635.png)

![image-20220313170947206](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313170947206.png)

### 3.获取索引信息

![image-20220313171056764](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313171056764.png)

### 4.查看默认信息

![image-20220313171222431](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313171222431.png)

![image-20220313171248507](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313171248507.png)

如果自己的文档没有指定字段类型，那么es就会给我们默认配置字段类型！

扩展：

_cat/health: 查看健康情况

### 5.修改文档

![image-20220313171850693](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313171850693.png)

![image-20220313171946920](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313171946920.png)

可以增加原先索引中没有的字段



### 6.删除

> 删除文档 DELETE /{indexName}/_doc/{docId}

![image-20220313172113217](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313172113217.png)

![image-20220313172214787](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313172214787.png)

> 删除索引 DELETE /{indexName}

![image-20220313172259877](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313172259877.png)

使用正则删除

![image-20220313172436907](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313172436907.png)





## 关于文档的操作

> 基本操作

### 1.添加数据

![image-20220313173402219](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313173402219.png)

### 2.查询数据

参考地址：https://zhuanlan.zhihu.com/p/183816335

根据ID进行查询

![image-20220313173627781](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313173627781.png)

### 3.更新数据

PUT 请求会覆盖原先的数据如果部分字段没有值的话会清空之前的数据

![image-20220313173741225](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313173741225.png)

POST 则只会影响更新的字段 **推荐使用**

![image-20220313174042991](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313174042991.png)

### 4.条件查询

GET /{indexName}/_search?q=field:value 
模糊查询

![image-20220313174646172](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313174646172.png)

**请求参数 增加 双引号 则视为精准查询 未添加双引号则会先分词**

如：

![image-20220313175157655](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313175157655.png)

![image-20220313175215251](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313175215251.png)

> 复杂查询（排序 分页 高亮 模糊查询 精准查询

### 1.查询

![image-20220313180003766](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313180003766.png)

### 2.返回字段控制

![image-20220313180417404](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313180417404.png)

### 3.排序

![image-20220313180816198](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313180816198.png)

### 4.分页查询

![image-20220313181001666](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313181001666.png)

### 5.bool查询

![image-20220313184203406](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313184203406.png)



![image-20220313190349295](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313190349295.png)

>  must: 多个条件之间类似and连接

> should: 多个条件之间类似or连接

>  must_not:

不满足则查询到

![image-20220313184436831](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313184436831.png)



> filter 结果的过滤



> 精确查询

term 查询是直接通过倒排索引指定的词条进行精确查询的

关于分词：

term：直接查询精确的，配合keyword类型使用 text类型无效

match：会使用分词器解析（先分析文档，然后再通过分析的文档进行查询！）

**两个类型 text, keyword**

text会被分词器解析，keyword则不会被分词器解析

![image-20220313201806262](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313201806262.png)

### 6.高亮查询

![image-20220313202741306](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313202741306.png)

> 查询null字段

使用exists

![image-20220313204109728](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313204109728.png)



> 查询非null字段

在早期版本中有missing字段 但新版本已经废弃，使用反向exists进行查询

![image-20220313204145306](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313204145306.png)

## SpringBoot整合ES

API 地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html

![image-20220313205706641](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313205706641.png)

### 1.maven地址

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.17.1</version>
</dependency>
```

### 2.保证springboot pom中es版本和服务器一致

![image-20220313214950540](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20220313214950540.png)

项目地址：https://github.com/LaymanPan/SpringBootTemplate.git
