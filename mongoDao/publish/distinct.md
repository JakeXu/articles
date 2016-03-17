##去重数据查询
>distinct(collection, filed) ---- 过滤重复的指定的字段查询
>- conllection (String) -- 查询数据的表名 
>- fied (Object) -- 去重字段
>- return (Object) -- 所有去重后数据

     var fied =['userName','address'];
     mongodbDao.distinct('User', fied).then(function (data) {
        // TODO
     })
     

