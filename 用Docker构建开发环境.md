# 用Docker轻松搭建开发环境
开发一个新项目，部署开发环境是第一步，但是会由于各种原因，导致配置开发环境很费时间，针对这种情况，其实可以轻松用docker来统一部署开发环境。详细如下：

我们的每个项目都在Bitbucket上有自己的group,创建了一个名为development的分支。分支的同一层级有README.md(理论上)和docker-compose.yml两个文件。  
当新程序员加入项目时，只需在Bitbucket上浏览developmentrepository，根据README.md的步骤就可以快速搭建环境。具体步骤如下所示：  
```
$ git -v  
$ docker -v  
$ docker-compose -v
$ git clone git@bitbucket.com:<project>/development.git <project> && cd <project>  
$ git submodule init && git submodule update  
$ git submodule foreach npm install  
$ docker-compose up -d  
```
至此，一切就都已经搭建好，并运行在本地机器上了。
### 实现原理  
本章介绍我们是如何实现上述工作流的。
前提条件  
```
$ git -v  
$ docker -v  
$ docker-compose  
```
由于我们的开发堆栈完全基于Docker，所以，程序员需要先安装Docker。这时他们不需要特别熟悉Docker，只需要在开发时使用Docker即可，我们间接地将他们引入到了容器的世界，之后会以此为桥梁向他们解释如何使用Docker实现持续集成、持续交付等等。README.md中并没有详细介绍如何安装Docker，因为安装很简单。

当docker-compose还叫Fig的时候我们就已经用它来编排开发堆栈里的容器。之后Docker收购了Fig，重命名为Docker Compose。有人提议将Docker Compose合并到Docker代码里，但是基于很多原因最终并没有这么做，所以Docker Compose仍然需要单独安装。

同样地，本文没有详细介绍Docker Compose的安装，因为很简单。  
搭建仓库（Repository）
如前所述，需要创建一个开发仓库，以及为每个应用创建对应的仓库。这里我们创建了api、dashboard和cpanel。当创建这些仓库的时候，重点关注development仓库的搭建。  
$ git clone git@bitbucket.com:＃project＃/development.git ＃project＃ && cd <project>  
现在将应用程序的仓库添加为development仓库的子模块，只需要键入如下命令：  
```
$ git submodule add git@bitbucket.org:<project>/api.git  
$ git submodule add git@bitbucket.org:<project>/dashboard.git  
$ git submodule add git@bitbucket.org:<project>/cpanel.git
```
这样，你的development仓库根目录下会创建出.gitmodules文件。程序员也就可以在克隆developmentrepository的时候一次得到所有的应用程序并运行：  
`$ git submodule init && git submodule update  `  
更多子模块的信息，请参考Git官方文档。

### Docker化一切
现在我们已经搭建好了development仓库，可以通过cd的方式访问所有不同的应用程序。接下来我们要用之前提到的编排工具：Docker Compose来容器化所有的应用及其配置。
首先从api应用程序开始。打开docker-compose.yml，为API声明一个容器，并为这个容器选择基础镜像。本示例中的代码基于Node.js，因此选择官方Node.js镜像：
```
api:
image: dockerfile/nodejs
```
这时，运行命令docker-compose up -d会创建出一个名为&lt;project>_api_1的容器，这个容器什么也不做（启动后立即退出）。运行命令docker-compose ps可以得到由docker-compose.yml编排的所有容器的信息。接下来配置api容器，使其多一些功能。为了实现这个目的，我们需要：将源代码挂载到容器中，声明用什么命令运行应用，暴露合适的端口以供访问应用  
这样配置文件类似：  
```
api:
image: dockerfile/nodejs
volumes:
- ./api/:/app/
working_dir: /app/
command: npm start
ports:
- "8000:8000"
```

现在再运行docker-compose up -d，就启动了api应用，可以在http://localhost:8000访问它。这个程序可能会崩溃，可以使用docker-compose logs api检查容器日志。

这里，我怀疑api的崩溃是因为它连不上数据库。因此需要添加database容器，并让api容器能够使用它。  
```
api:  
image: dockerfile/nodejs  
volumes:  
- ./api/:/app/  
working_dir: /app/  
command: npm start  
ports:  
- "8000:8000"  
links:  
- database  
database:  
image: postgresql  
ports:
- "5432:5432"
```
通过创建database容器，并将其连接到api容器，我们就可以在api容器里找到database。要想展示API的环境（比如，console.log(process.env)），必须使用如下变量，比如`POSTGRES_1_PORT_5432_TCP_ADDR和POSTGRES_1_PORT_5432_TCP_PORT`。这是我们在API的配置文件里使用的关联到数据库的变量。

