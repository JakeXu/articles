##添加数据方法的实例
> **save ( collection,  data)** ------ 插入新增数据
> - conllection (String) --  集合名 
> - data (Object) --  插入的数据，同一个集合，其data可以不同。
> - return (Object) --  返回数据插入后的数据对象，里面会有_id属性。


#####用法如下：
    var userData={userName:"卫龙飞",age:"30"};
	mongodbDao.save('User', userData).then(function (data) {
	    // TODO
	})
    
 

 **在使用save进行插入的时候，你不必像关系型数据库那样先创建表。如果User存在，则直接插入User的集合中，如果不存在，则先自动创建，再插入。**