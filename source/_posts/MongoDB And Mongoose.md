---
title: MongoDB And Mongoose
date: 2019-05-30 00:24:50
categories: 
    - [Database, MongoDB]
tags:
---

## windows下安装mongoDB

点击[MongoDB](https://www.mongodb.com/download-center/community)下载并安装(不勾选安装MongoDB Compass，负责可能很长时间都在执行安装，在官网手动下载[MongoDB Compass](https://www.mongodb.com/download-center/compass)并安装)。默认安装在C:\Program Files\MongoDB下，在系统环境变量path下添加C:\Program Files\MongoDB\Server\4.0\bin路径。
打开命令行输入mongod

```
C:\Users\cookiepeace>mongod
2019-05-29T09:33:12.904-0700 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] MongoDB starting : pid=12044 port=27017 dbpath=C:\data\db\ 64-bit host=DESKTOP-2OOPGVS
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] targetMinOS: Windows 7/Windows Server 2008 R2
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] db version v4.0.9
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] git version: fc525e2d9b0e4bceff5c2201457e564362909765
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] allocator: tcmalloc
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] modules: none
2019-05-29T09:33:12.907-0700 I CONTROL  [initandlisten] build environment:
2019-05-29T09:33:12.908-0700 I CONTROL  [initandlisten]     distmod: 2008plus-ssl
2019-05-29T09:33:12.908-0700 I CONTROL  [initandlisten]     distarch: x86_64
2019-05-29T09:33:12.908-0700 I CONTROL  [initandlisten]     target_arch: x86_64
2019-05-29T09:33:12.908-0700 I CONTROL  [initandlisten] options: {}
2019-05-29T09:33:12.908-0700 I STORAGE  [initandlisten] exception in initAndListen: NonExistentPath: Data directory C:\data\db\ not found., terminating
2019-05-29T09:33:12.909-0700 I NETWORK  [initandlisten] shutdown: going to close listening sockets...
2019-05-29T09:33:12.909-0700 I CONTROL  [initandlisten] now exiting
2019-05-29T09:33:12.909-0700 I CONTROL  [initandlisten] shutting down with code:100
```
错误`initAndListen: NonExistentPath: Data directory C:\data\db\ not found., terminating`提示C:\data\db\路径不存在。

```
C:\Users\cookiepeace>md c:\data\db
```
输入该命令创建目录。然后再执行一次```mongod```
```
C:\Users\cookiepeace>mongod
2019-05-29T09:40:25.537-0700 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
2019-05-29T09:40:25.540-0700 I CONTROL  [initandlisten] MongoDB starting : pid=10184 port=27017 dbpath=C:\data\db\ 64-bit host=DESKTOP-2OOPGVS
2019-05-29T09:40:25.540-0700 I CONTROL  [initandlisten] targetMinOS: Windows 7/Windows Server 2008 R2
2019-05-29T09:40:25.541-0700 I CONTROL  [initandlisten] db version v4.0.9
2019-05-29T09:40:25.542-0700 I CONTROL  [initandlisten] git version: fc525e2d9b0e4bceff5c2201457e564362909765
2019-05-29T09:40:25.542-0700 I CONTROL  [initandlisten] allocator: tcmalloc
2019-05-29T09:40:25.542-0700 I CONTROL  [initandlisten] modules: none
2019-05-29T09:40:25.543-0700 I CONTROL  [initandlisten] build environment:
2019-05-29T09:40:25.543-0700 I CONTROL  [initandlisten]     distmod: 2008plus-ssl
2019-05-29T09:40:25.543-0700 I CONTROL  [initandlisten]     distarch: x86_64
2019-05-29T09:40:25.544-0700 I CONTROL  [initandlisten]     target_arch: x86_64
2019-05-29T09:40:25.544-0700 I CONTROL  [initandlisten] options: {}
2019-05-29T09:40:25.546-0700 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=7658M,session_max=20000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),statistics_log=(wait=0),verbose=(recovery_progress),
2019-05-29T09:40:25.570-0700 I STORAGE  [initandlisten] WiredTiger message [1559148025:569655][10184:140725009992784], txn-recover: Set global recovery timestamp: 0
2019-05-29T09:40:25.576-0700 I RECOVERY [initandlisten] WiredTiger recoveryTimestamp. Ts: Timestamp(0, 0)
2019-05-29T09:40:25.611-0700 I CONTROL  [initandlisten]
2019-05-29T09:40:25.611-0700 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2019-05-29T09:40:25.613-0700 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2019-05-29T09:40:25.615-0700 I CONTROL  [initandlisten]
2019-05-29T09:40:25.616-0700 I CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
2019-05-29T09:40:25.616-0700 I CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server.
2019-05-29T09:40:25.616-0700 I CONTROL  [initandlisten] **          Start the server with --bind_ip <address> to specify which IP
2019-05-29T09:40:25.617-0700 I CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
2019-05-29T09:40:25.619-0700 I CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
2019-05-29T09:40:25.619-0700 I CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.
2019-05-29T09:40:25.620-0700 I CONTROL  [initandlisten]
2019-05-30T00:40:25.621+0800 I STORAGE  [initandlisten] createCollection: admin.system.version with provided UUID: 42182d31-fb88-424e-b0aa-23a06e855858
2019-05-30T00:40:25.638+0800 I COMMAND  [initandlisten] setting featureCompatibilityVersion to 4.0
2019-05-30T00:40:25.646+0800 I STORAGE  [initandlisten] createCollection: local.startup_log with generated UUID: 6e6c1307-6143-40d2-b2b2-ec25120c968d
2019-05-30T00:40:25.913+0800 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory 'C:/data/db/diagnostic.data'
2019-05-30T00:40:25.916+0800 I NETWORK  [initandlisten] waiting for connections on port 27017
2019-05-30T00:40:25.916+0800 I STORAGE  [LogicalSessionCacheRefresh] createCollection: config.system.sessions with generated UUID: ad8615f7-b9f1-4b48-95e5-0717b02958d0
2019-05-30T00:40:25.932+0800 I INDEX    [LogicalSessionCacheRefresh] build index on: config.system.sessions properties: { v: 2, key: { lastUse: 1 }, name: "lsidTTLIndex", ns: "config.system.sessions", expireAfterSeconds: 1800 }
2019-05-30T00:40:25.932+0800 I INDEX    [LogicalSessionCacheRefresh]     building index using bulk method; build may temporarily use up to 500 megabytes of RAM
2019-05-30T00:40:25.936+0800 I INDEX    [LogicalSessionCacheRefresh] build index done.  scanned 0 total records. 0 secs
```
打开MongoDB Compass一路next后点击connect。

## Mongoose

### 连接MongoDB

```bash
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/myapp', {useNewUrlParser:true})
    .then(() => console.log('Connected to MongoDB...'))
    .catch(err => console.error('Connected failed', err));
```