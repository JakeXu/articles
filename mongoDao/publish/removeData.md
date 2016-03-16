##删除数据方法的实例
> **remove ( collection,  selector )** ------ 按条件删除数据。
> - conllection (String) --  集合名
> - selector (Object) --  指定条件的数据对象。其中值可以为字符串，但是该字符串只能是数据库内部_id的             值，当是它的时候，其会删除掉该条数据
> - return (Object) --  。


#####用法如下：
	//普通用法
	mongodbDao.remove('User', {userName:"卫龙飞"}).then(function (data) {
	    // TODO
	})
	//简洁用法
	mongodbDao.remove('User', '56970b5a8d28674a506d2346').then(function(data){
        // TODO
    })
    
 
**该方法会将符合selector条件的数据都删除,所以这可能是一个批量删除。**
