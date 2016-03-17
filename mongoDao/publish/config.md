##配置参数设置
mongodb的配置参数设置在名为config.js的文件中,如下：

```
    //mongodb开发环境
    dbhost: '开发环境的数据库ip',
    dbport: '开发环境的数据库端口',
    //mongodb正式环境
    dbhost1: '副本集主节点ip',
    dbhost2: '副本集副节点ip',
    dbport1: '副本集主节点端口',
    dbport2: '副本集副节点端口',
    replSetName:'副本集名称',
    dbname: "数据库名",
    dbusername:'登录用户名',
    dbpwd: '登录密码',
```

我们采用了官方推荐的Replica Sets的方式启动mongodb，以保证服务的稳定性。
服务在启动的时候，如果是直接node index.js的方式启动，则它是以开发环境启动的，它连接的是开发环境的数据库。想以生产模式启动，需要以NODE_ENV=PRODUCTION node index.js的方式启动。

