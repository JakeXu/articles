##**特殊操作** 
#####除了一些基本操作外,我们还封装了一些十分有用高效的特殊操作。
>####findAndModify(collection, selector, newData, sort)
>+ conllection &emsp;&emsp;[type:string]&emsp;&emsp;      查询数据的表名 
>+ selector     &emsp;&emsp;   [type:js对象] &emsp;&emsp;      查询数据所需要的匹配条件
>+ newData   &emsp;&emsp;  [type:js对象] &emsp;&emsp;     修改数据 
>+ sort     &emsp;&emsp;   ［type:js对象］&emsp;&emsp;    排序规则
>return

#####查询用户表里面所有用户名是"卫龙飞"的数据
     var conditionData={userName:"卫龙飞"};
     /**
      *@param  'User'--表名 
      *@param   conditionData-- 查询条件[type:javaScript Object]
      *@return  data[type:数组]
      */
     mongodbDao.query('User', conditionData).then(function (data) {
        if(data){
             console.log("data的数据",data);
        }else{
             console.log('用户表里面没有用户名是"卫龙飞"的数据');
        }
     })
     
     
  >####findOne(collection, data)
>+ conllection &emsp;&emsp;[type:string]&emsp;&emsp;       查询数据的表名 
>+ data     &emsp;&emsp;   [type:js对象｜type:string] &emsp;&emsp;       查询数据所需要的匹配条件
>+ return   &emsp;&emsp;  [type:js对象] &emsp;&emsp;        查询出所有匹配条件的数据      


#####我们对mongodb数据库查询的封装不止一种方式,常用的findOne方法也可以做到,不过这个方法和query方法有些不同
    mongodbDao.findOne('User',conditionData).then(function(data){
       if(data){
             console.log("data的数据",data);
        }else{
             console.log('用户表里面没有用户名是"卫龙飞"的数据');
        }

     });
#####你可以看到以上两种写法的执行结果会有所不同,那是因为query方法和findOne方法的作用虽然都是查询,但是query方法是查询出满足条件的所有数据(数组),而findOne只是取数据库中满足条件的第一条数据(javaScript Object)。同时我们对findOne方法做了重载,查询条件可以是字符串,但是字符串必须是该数据的ObjectId所对应的字符串。
     var userId="1000000000000001";
     mongodbDao.findOne('User',userId).then(function(userData){
		   console.log("用户数据",userData);
     })

    /**
     *  查询并修改,该操作为一个原子操作
     *  @param remove:boolean 返回之前就删除对象
     *  @param new:true 返回已更改的对象
     *  @param fields 指定返回的字段，默认为全部
     *  @param upsert:boolean 匹配的不存在，就创建并插入数据
     */
    findAndModify: function(collection, selector, newData, sort) {
        var deferred = Q.defer();
        var sort = sort ? sort : {
            createTime: -1
        };
        if (typeof(selector) == 'string') {
            selector = {
                _id: ObjectID.createFromHexString(selector)
            };
        }
        this._db.collection(collection).findAndModify(selector, sort, newData, {
            w: 1,
            new: true,
            upsert: true
        }, function(err, data) {
            if (err) {
                deferred.reject(new Error(err));
            } else {
                deferred.resolve(data);
            }
        });
        return deferred.promise;
    }
    
    /**
     * 查询并删除,该操作为一个原子操作
     */
    findAndRemove: function(collection, selector, sort) {
        var deferred = Q.defer();
        var sort = sort ? sort : {
            createTime: -1
        };
        if (typeof(selector) == 'string') {
            selector = {
                _id: ObjectID.createFromHexString(selector)
            };
        }
        this._db.collection(collection).findAndRemove(selector, sort, function(err, data) {
            if (err) {
                deferred.reject(new Error(err));
            } else {
                deferred.resolve(data);
            }
        });
        return deferred.promise;
    },

      distinct: function(collection, field) {
        var deferred = Q.defer();
        try {
            this._db.collection(collection).distinct(field, function(err, data) {
                if (err) {
                    deferred.reject(new Error(err));
                } else {
                    deferred.resolve(data);
                }
            });
        } catch (err) {
            deferred.reject(new Error(err));
        }
        return deferred.promise;
    }
#####findAndModify和findAndRemove方法特点是看似它是多个操作集合,但是他却十分的高效,因为这两个方法都是一个原子操作。