
#mapReduce

##mapReduce是用于数据分析、统计的函数
```
db.collection.mapReduce(
                         (map),
                         (reduce),
                         (options):{
                           out: <collection>,
                           query: <document>,
                           sort: <document>,
                           limit: <number>,
                           finalize: <function>,
                           scope: <document>,
                           jsMode: <boolean>,
                           verbose: <boolean>,
                           bypassDocumentValidation: <boolean>
                         }
                       )

```
###参数：
- **map** ：(function)  用emit函数发出(key, value)键值对,value是一个集合
- **reduce** ：(function)  把所有map函数中发出的键值对按照key的值聚合为(key,values),values是key相同的value的数组
- **option** ：(document)  控制mapReduce过程的参数

###option参数:
- **query** ：  (document)   集合的查询参数,map函数使用.
- **sort** ：  (document)    集合按该字段排序,必须存在.设置该字段和emit发出的key字段相同可以提高map函数执行效率
- **limit** ：  (number)     设置最大数量的集合数
- **finalize** ： (function) 用户对mapreduce最终结果的修改
- **scope** ：  (document)   mapreduce过程中可以访问到的对象集合
- **out** ：      (string)      统计结果存放的表名
- **jsMode** ：  (Boolean)     是否减少执行过程中BSON和JS的转换，默认为true
- **verbose** ：verbose    (Boolean)     是否显示详细的时间统计信息,默认为true

## 用法如下:

一个存放店家订单的文档,要统计一段时间内每个店完成的订单数量和总价格
###订单表格式:
```
{
    "_id" : ObjectId("56944bc9b78fe0f714d7ff32"),
    "price" : 20,
    "shopid" : "29000007",
    "orderId" : "D290000071600020100",
    "createTime" : ISODate("2016-01-12T08:41:45.872Z")
}
```

### 代码块
```
var map = function () {
            emit(this.shopid, {
                counts: 0,
                price: 0
            });
            emit(this.shopid, {
                counts: 1,
                price: this.price
            });
        };
```

> **注意：**emit函数执行两次是因为如果只有一条数据的情况下不执行reduce函数，这个问题不清楚是不是mapReduce故意为之, 真* * *别扭

```
        var reduce = function (key, values) {

            var result = {
                counts: 0,
                price: 0
            };

            //数据聚合
            values.forEach(function (value) {
               result.counts += value.counts;
               result.price += value.price;
            });

            return result;
        };

        collection.mapReduce(map, reduce, {
                //输出结果
                out: {replace: 'shopStatistics'},
                scope: {},
                //查询start到end这段时间内的订单
                query: {createTime: {$gt: start, $lt: end}}
            },
            function (err, collection) {
                if (err) {
                    deferred.reject(new Error(err));
                } else {
                    collection.find().toArray(function (err, results) {
                        if (err) {
                            deferred.reject(new Error(err));
                        } else {
                            deferred.resolve(results);
                        }
                    });

                }
        });
```



##吐槽一下mapReduce的坑

###问题提出
在reduce函数中values并不是一个连续的数组，而是一个链表结构的，每个节点存储100个键值对，每个节点有一个索引指向下一个节点。

> **结构：**values --> [100个键值对] --> [100个键值对] --> [53个键值对]

在reduce函数中聚合操作是这样写的：
```
values.forEach(function (value) {
               result.counts += value.counts;
               result.price += value.price;
            });

```
这种情况下不会出现问题。问题稍作修改假设只统计订单金额大于50的订单，代码改成这样


```
values.forEach(function (value) {
              if(result.price > 50){
                     result.counts += value.counts;
                     result.price += value.price;
              }
            });
```


理论上不会对mapReduce的结果产生影响，实践证明在reduce函数中丢掉部分数据，通过代码的跟踪调试发现这样的规律，假设数据如下，mapReduce过程按照shopId排序：
- **shop001**    70个订单
- **shop002**    20个订单
- **shop003**    50个订单

此时计算shop001、shop002的结果是正确的，但是shop003只计算了后面40个订单的值    10 == 100 -（70 + 20）  50 - 10 = 40

###结论：每逢reduce数据横跨100的倍数就会产生问题

> **猜想：**  在mapReduce的执行过程中对shop003的聚合函数执行了两次，且后一次覆盖的前一次的结果，这个问题搜索了很多论坛，有多人提出过这个问题，但是目前没看到解决的办法。

当然在这个简单的问题处理上通过修改query参数能轻松解决，但是涉及到mapReduce的表结果稍微复杂，或者表设计时候就存在一些判断逻辑，这个问题无法绕过去。哪位大牛解决过这个问题，务必不吝赐教。




当然程序员不能在一棵树上吊死，替代办法：
###聚合管道aggregate

