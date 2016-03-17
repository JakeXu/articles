##统计数据量
>**getCount(collection, selector) ---- 统计指定条件的数据量**
>- conllection (String) -- 查询数据的表名 
>- selector (Object) -- 需要查询数据的匹配条件
>- return (Num) -- 统计的数据

     var selector = {userName: 'hades'};
     mongodbDao.getCount('User', selector).then(function (data) {
        // TODO
     })
     

