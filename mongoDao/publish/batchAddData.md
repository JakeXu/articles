##**批量数据插入**
>####saveBatch(collection, data)
>+ conllection &emsp;&emsp;[type:string]&emsp;&emsp;       需要创建并且保存数据的表名 
>+ data     &emsp;&emsp;   [type:数组对象] &emsp;&emsp;                       需要插入到数据库的大批量数据
>+ return   &emsp;&emsp;  [type:int] &emsp;&emsp;  批量插到数据库的数量

#####我们这里的批量插入采用先将数据放入数组里面,然后调用saveBatch方法执行,他的效率高于使用for循环将数据一条条save到数据库。
    var batchData=[
    {userName:"大飞01"，passWord:"露露01"},
    {userName:"大飞02",passWord:"露露02"},
    {userName:"大飞03",passWord:"露露03"},
    {userName:"大飞04",passWord:"露露04"}
    ];
    
     mongodbDao.saveBatch('User',batchData).then(function(data){
        console.log("批量插入数据",data);
    })