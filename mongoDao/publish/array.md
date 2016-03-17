##数据内部属性是数组的操作实例
**向数据库插入一条数据 数据有个属性就是数组**
```
   var  egData={
         userName: "hades",
         age: 30,
         address: "xxxxxx",
         friends: ["x","y","z"]
    }
```

**判断egData里面的friends数组里面有没有"x"这个数据**
```
 var selector={
       "friends": {
          "$all": ["x"]
       }
   }; 
   mongodbDao.query('User', selector).then(function(data){
      //TODO
   })
```

**向egData里面的friends数组里面增加一个"lulu" 并且原先数组里面没有这个数据在添加**
```
  var selector={
      "$addToSet": {
        "friends": "lulu"
      }
  }
  mongodbDao.update('User', {userName: "hades"}, selector, false, false).then(fucntion(data){
     // TODO
  })
```

**删除egData里面的friends数组里面的"x"数据**
```
  var selector={
          "$pull": {
            "friends": "x"
          }
      }
      mongodbDao.update('User', {userName: "hades"}, selector).then(fucntion(data){
          // TODO
      })
```
**删除多个数据 使用pullAll**

