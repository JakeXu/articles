###高姿态的使用CDN
前后端分离的这种开发模式，在移动时代迅速普及开。
CDN在其中扮演的角色发生了一个巨大的变化，以前只是作为一个优化策略存在，如今其可是作为高性能的静态服务器而存在。
####什么意思？CDN还可以做服务？
是的，它不但能做服务，而且其做的服务，具有非常大的性能优势。 
我们来简单分析一下原因，大家知道CDN的主要作用是存放静态资源，从而提高网站访问速度。（[为什么能提速网站？](http://mp.weixin.qq.com/s?__biz=MzI4MjA4ODU0Ng==&mid=402603447&idx=1&sn=a66afa8393ffe5b8272ec0733f3ad1fa&scene=2&srcid=03085jwKbPsa6GysR9gWgv6E&from=timeline&isappinstalled=0#wechat_redirect)）在现代前端里面，前后端是分离开发的，后台只负责提供rest api，前端负责页面渲染。既然是这样，那么整个工程的前端部分都是静态资源，它是可以放到CDN上的（后端部分我们不在这篇文章里面讨论）。
####CDN做服务是否靠谱，优势是啥？
首先： 网站对抗DDOS的攻击，大家知道最有效的方法么？嗯，页面静态化，上CDN。
再次：带宽有限的情况下，最大化提高网站的承受能力。为什么？
看如下对比：（假设网站带宽为100M同时只考虑带宽的影响，其他服务器的性能优化默认为最优）
| 架构模式      |    传输的内容 | 假设大小  | 服务端性能开销 | 站点承受并发 | 
| :--------  | --------:| :--: | :--: | :--: |
| 前后端分离   | json |  2k   |没有页面渲染的开销|6400/s|
| 后端动态渲染 |  html,js,css |  1M  |有页面渲染的开销|12.5/s|
哇，这个差距太明显了。这个其实说明了为什么像tomcat这种性能很糟糕的容器，wordpress这种php写的大众博客，他们的性能那么低，为什么还能广泛流传使用。因为对于大多数场景来说，其性能瓶颈不在服务本身，而是在网络带宽啊，亲们。

####我们来看看实战中是如何利用CDN来构建服务
我们项目中用的云服务是阿里云的OSS（七牛也是可以的，思路一样），所以我们针对OSS来操作。
首先开通OSS服务后，在Bucket管理目录中，点击右上的新建Bucket，在弹出来的对话框中取好BucketName(我们取名为thisisatest),选择好所属地域，读写权限选择为‘公共读’，如下图：
![enter image description here](http://iamhades.oss-cn-shanghai.aliyuncs.com/a1.png)  

创建好名称后，进入Bucket概览中,里面的OSS域名栏，如下:
![enter image description here](http://iamhades.oss-cn-shanghai.aliyuncs.com/a2.png)
里面的自定义绑定域名，点击进去，添加上自己的域名，然后点击下一步，添加CNAME记录勾选'自动添加‘ 就可以了。
#####域名的问题需要注意一下：
通过OSS默认的域名来访问OSS的文件全部默认为下载，而HTTP头相关参数如Content-Disposition=inline的设置都是无效的。要直接显示而不是下载，需要绑定用户自己的域名即可，通过测试，总结如下：
- 绑定了自己的域名：
    - 图片显示：通过绑定域名访问图片文件，确实是直接显示；
    - 图片下载：如果有通过绑定域名而直接下载需求的话，可以设置HTTP头的Content-Disposition=attachement;filename=xxxx，即可实现文件另存为”xxxx”；Content-Disposition=attachement则按照原文件名另存为的下载模式，可以满足开发者的不同需求；
- 若使用OSS的默认域名：
则Content-Disposition的设置均会被系统默认的下载策略覆盖，都是图片下载；

#####Bucket的属性设置
进入Bucket的属性栏，关注‘Website设置’，‘Cors设置’ , 设置如下图：
![enter image description here](http://iamhades.oss-cn-shanghai.aliyuncs.com/a3.png)
里面的默认首页和404页，都是根据你自己需要进行设置。

![enter image description here](http://iamhades.oss-cn-shanghai.aliyuncs.com/a4.png)
添加进自己的主站域名，注意http://localhost:8080的设置，主要是为了本地开发需要而设定的。

进行到这里，阿里云上的配置部分就ok了。你可以测试一下，进入Object管理中，上传一个index.html的文件，在浏览器中输入域名，如果可以正常访问那么就是配置正确的。
#####前端代码的自动化配置
接下来我们开始前端代码方面的配置，以便让整个过程真正的高逼格起来，符合现在强调的自动化流程嘛。
**在此推荐webpack的一个自己开发的插件[aliyunoss-webpack-plugin](https://www.npmjs.com/package/aliyunoss-webpack-plugin)，它的作用就是上传指定的文件到oss上。**
将其引入到工程中，我们的前端项目是用vuejs开发的SPA项目，基于webpack构建的，入口文件是index.html。
在webpack的生产配置文件plugins中配置：

    new AliyunossWebpackPlugin({
       buildPath:__dirname + '/build',
       region: 'oss-cn-shanghai',
       accessKeyId: 'your key',
       accessKeySecret: 'your secret',
       bucket: 'thisisatest',
       deleteAll: true,
       getObjectHeaders: function(filename) {
          return {
            'Cache-Control': 'max-age=2592000'
       }
    }


**buildPath**是构建的路径，你需要将工程的入口文件index.html也生成在里面，一并上传。
**地域参数region**是要取决于你一开始新建Bucket的位置，目前支持的列表[点击这里查看](https://help.aliyun.com/document_detail/oss/user_guide/oss_concept/endpoint.html?spm=5176.docoss/user_guide/oss_concept/concepts.2.3.tJhjs3)
**参数getObjectHeaders** 设置上传文件的头信息，我们将静态资源的有效期设置为一个月，阿里的oss默认是不会给文件添加任何信息的，所以我们需要根据自己的需要来进行设置，目前支持的属性[点击查看](http://doc.oss.aliyuncs.com/#_Toc336676772)

这样在每次生产构建的时候，其就会自动删除oss之前的文件，上传新的文件，这样整个前端工程就完成部署更新。


是不是非常简单？云服务时代，不就是让所有的一切都变得简单美好么。
文章写到这里，不免有槽点，我只想起一个抛砖引玉的作用。


