##**正则表达式的运用**
#####mongodb查询条件可以是正则表达式
    var conditionData={userName:{$regex:"卫龙飞"}};
#####另外一种写法:
    var conditionData={userName:/卫龙飞/};
#####查询条件如果是由英文字母组成,必然有字母大小写的是区别，如果不需要区分大小写,需要在查询条件后面加上$options:"$i"
     var conditionData={userName:{$regex:"weilongfei",$options:"$i"}}
     mongodbDao.query('User',conditionData).then(function(data){
         console.log("查询数据",conditionData);
    })
  