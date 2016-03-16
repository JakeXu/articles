##常用的分页查询
>####findPagingData(collection,selector,pageRequest) ----分页查询
>- conllection (String) -- 查询数据的表名 
>- selector  (Object) -- 查询过滤条件
>- pageRequest  (Object) -- 分页对象，里面包含start开始的数据下标，limit一次查询的数据量，sort排序规则
>- return (Object) -- 返回值是一个比较复杂的javaScript Object, 如果获取分页数据,可以直接获取返回对象的records属性值


       var selector={age:30};
       var pageRequest = {};
       pageRequest.start = 1;
       pageRequest.limit = 10;
       pageRequest.sort = sort ? sort : {createTime: -1};
       mongodbDao.findPagingData('User', selector,pageRequest).then(function(userData){
          // TODO
       })
   

