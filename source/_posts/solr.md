---
title: SolrCloud
date: 2018-01-08 17:06:02
tags:
  - Solr
  - Zookeeper
---
## 背景
对于电商平台来说，搜索商品是必不可少的一步，而对于海量的商品数据，若只使用数据库查询，无疑是又慢又耗时的，并且一些特定场景仅使用数据库查询无法做到，这时我们就需要全文检索技术实现搜索功能。
## solr是什么
* Solr 是Apache下的一个顶级开源项目，采用Java开发，它是基于Lucene的全文搜索服务器。Solr提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展，并对索引、搜索性能进行了优化。
* Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr 索引的实现方法很简单，用 POST 方法向 Solr 服务器发送一个描述 Field 及其内容的 XML 文档，Solr根据xml文档添加、删除、更新索引 。Solr 搜索只需要发送 HTTP GET 请求，然后对 Solr 返回Xml、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况。
* Solr 包装并扩展了 Lucene，所以Solr的基本上沿用了Lucene的相关术语。更重要的是，Solr 创建的索引与 Lucene 搜索引擎库完全兼容。
* Solr是一个独立的企业级搜索应用服务器，它对外提供类似于Web-service的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的文件，生成索引；也可以通过提出查找请求，并得到返回结果。
* Solr类似webservice，调用接口，实现增加，修改，删除，查询索引库。
## solr的优势
* 高级的全文搜索功能；
* 专为高通量的网络流量进行的优化；
* 基于开放接口（XML和HTTP）的标准；
* 综合的HTML管理界面；
* 可伸缩性－能够有效地复制到另外一个Solr搜索服务器；
* 使用XML配置达到灵活性和适配性；
* 可扩展的插件体系。
## solr的概念词语
Collection：在SolrCloud集群中逻辑意义上的完整的索引。它常常被划分为一个或多个 Shard，它们使用相同的Config Set。如果Shard数超过一个，它就是分布式索引，SolrCloud让你通过Collection名称引用它，而不需要关心分布式检索时需要使用的和Shard相关参数。
ConfigSet: Solr Core提供服务必须的一组配置文件。每个config set有一个名字。最小需要包括solrconfig.xml(SolrConfigXml)和schema.xml (SchemaXml)，除此之外，依据这两个文件的配置内容，可能还需要包含其它文件。它存储在Zookeeper中。Config sets可以重新上传或者使用upconfig命令更新，使用Solr的启动参数bootstrap_confdir指定可以初始化或更新它。
Core: 也就是Solr Core，一个Solr中包含一个或者多个Solr Core，每个Solr Core可以独立提供索引和查询功能，每个Solr Core对应一个索引或者Collection的Shard，Solr Core的提出是为了增加管理灵活性和共用资源。在SolrCloud中有个不同点是它使用的配置是在Zookeeper中的，传统的Solr core的配置文件是在磁盘上的配置目录中。
Leader: 赢得选举的Shard replicas。每个Shard有多个Replicas，这几个Replicas需要选举来确定一个Leader。选举可以发生在任何时间，但是通常他 们仅在某个Solr实例发生故障时才会触发。当索引documents时，SolrCloud会传递它们到此Shard对应的leader，leader再分发它们到全部Shard的replicas。
Replica: Shard的一个拷贝。每个Replica存在于Solr的一个Core中。一个命名为“test”的collection以numShards=1创 建，并且指定replicationFactor设置为2，这会产生2个replicas，也就是对应会有2个Core，每个在不同的机器或者Solr实 例。一个会被命名为test_shard1_replica1，另一个命名为test_shard1_replica2。它们中的一个会被选举为 Leader。
Shard: Collection的逻辑分片。每个Shard被化成一个或者多个replicas，通过选举确定哪个是Leader。
Zookeeper: Zookeeper提供分布式锁功能，对SolrCloud是必须的。它处理Leader选举。Solr可以以内嵌的Zookeeper运行，但是建议用独立的，并且最好有3个以上的主机。

