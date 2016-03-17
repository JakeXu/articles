##比较查询
**query( collection, selector )方法中的selector参数进行比较查询的时候的设置如下：**  

例如需要查询用户表中年龄大于25的所有人的数据信息
```
  var selector={age: {$gt: 25 }};
```
例如需要进行一个范围查询，年龄在大于20，小于30的所有人的数据信息
```
  var selector={age: {$gt: 20, $lt:30}};
```
在时间上的查询，也是可以做的，如下：
```
  var selector={activityStartTime: {$gt: new Date(), $lt:new Date(2015-01-01)}};
```
#####其中的$gt的意思就是“大于”，其它比较符如下：
|    符号    |    含义   |   
| :--------:| :--------: | 
|   $lt     |    <      |  
|   $lte   |    <=    |
|   $gt    |    >     |
|   $gte   |     >=   |
|   $ne    |     !=   |


