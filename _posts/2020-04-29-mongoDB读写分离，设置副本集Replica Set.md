---
layout:     post
title:      mongoDB读写分离，设置副本集Replica Set
subtitle:   node.js读写分离
date:       2020-04-29
author:     Kason
header-img: img/articleBg/db1.jpg
catalog: true
tags:
    - 数据库
    - mongoDB
    - Linux
---

>mongoDB读写分离，设置副本集Replica Set



环境：ubuntu
mongodb版本：3.6.3
主节点IP：192.168.199.128
副节点IP:192.168.199.129
仲裁节点IP:192.168.199.130

ubuntu安装mongodb过程省略，网上太多文章了。
安装完成后，现在我们分别配置两台机器的mongod.conf文件，
```
vim /etc/mongod.conf
```
配置内容：
```
# mongodb.conf

# Where to store the data.
dbpath=/var/lib/mongodb

#where to log
logpath=/var/log/mongodb/mongodb.log

logappend=true
#IP要换成本机对应的IP
bind_ip = 192.168.199.128
port = 27017
fork = true
replSet = myrs
# Enable journaling, http://www.mongodb.org/display/DOCS/Journaling
journal=true

# Enables periodic logging of CPU utilization and I/O wait
#cpu = true

#后面不重要的直接省略！！！

```
分别在每台机器上启动数据库
```
mongod -f /etc/mongodb.conf 
```
2台机器要都启动哦。

登录任意一台机器的mongodb后台管理shell：
```
mongo 192.168.199.128
//如果要选定数据库启动也可以这样
mongo 192.168.199.128/数据库名

```
###初始化副本集
```
use admin

onfig = {
"_id":"myrs",
"members":[
  {"_id":0,"host":"192.168.199.128:27017"},
  {"_id":1,"host":"192.168.199.129:27017"},
  {"_id":2,"host":"192.168.199.130:27017",arbiterOnly:true}
]
}

rs.initiate(config);   #初始化配置
```
注意：如果执行rs.initiate(config)报错，那么我们需要检查三台服务器的防火墙27017端口是否开放。 如果没有问题，我们可以查看集群节点：

```
rs.status();
```
至此，整个副本集已经搭建成功了。
那么，我们可以测试一下副本集的基本功能：
### 1、测试副本集的数据复制功能
此时在我的机器上192.168.199.128是主节点，我们用mongo来登录shell。
```
mongo 192.168.199.128#连接主数据库

use test  #创建test数据库
db.testCollection.insert({"title":"new"});       #插入数据
```
我们在另一个主机，登录副本shell，我这里是192.168.199.129：
```
mongo 192.168.199.129#连接主数据库
use test
show tables

此时会报错：

[thread1] Error: listCollections failed: {
"ok" : 0,
"errmsg" : "not master and slaveOk=false",
"code" : 13435,
"codeName" : "NotMasterNoSlaveOk"
} 
```
因为mongodb默认是从主节点读写数据的，副本节点上不允许读，需要设置副本节点可以读。
```
myrs:SECONDARY> db.getMongo().setSlaveOk();
```
此时就可以读取数据了：
```
repset:SECONDARY> db.testdb.find();

控制台输出：
{ "_id" : ObjectId("5ea912dcbbf5cef49fdbceac"), "title" : "new" }

```
所以，数据复制的功能是可用的。

2、测试副本集的故障转移功能
将主节点192.168.198.224的mongod进程杀死：
```
myrs:PRIMARY> use admin
myrs:PRIMARY> db.shutdownServer()
```
此时可以看到，192.168.199.129原本是副节点，此时自动转换为主节点。可以通过rs.status()来查看。
你可能还会碰到无法关闭mongodb的情况：
```
myrs:PRIMARY> use admin
switched to db admin
myrs:PRIMARY> db.shutdownServer();
2020-04-28T23:05:49.842-0700 E QUERY    [thread1] Error: shutdownServer failed: {
	"operationTime" : Timestamp(1588140343, 1),
	"ok" : 0,
	"errmsg" : "shutdown must run from localhost when running db without auth",
	"code" : 13,
	"codeName" : "Unauthorized",
	"$clusterTime" : {
		"clusterTime" : Timestamp(1588140343, 1),
		"signature" : {
			"hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
			"keyId" : NumberLong(0)
		}
	}
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype.shutdownServer@src/mongo/shell/db.js:453:1
@(shell):1:1

````
errmsg的意思就是，没有权限控制的情况下，只能用loaclhost方式关闭。
所以我们可以献给mongodb设置权限访问；
参考：
[https://www.cnblogs.com/yupixiu/p/10320472.html](https://www.cnblogs.com/yupixiu/p/10320472.html)

然后kill -2 mongo的PID先这样关闭Mongo服务。
再修改/etc/mongod.conf
新曾一条配置:
```
auth = true
```
重启mongodb
```
mongod -f /etc/mongodb.conf 
```
进入admin 然后验证权限，然后关闭主mongodb数据库
```
use admin
db.auth('root','123456');
db.shutdownServer();
```
去副数据库看状态,正常状态下会切换为Primary数据库
```
db.status()
```
如果副本一直是RECOVERING的状态：
```
	"members" : [
		{
			"_id" : 0,
			"name" : "192.168.199.128:27017",
			"health" : 0,
			"state" : 6,
			"stateStr" : "(not reachable/healthy)",
			"uptime" : 0,
			"optime" : {
				"ts" : Timestamp(0, 0),
				"t" : NumberLong(-1)
			},
			"optimeDurable" : {
				"ts" : Timestamp(0, 0),
				"t" : NumberLong(-1)
			},
			"optimeDate" : ISODate("1970-01-01T00:00:00Z"),
			"optimeDurableDate" : ISODate("1970-01-01T00:00:00Z"),
			"lastHeartbeat" : ISODate("2020-04-29T06:33:36.674Z"),
			"lastHeartbeatRecv" : ISODate("2020-04-29T06:33:36.695Z"),
			"pingMs" : NumberLong(0),
			"authenticated" : false,
			"configVersion" : -1
		},
		{
			"_id" : 1,
			"name" : "192.168.199.129:27017",
			"health" : 1,
			"state" : 3,
			"stateStr" : "RECOVERING",
			"uptime" : 3524,
			"optime" : {
				"ts" : Timestamp(1588140952, 1),
				"t" : NumberLong(2)
			},
			"optimeDate" : ISODate("2020-04-29T06:15:52Z"),
			"infoMessage" : "could not find member to sync from",
			"configVersion" : 1,
			"self" : true
		}
```
可以参考：
[https://dba.stackexchange.com/questions/77881/mongo-db-replica-set-stuck-at-recovering-state](https://dba.stackexchange.com/questions/77881/mongo-db-replica-set-stuck-at-recovering-state)
