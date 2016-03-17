##分组查询
>group(collection, keys, condition, initial, reduce) ---- 根据条件进行分组查询
>- conllection (String) -- 查询数据的表名 
>- keys (Object) -- 分组键
>- condition (Object) -- 匹配条件 
>- initial  (Object) -- 表示$reduce函数参数prev的初始值。每个组都有一份该初始值
>- reduce  (Function) -- reduce函数接受两个参数，doc表示正在迭代的当前文档，prev表示累加器文档
>- return  ([Object]) -- 分组后查询到的数据

    var keys={age:true};
    var condition={address:"上海"};
    var initial={count: 0};
    var reduce={
          reduce: function(doc,prev){ 
                     prev.count++;
                }
        }
    mongoDao.group('User',keys, condition, initial, reduce}).then(function(data){
        // TODO
    });  
   