通过link指令，这个数据库容器被认为是API容器的依赖条件。这意味着Docker Compose在启动API容器之前一定会先启动数据库容器。  
现在我们用同样的方式描述其它应用程序。这里，我们可以通过环境变量  `API_1_PORT_8000_TCP_ADDR和API_1_PORT_8000_TCP_PORT`，将api连接到dashboard和cpanel应用。  
```
- ./api/:/app/   
working_dir: /app/ 
command: npm start
ports:
- "8000:8000"  
links:  
- database  
database:  
image: postgresql  
dashboard:  
image: dockerfile/nodejs  
volumes:  
- ./dashboard/:/app/  
working_dir: /app/  
command: npm start  
ports:  
- "8001:8001"  
links:  
- api  
cpanel:  
image: dockerfile/nodejs  
volumes:  
- ./api/:/app/  
working_dir: /app/  
command: npm start  
ports:  
- "8002:8002"  
links:  
- api  
```
就像之前为数据库修改API配置文件一样，可以为dashboard和cpanel应用使用类似的环境变量，从而避免硬编码。
现在可以再次运行docker-compose up -d命令和docker-compose ps命令：
```
kytwb@continuous:~/path/to/<project>$ docker-compose up -d
Recreating <project>_database_1...
Recreating <project>_api_1...
Creating <project>_dashboard_1...
Creating <project>_cpanel_1...
kytwb@continuous:~/path/to/<project>$ docker-compose ps
Name                     Command              State      Ports           
----------------------------------------------------------------------------------
<project>_api_1         npm start            Up         0.0.0.0:8000->8000/tcp
<project>_dashboard_1   npm start            Up         0.0.0.0:8001->8001/tcp
<project>_cpanel_1      npm start            Up         0.0.0.0:8002->8002/tcp
<project>_database_1    /usr/local/bin/run   Up         0.0.0.0:5432->5432/tcp
```
应用应该就已经启动并运行了。  
从http://localhsot:8000可以访问api。  
从http://localhsot:8001可以访问dashboard。  
从http://localhsot:8002可以访问cpanel。  

### 更进一步  
本地路由

在使用`docker-compose up -d`运行所有容器之后，可以通过http://localhost:<application_port>访问我们的应用。基于当前配置，我们可以很容易地使用jwilder/nginx-proxy加上本地路由功能，这样就可以使用和生产环境类似的URL访问本地应用了。比如，通过http://api.domain.local访问http://api.domain.com的本地版本。
jwilder/nginx-proxy镜像将一切变得很简单。只需要在docker-compose.yml里加上描述去创建一个名为nginx的新容器。根据jwilder/nginx-proxy的README文件（挂载Docker守护进程socket，暴露80端口）配置该容器就可以了。之后，在现有容器里再添加额外的环境变量VIRTUAL_HOST和VIRTUAL_PORT，如下：
```
api:
image: dockerfile/nodejs
volumes:
- ./api/:/app/
working_dir: /app/
command: npm start
environment:
- VIRTUAL_HOST=api.domain.local
- VIRTUAL_PORT=8000
ports:
- "8000:8000"
links:
- database
database:
image: postgresql
dashboard:
image: dockerfile/nodejs
volumes:
- ./dashboard/:/app/
working_dir: /app/
command: npm start
environment:
- VIRTUAL_HOST=dashboard.domain.local
- VIRTUAL_PORT=8001
ports:
- "8001:8001"
links:
- api
cpanel:
image: dockerfile/nodejs
volumes:
- ./api/:/app/
working_dir: /app/
command: npm start
environment:
- VIRTUAL_HOST=cpanel.domain.local
- VIRTUAL_PORT=8002
ports:
- "8002:8002"
links:
- api
nginx:
image: jwilder/nginx-proxy
volumes:
- /var/run/docker.sock:/tmp/docker.sock
ports:
- "80:80"
```
nginx容器会检查所有运行在Docker守护进程之上（通过挂载的docker.sock文件）的容器，为每个容器创建合适的nginx配置文件，并设置VIRTUAL_HOST环境变量。

要想完成本地路由的搭建，还需要在etc/hosts里添加所有的VIRTUAL_HOST。我是手动用node.js的hostile包来完成这个工作的，不过我猜应该可以自动化实现，就像jwilder/nginx-proxy可以根据nginx配置文件动态变化一样。这里需要再研究一下。

现在可以再次运行`docker-compose up -d`，然后使用和生产环境一样的url访问应用程序，只需用.localTLD代替.comTLD。

