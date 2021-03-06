##Express VS Koa

这几天使用了一下Koa.js, 之前很多次听人说起它的精简，优雅。  
于是我怀着这样的心情来使用它，但是用下来后，我发现它的api设计有些不合常理。
我们来对Express 和 Koa 做一个对比：
| 功能      |   Express | Koa  |
| :-------- |:--------:| :--: |
|核心模块   | 有 |  有   |
| 路由     |  有 |  无  |
| 模版     | 有  | 无  |
| 富响应  | 有  | 无  |
| 中间件     | 支持  | 支持  |
| 流程控制    | 无  | 支持Generator  |

从上面的对比中，我们可以看出来，Koa精简是有原因的，因为它只保留了核心模版，其他的路由，模版，富响应（下载文件，JSONP等）都需要以插件的方式来进行按需安装。这...不精简有鬼了。
我们来谈一下流程控制部分，在Koa中借助Generator函数，使用Co来对它进行流程控制。
这种方式的引入，是Koa被人接受的最大理由,个人也非常认同这种方式的处理。这种方式带来了什么？

> 使异步变成同步的书写方式，可读性增强
> 异常处理变的可控
> 避免callback hell问题，以及Promise不够自然的链式问题，冗余代码问题
> 中间件以装配器模式引入，跟Express的链式模式不同

**koa中的req,res** 
关于这点，是我个人非常不喜欢koa的一个最重要的原因，有些过度封装。在Koa中，没有明确的response，request。
如果你想返回参数，用this.body=data 的方式。这样等于弱化了web开发的很多概念，非常不好。

> 如果我们能在Express中引入基于Generator的流程控制，那么Express就能变的更加的强大。因此个人开发了[power-express插件](https://www.npmjs.com/package/power-express)，通过它，可以使得在Express中像Koa中一样，使用Generator来进行流程控制。并且对于中间件来说，并没有严格要求使用Generator函数，这使的链式跟装配器模式共存，它能为开发上带来更多的灵活性。

下篇我们来讲讲power-express是如何实现的...

