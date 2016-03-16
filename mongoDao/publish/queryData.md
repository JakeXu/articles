##**调用查询数据的方法实例**
>**query( collection, selector )**------ 根据条件查询数据
>- conllection（String) -- 查询数据的表名 
>- selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会操作掉该条数据
>- return ([Object]) -- 查询出所有匹配条件的数据 

#####用法如下
     var selector={ userName: "卫龙飞" };
     mongodbDao.query('User', selector).then(function (data) {
        // TODO
     })
     
     
  >**findOne( collection, data )** ------ 根据条件查询一条数据
>- conllection（String) -- 查询数据的表名 
>- selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会操作掉该条数据
>- return (Object) -- 从所有匹配条件的数据中返回第一条
    
#####用法如下  
    var selector={ userName: "卫龙飞" };
    mongodbDao.findOne('User',selector).then(function(data){
        // TODO
    })

> **queryAdv( collection, selector, fields )**  ------ 根据条件查询的用法，其是优化的查询，因为它可以指定返回的字段
>- conllection（String) -- 查询数据的表名 
>- selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会操作掉该条数据
> - fields (Object) --  指定需要的字段，如{ username : 1, age : 0 }
>- return (Object) -- 从所有匹配条件的数据中返回第一条

#####用法如下 
    var selector={userName:"卫龙飞"}; 
    mongodbDao.queryAdv('User',selector,{usrname:1}).then(function(data){
        // TODO
    })
