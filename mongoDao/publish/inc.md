##$inc自增长
业务中总存在着字段需要自增长的情况，我们如果通过先查询出来，再计算后更新的方式来实现，会存在并发问题。所以mongodb提供了$inc这样的属性来做这样操作，整个过程保证是原子性。

**字段age值增加2**

```
mongodbDao.updateAdv('User',{userName:"卫龙飞"},{"$inc":{"age":2}}).then(function(data){
   // TODO
})
```

注意在使用$inc的时候，不能使用update方法，要使用updateAdv方法。

