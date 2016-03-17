##findAndRemove
>**findAndRemove(collection, selector, newData, sort) ---- 查询并删除的操作，注意该方法属于一个原子操作**
>- conllection (String) -- 查询数据的表名 
>- selector  (Object) -- 需要修改数据的匹配条件
>- sort  (Object) -- 排序规则   
>- return (Object) -- 修改后的数据

用法如下：

     var selector =['userName','hades'];
     mongodbDao.findAndRemove('User', selector,{}).then(function (data) {
        // TODO
     })
findAndRemove方法会先按查询条件匹配数据，然后排序,最后删除第一条数据，整个过程没有并发问题。