## solr整体架构
### 总体架构
![](https://i.imgur.com/h8ZmT5D.png)
### 内部结构
![](https://i.imgur.com/0gjq7aS.png)
### shard结构
![](https://i.imgur.com/TSGgh8a.png)
### 索引的创建（写操作）
![](https://i.imgur.com/HECtWap.png)
分布式索引的过程如下：
* 用户可以把创建文档索引的请求提交给任一个Replica或Leader
* 如果它不是Leader,它会把请求转交给和自己同Shard的Leader
* Leader把文档路由给本Shard的每个Replica，各自做索引
* 如果文档基于路由规则并不属于本Shard,Leader会把它转交给该文档对应Shard的Leader
* 对应的Leader会把文档路由给本Shard的每个Replica，各自做索引
### 搜索（读操作）
![](https://i.imgur.com/B67ekH0.png)
分布式搜索过程如下：
* 用户的一个查询请求，可以发送到含有该Collection的任意机器，Solr的内部处理逻辑会转到一个Replica
* 此Replica会基于查询索引的方式，启动分布式查询，基于索引的Shard个数，把查询转换为多个子查询，并把每个子查询定位到对应Shard的任意一个Replica
* 每个子查询返回查询结果
* 最初的Replica合并每个子查询的查询结果，并把最终结果返回给用户

## solr的配置文件
### schema.xml（managed-schema）
schema.xml这个配置文件可以在你下载solr包的安装解压目录的\solr\example\solr\collection1\conf中找到，它就是solr模式关联的文件。
在solr6.x版本后，schema.xml更改为了managed-schema，里面内容是一样的。
#### Field
定义Solr索引的document里的字段，这个一般是根据具体的搜索应用来定义需要有什么字段。有三个特殊一点的字段，分别是：”version“, “root” 和 “id”。

| 属性           | 默认值         | 说明  |
| ------------- |-------------|:-----|
| name          |无             | 必须   |
| type          |无             | 必须，值为定义的`<fieldType>`|
| indexed       | true          | 是否进行索引。 true的时候进行索引|
| stored        | true          | 是否存储。如果此字段的值需要显示在搜索结果中，则需要进行存储。|
| docValues     | false         | 是否需要存储docValues。true为设置。docValues用于提升sorting, faceting, grouping, function queries等性能，现在仅支持StrField, UUIDFiel和所有的Trie*Field，此值为true的字段要求此字段multiValued=false，并且 (required=true或设置了default的值).|
| multiValued   | false         | 是否有多个值|
| omitNorms     |无             | 如果你的大部分的document的长度大小都差不多，则设置成true。如果此字段在索引时需要boost，则设置为false.|
| termVectors   | false         | 设置为true，使More Like This特性生效，会极大的增加索引文件的大小|
| termPositions	| false         | 通常用于提高高亮搜索结果这一功能的性能。设置为true，会增加索引文件的大小|
| termOffsets   | fasle         | 通常用于提高高亮搜索结果这一功能的性能。设置为true，会增加索引文件的大小|
| termPayloads  | fasle         | 通常用于提高高亮搜索结果这一功能的性能。设置为true，会增加索引文件的大小|
| required      | fasle         | 如果设置为true，则索引时，如果此字段值为null，则会报错|
| default       |无             | 此字段是默认字段|

* 将所有只用于搜索的，而不需要作为结果的field（特别是一些比较大的field）的stored设置为false
* 将不需要被用于搜索的，而只是作为结果返回的field的indexed设置为false
* 删除所有不必要的copyField声明
* 为了索引字段的最小化和搜索的效率，将所有的 text fields的index都设置成field，然后使用copyField将他们都复制到一个总的 text field上，然后对他进行搜索。
* 为了最大化搜索效率，使用java编写的客户端与solr交互（使用流通信）
* 在服务器端运行JVM（省去网络通信），使用尽可能高的Log输出等级，减少日志量。

#### Fieldtype
Fieldtype：就是属性类型的意思，像int，String，Boolean种类型，同时也可以自己添加属性类型，例如添加中文分词类型text_ik（ikanalyzer，支持自定义扩展词典与停止词词典），则需要添加
```
<fieldType name="text_ik" class="solr.TextField">
	<analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>  
</fieldType>
```
若使用英文分词，FieldType选择自带的text_en，俄文分词选择自带的text_ru。
#### CopyField
> 用百度或google搜索时，我们可能想要搜索一个人名，或者书名，或者网站的名字，其后台索引文件里分别由不同的field去保存那些值，那它是如何用一个输入框去搜索不同的field的内容的呢？答案就是`<copyField>`

### Solrconfig.xml
>这个配置文件内容有点多,主要内容有:使用的lib配置,包含依赖的jar和Solr的一些插件;组件信息配置;索引配置和查询配置

#### 索引配置
mainIndex 标记段定义了控制Solr索引处理的一些因素.
* useCompoundFile：通过将很多 Lucene 内部文件整合到单一一个文件来减少使用中的文件的数量。这可有助于减少 Solr 使用的文件句柄数目，代价是降低了性能。除非是应用程序用完了文件句柄，否则false 的默认值应该就已经足够。
* useCompoundFile：通过将很多Lucene内部文件整合到一个文件，来减少使用中的文件的数量。这可有助于减少Solr使用的文件句柄的数目，代价是降低了性能。除非是应用程序用完了文件句柄，否则false的默认值应该就已经足够了。
* mergeFacor：决定Lucene段被合并的频率。较小的值（最小为2）使用的内存较少但导致的索引时间也更慢。较大的值可使索引时间变快但会牺牲较多的内存。（典型的 时间与空间 的平衡配置）
* maxBufferedDocs：在合并内存中文档和创建新段之前，定义所需索引的最小文档数。段 是用来存储索引信息的Lucene文件。较大的值可使索引时间变快但会牺牲较多内存。
* maxMergeDocs：控制可由Solr合并的 Document 的最大数。较小的值（<10,000）最适合于具有大量更新的应用程序。
* maxFieldLength：对于给定的Document，控制可添加到Field的最大条目数，进而阶段该文档。如果文档可能会很大，就需要增加这个数值。然后，若将这个值设置得过高会导致内存不足错误。
* unlockOnStartup：告知Solr忽略在多线程环境中用来保护索引的锁定机制。在某些情况下，索引可能会由于不正确的关机或其他错误而一直处于锁定，这就妨碍了添加和更新。将其设置为true可以禁用启动索引，进而允许进行添加和更新。（锁机制）

#### 查询处理配置
query标记段中以下一些与缓存无关的特性：
* maxBooleanClauses：定义可组合在一起形成以个查询的字句数量的上限。正常情况1024已经足够。如果应用程序大量使用了通配符或范围查询，增加这个限制将能避免当值超出时，抛出TooMangClausesException。
* enableLazyFieldLoading：如果应用程序只会检索Document上少数几个Field，那么可以将这个属性设置为true。懒散加载的一个常见场景大都发生在应用程序返回一些列搜索结果的时候，用户常常会单击其中的一个来查看存储在此索引中的原始文档。初始的现实常常只需要现实很短的一段信息。若是检索大型的Document，除非必需，否则就应该避免加载整个文档。

query部分负责定义与在Solr中发生的时间相关的几个选项：
> 概念：Solr（实际上是Lucene）使用称为Searcher的Java类来处理Query实例。Searcher将索引内容相关的数据加载到内存中。根据索引、CPU已经可用内存的大小，这个过程可能需要较长的一段时间。要改进这一设计和显著提高性能，Solr引入了一张“温暖”策略，即把这些新的Searcher联机以便为现场用户提供查询服务之前，先对它们进行“热身”。

* newSearcher和firstSearcher事件，可以使用这些事件来制定实例化新Searcher或第一个Searcher时，应该执行哪些查询。如果应用程序期望请求某些特定的查询，那么在创建新Searcher或第一个Searcher时就应该反注释这些部分并执行适当的查询。

query中的智能缓存：
* filterCache：通过存储一个匹配给定查询的文档 id 的无序集，过滤器让 Solr 能够有效提高查询的性能。缓存这些过滤器意味着对Solr的重复调用可以导致结果集的快速查找。更常见的场景是缓存一个过滤器，然后再发起后续的精炼查询，这种查询能使用过滤器来限制要搜索的文档数。
* queryResultCache：为查询、排序条件和所请求文档的数量缓存文档 id 的有序集合。
* documentCache：缓存Lucene Document，使用内部Lucene文档id（以便不与Solr唯一id相混淆）。由于Lucene的内部Document id 可以因索引操作而更改，这种缓存不能自热。
* Named caches：命名缓存是用户定义的缓存，可被 Solr定制插件 所使用。

其中filterCache、queryResultCache、Named caches（如果实现了org.apache.solr.search.CacheRegenerator）可以自热。

每个缓存声明都接受最多四个属性：

* class：是缓存实现的Java名
* size：是最大的条目数
* initialSize：是缓存的初始大小
* autoWarmCount：是取自旧缓存以预热新缓存的条目数。如果条目很多，就意味着缓存的hit会更多，只不过需要花更长的预热时间。


对于所有缓存模式而言，在设置缓存参数时，都有必要在内存、cpu和磁盘访问之间进行均衡。统计信息管理页（管理员界面的Statistics）对于分析缓存的 hit-to-miss 比例以及微调缓存大小的统计数据都非常有用。而且，并非所有应用程序都会从缓存受益。实际上，一些应用程序反而会由于需要将某个永远也用不到的条目存储在缓存中这一额外步骤而受到影响。

## solr安装
### 准备工作
* jdk1.9
* solr7.2
* zookeeper3.4.11

1. 安装jdk，安装zookeeper在这里就不多说了，常规配置安装即可。
2. 安装solr，官方下载地址：`wget http://archive.apache.org/dist/lucene/solr/7.2.0/solr-7.2.0.tgz`，若嫌速度慢可以通过阿里镜像地址下载：`wget https://mirrors.aliyun.com/apache/lucene/solr/7.2.0/solr-7.2.0.tgz`
3. `tar zxvf solr-7.2.0.tgz` 进行解压
4. `cd solr-7.2.0.tgz/bin` ，修改`solr.in.sh`文件，将`ZK_HOST`修改实际地址：

```
# Set the ZooKeeper connection string if using an external ZooKeeper ensemble
# e.g. host1:2181,host2:2181/chroot
# Leave empty if not using SolrCloud
ZK_HOST="10.8.2.114:2181,10.8.2.115:2181,10.8.2.116:2181"

# Set the ZooKeeper client timeout (for SolrCloud mode)
ZK_CLIENT_TIMEOUT="15000"

```
### 设置自动提交
在solr中，有硬提交和软提交两种提交方式，其中硬提交是将数据持久化到磁盘里，并能查询到该数据。在新版本的solr中，硬提交是默认开启的（solrconfig.xml）：
```
<autoCommit>
	<!--表示在软提交成功后的15000ms时会自动进行一次硬提交-->
	<maxTime>${solr.autoCommit.maxTime:15000}</maxTime>
	<!--表示软提交达到1万条的时候会自动进行一次硬提交,maxTime和maxDocs满足其中一条就会执行硬提交-->
	<maxDocs>10000</maxDocs>
	<openSearcher>false</openSearcher>
</autoCommit>
```
软提交是指将数据提交到内存中，并没有持久化到磁盘，但是会把提交的记录写到tlog的日志文件里面,在正常关闭solr 服务的时候，solr会自动执行一次硬提交，在solr 重启的时候会自动加载tlog的日志文件。下面就是自动软提交的配置，不需要自己维护提交（默认没有开启）：
```
<autoSoftCommit>
	<!--表示每10s执行一次软提交，默认为-1不开启软提交-->
	<maxTime>${solr.autoSoftCommit.maxTime:10000}</maxTime>
</autoSoftCommit>
```

### 设置多个configsets
将/solr-7.2.0/server/solr/configsets中的_default目录在同级内复制一份为myConfig。
### 与数据库关联
修改myConfig中的solrconfig.xml，新增如下内容：
```
<requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">    
	<lst name="defaults">    
		<str name="config">data-config.xml</str>    
	</lst>    
</requestHandler>
```
在同级目录中新增`data-config.xml`文件，内容如下：
```
<?xml version="1.0" encoding="utf-8"?>    
<dataConfig>  
 <dataSource type="JdbcDataSource" driver="com.mysql.jdbc.Driver"    
   url="jdbc:mysql://10.8.2.40:3306/solrcloud"    
   user="root"    
   password="root"/>  
   <document name="prod">  
      <entity name="product" pk="product_id" query="select product_id,product_name,product_type,product_content,product_price from product">    
          <field column="product_id" name="product_id" />    
          <field column="product_name" name="name" />    
          <field column="product_type" name="type" />  
          <field column="product_content" name="content" />  
          <field column="product_price" name="price" />   
      </entity>  
 </document>   
</dataConfig>  
```
其中url为数据库地址，user/password为数据库的账户密码。document.name随意，entity.name为表名，pk为表内主键，query为要导入数据的sql，field.colume为表内字段，field.name为solr中存储的字段（须在`managed-schema`文件中定义）。
在`managed-schema`文件中增加所需字段：
```
<field name="product_id" type="string" indexed="true" stored="true" />
<field name="name" type="string" indexed="true" stored="true" />
<field name="type" type="string" indexed="true" stored="true"/>
<field name="content" type="string" indexed="true" stored="true"/>
<field name="price" type="pfloat" indexed="true" stored="true"/>
<field name="name_content" type="text_ik" indexed="true" stored="true" multiValued="true"/>

<copyField source="name" dest="name_content"/>
<copyField source="content" dest="name_content"/>
```
将`/dist`目录下的`solr-dataimporthandler-7.2.0.jar`和自己下载的数据库链接驱动`mysql-connector-java-5.1.45-bin.jar`放入`/server/solr-webapp/webapp/WEB-INF/lib/`目录中。
### 设置中文分词
在`managed-schema`配置文件中，增加如下内容：
```
<fieldType name="text_ik" class="solr.TextField">
	<analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>  
</fieldType>
```
下载最新的`ikanalyzer`包（ik分词器在2012年后就不在更新了，所以只找到了`ik-analyzer-solr5-5.x`版本），解压后将`ext.dic`，`IKAnalyzer.cfg.xml`，`stopword.dic`配置文件复制到/conf目录下，将`ik-analyzer-solr5-5.x.jar`，`solr-analyzer-ik-5.1.0.jar`复制到`server/solr-webapp/webapp/WEB-INF/lib`目录下。
### 启动solr
solr有两种方式，一种是单机版，一种是云版，启动方式都相同，皆为在`solr-7.2.0/bin/`目录下，使用`solr start -force`方式启动，若没有配置第4步，则为单机版，反之为云版。系统第一次启动时，会将`configsets/_default`更新到zookeeper中并作为默认配置文件，需执行
```
zkcli.sh -zkhost 10.8.2.114:2181,10.8.2.115:2181,10.8.2.116:2181 -cmd upconfig -confdir /home/solr/solr-7.2.0/server/solr/configsets/myConfig/conf/ -confname myConfig
```
命令将自定义配置文件更新到zookeeper中。

## solr应用
### 创建Collection
在Collections中点击`Add Collection`（相当于数据库的表），输入对应信息：
![](https://i.imgur.com/ekchyqA.png)
总体上说，当节点数和Shard数相等时，ElasticSearch集群的性能可以达到最优。即，对于一个3节点集群，我们为每个集群节点分配一个Shard，总共3个Shard。但是由于ElasticSearch的不可变性（Immutable）的限制，系统无法对Shard进行重新拆分分配，除非重新索引这个文件集合。所以，当我们需要增加更多节点的时候，又希望Shard能利用到增加节点带来的系统性能提升时，我们就不得不进行重新索引，由于重索引开销巨大，这是我们不希望看到的。
如果需要重新建立索引，将会是一个巨大的开销，为了支持未来可能的水平扩展，我们会为集群分配比node数更多的shard数，也就是说每个节点会有多个Shard。
如果单个node分配多个shard，就会引入另外一系列的性能问题，我们知道对于任意一次完整的搜索，ElasticSearch会分别对每个shard进行查询，最后进行汇总。当节点数和shard数是一对一的时候，所有的查询可以并行运行。但是，对于具有多个shard的节点，如果磁盘是15000RPM或SSD，可能会相对较快，但是这也会存在等待响应的问题，所以通常不推荐一个节点超过2个shard。
3节点6shard，即每个节点2shard，这可以使我们在未来轻松的横向扩展到6个节点，应对许多极端的场景。

创建成功后可在`Cloud`或`Collections`中查看
`Cloud`
![Cloud](https://i.imgur.com/QisssTU.png)
`Collections`
![Collections](https://i.imgur.com/tYw44cB.png)

### 添加Field
Field相当于数据库中表的字段，例如我们创建的商品Collection，那么它的属性就有prodName，prodDesc，prodPrice，prodType等，其中prodName，prodDesc我们是希望搜索时可以通过关键字进行模糊搜索的,并且要求关键字搜索时，是同时从这两个字段中搜索（此时用到`copyField`和`multiValued`），而prodPrice与prodType是需要精确搜索的。
添加Field有两种方式，一种为界面上操作，一种是直接修改`managed-schema`文件。
* 在界面左侧`Collection Select`下拉框中选择我们`productCollection`，点击`Schema`,首先我们要先新增字段（Add Field）
![](https://i.imgur.com/1YEKSzW.png)
* 在`managed-schema`文件中添加所需字段，需注意的是，在`copyField`字段中需要把属性`multiValued`设成`true`。

```
<field name="prodName" type="string" indexed="true" stored="true"/>
<field name="prodDesc" type="string" indexed="true" stored="true"/>
<field name="prodType" type="string" indexed="true" stored="true"/>
<field name="prodPrice" type="pfloat" indexed="true" stored="true"/>
<field name="name_desc" type="text_ik" indexed="true" stored="true" multiValued="true"/>

<copyField source="prodName" dest="name_desc"/>
<copyField source="prodDesc" dest="name_desc"/>
```
在本地修改完配置文件后，需要更新到zookeeper中，或需新增一个配置，命令如下：
```
zkcli.sh -zkhost 10.8.2.114:2181,10.8.2.115:2181,10.8.2.116:2181 -cmd upconfig -confdir /home/solr/solr-7.2.0/server/solr/configsets/myConfig/conf/ -confname myConfig
```

**<font color="red">注：由页面操作添加的field等，只会更新到zookeeper中，不会更新到本地文件，若先使用界面操作添加了field，后又使用修改本地配置文件更新zookeeper，会出现问题。</font>**

### 添加数据
可以通过界面Documents添加：
![](https://i.imgur.com/dWyxm9c.png)
此时会自动将prodName，prodDesc的值存入name_desc字段中。
### 查询数据
可以通过界面Query查询：
![](https://i.imgur.com/uQKfqMM.png)
### 从数据库中导入数据
首先需要建一个使用自定义配置文件的collection，选择该collection后，点击Dataimport按钮，进入导入数据页面。
![](https://i.imgur.com/UztjxhY.png)
* command中包括full-import（全量导入）/delta-import（增量导入）
* Clean代表导入前清空原有索引（选择增量导入时不要选Clean，否则会将之前的数据删除掉）
* Commit代表执行后自动commit，若勾选了Debug选项，则需要手动commit
* Optimize代表做完该操作后是否需要优化

点击Execute后，需手动点击`Refresh Status`，或者勾选`Auto-Refresh Status`来刷新状态。

### 分割shard
当索引达到一定的数量级的时候，搜索的速度或许就会达到一个瓶颈，因为数据量一旦增多，各种查询开销也会一并增加，在solrcloud中，创建索引的时候我们一般都只会创建符合当前性能需求的分片数量，但是数据假如是不可预期的增长的话，那么当当个shard变的很大的时候，我们就可以对这个shard进行拆分，使单个shard中的数据量变少，这也是集群纵向扩展的常用方法。
比如说我们要对collection1的shard1进行分割，只需要访问`http://10.8.2.114:8983/solr/admin/collections?action=SPLITSHARD&collection=collection1&shard=shard1`即可。
![](https://i.imgur.com/ZgkPBKK.png)
原本的shard1会变成`inactive`状态，新增shard1_0，和shard1_1，在collections中可以将处于`inactive`状态的shard删除。
![](https://i.imgur.com/uJb4iLR.png)
## 在程序中使用
### 添加依赖
首先需要在pom文件中添加依赖：

```
<dependency>
    <groupId>org.apache.solr</groupId>
    <artifactId>solr-solrj</artifactId>
    <version>7.2.0</version>
</dependency>
```

### 获取solr客户端

```
CloudSolrClient.Builder builder = new  CloudSolrClient.Builder();
CloudSolrClient client =  builder.withZkHost("10.8.2.114:2181,10.8.2.115:2181,10.8.2.116:2181").build();
client.setDefaultCollection("productCollection");
```
### 添加索引
```
SolrInputDocument document = new SolrInputDocument();
document.addField("prodName","华为手机");
document.addField("prodDesc","就是好用，没啥好说的");
document.addField("prodType","0");
document.addField("prodPrice",4000.000);
client.add(document);
client.commit();
```
### 查询索引
```
Map<String, String> queryParamMap = new HashMap<String, String>();
queryParamMap.put("q", "手机系统");
queryParamMap.put("df", "name_desc");
//[3]添加到SolrParams对象
MapSolrParams queryParams = new MapSolrParams(queryParamMap);
QueryResponse response =  client.query(queryParams);
SolrDocumentList documents = response.getResults();
long num = documents.getNumFound();
for(SolrDocument doc : documents){
    System.out.println("id:"+doc.get("id")+"\t"+"name:"+doc.get("prodName"));
}
```
### 删除单个/多个索引（根据id删除）
```
client.deleteById(id/ids);
client.commit();
```
### 删除指定条件的索引
```
client.deleteByQuery("*:*");
client.commit();

```
### 增量/全量建立索引
```
SolrQuery query = new SolrQuery();
query.setRequestHandler("/dataimport");
query.setParam("command","full-import");
query.setParam("clean","true");
query.setParam("optimize","true");
query.setParam("entity","product");
query.setParam("commit","true");
client.query(query);
```