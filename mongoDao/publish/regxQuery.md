##正则表达式的查询
**query( collection, selector )方法中的selector参数可以设置为正则表达式，如下：**
```
 var selector={ userName:{ $regex:"卫龙飞" }};
```
另外一种写法:
```
  var selector={ userName:/卫龙飞/ };
```
查询条件如果是由英文字母组成,必然有字母大小写的是区别，如果不需要区分大小写, 需要在查询条件后面加上$options:"$i"

```
 var selector={ userName:{ $regex: "weilongfei", $options:"$i" }}
 mongodbDao.query('User',selector).then(function(data){
    // TODO  
 }) 
```

