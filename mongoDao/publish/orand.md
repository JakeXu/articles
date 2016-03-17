##和/或查询
查询条件为 ‘or’ 的时候，其设置为：

```
   var selector={ $or: [{ name:'hades', age:'31'}] }
```
查询条件为'and' 的时候，其设置为:

```
   var selector={ $and: [{ name:'hades', age:'31'}] }
```
更为复杂一点的查询如下：

```
var selector={ $and: [{ name: 'hades', age: {$lt: 30}}] }
```

