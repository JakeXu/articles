##findAndModify
>**findAndModify(collection, selector, newData, sort) ---- 查询并修改的操作，注意该方法属于一个原子操作**
>- conllection (String) -- 查询数据的表名 
>- selector  (Object) -- 需要修改数据的匹配条件
>- newData  (Object) -- 更新数据
>- sort  (Object) -- 排序规则   
>- return (Object) -- 修改后的数据

更新最常见的操作是根据某些条件查询出文档，然后对查询出的文档进行某些更改，如果我们分步进行操作(先查询、然后更改)，这样会带来线程安全隐患。mongoDB为了避免这种问题，为我们提供了原子性的命令findAndModify，下面对findAddModify方法进行说明

     var selector =['userName','卫龙飞'];
     mongodbDao.findAndModify('User', selector,{address:"北京"},{}).then(function (data) {
             // TODO
     })
findAndModify命令一个速度较慢的原子性操作，它的优点在于是原子性操作，而缺点是运行速度较慢。他会对query条件查询出来的第一条数据第进行修改操作(这里要注意sort排序规则,因为是先对query出来的数据排序,在更新第一条数据。如果排序规则为空,默认为按数据插入时间降序排序。)，如何query条件没有匹配到数据,那么数据库会插入一条新的数据,数据就是newData。findAndModify最重要的特性就是其原子性，保证线程安全。
