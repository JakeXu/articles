##调用修改数据的方法实例
>**update( collection, selector, newData )**------ 更新数据
>- collection (String) --  集合名 
>- selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会操作掉该条数据
>- newData (Object) -- 更新的数据 
>- return (int) -- 修改成功的数据数量

     var conditionData={userName:"卫龙飞"};
     var newData={age:"30"};
     mongodbDao.update('User', conditionData, newData).then(function(data){
        // TODO
     })
**这个update方法通常用来更新数据的某些字段的值,当字段不存在的时候将创建这些字段,并不会影响其他字段。**

>**updateAdv(collection, selector, updateObj, options)**------ 更新数据的高级方法，其可以自定义更新属性
>- collection (String) --  集合名 
>- selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会操作掉该条数据
>- updateObj (Object) -- 更新设置对象，里面需要使用$inc，$addToSet，$pull，$set等方式。具体设置[点击查看](https://mongodb.github.io/node-mongodb-native/api-generated/collection.html#update)
> - options (Object) -- 更新操作的参数设置，包括w,upsert,multi等设置。具体设置[点击查看](https://mongodb.github.io/node-mongodb-native/api-generated/collection.html#update)
>- return (int) -- 修改成功的数据数量

     var conditionData={userName:"卫龙飞"};
     var updateObj={$set:{age:"30"}};
     var options={w:1,upsert:true};
     mongodbDao.updateAdv('User', conditionData, updateObj, options).then(function(data){
        // TODO
     })
**updateAdv的使用，务必在熟悉[mongodb的特性和api](https://mongodb.github.io/node-mongodb-native/api-generated/collection.html)情况下使用。**

