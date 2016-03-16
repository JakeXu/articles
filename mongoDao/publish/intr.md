##MongoDB

[MongoDB](http://docs.mongoing.com/manual-zh/index.html) 是 NoSQL 的代表之一,其采用 **JSON/BSON**当做数据储存格式，亦使用 JavaScript 做为 Server-side 的执行语言（相当于PLSQL）。它的这些特性，使得其成为NodeJs的推荐搭配数据库。
在结合业务开发的需要，对MongoDB的driver做了封装，以适合快速的业务开发需要和更加的面向对象。

####封装思路及要求如下：
> - 在官方driver的基础上，封装一个Dao层，该Dao层提供统一的接口规范，以屏蔽不同driver的接口变动对业务层的影响。
> - 利用封装后的Dao层，构建BaseService，后续所有的业务Service 继承该类，以提供基础CRUD需求，增加代码的复用性。
> - BaseService层中，注入的Dao实列，在满足接口规范的情况下，可随意进行其他数据库Dao层的切换(如：mySql)。
> - 需要考虑服务的高可用性，目前业务数据量暂不考虑分片，故需要引入Replica Set的方式。
> - 发挥NoSql数据库的精髓，给予表结构充分的自由，不用像mongoose那样的Schema Model，当然这使的你在开发的时候需要当心一些。
> - 需要引入Promise，来增加代码的复用性，同时避免回调地狱。我们选用的Promise第三方库是 [Q](https://www.npmjs.com/package/q)

####我们现在使用的MongoDB3.0的特性：
> - 数据存储的格式是BSON。
> - 文档级别并发控制，3.0以前是库级别。
> -  缺少事物支持，需要二段提交的方式来解决。
> -  HA支持3种工作方式：Master-Slave, Replica Set, Sharding。
> - 支持动态查询。支持完全索引，包含内部对象。

####使用MongoDB的注意点：
> - 在国内服务器存在一个时区的问题，可能需要我们手动的加上8个小时的时差。在我们的框架中默认对save操作会增加一个createTime字段，对update操作会增加一个updateTime字段，它们都有做加8小时的处理。
> - 由于却少数据模型的约束，所以会给开发者极大的自由，在整个的开发过程中，会非常的爽，但是这可能会导致很多隐性的bug存在，所以还是请使用者注意下规范，测试案列的编写必不可少。

