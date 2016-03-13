＃Mac下初尝Docker

###mac下安装Homebrew

在终端中输入：

ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

如果不是管理用户，需要用sudo命令来执行。
安装完成以后，如果在运行 brew install 命令的过程中，出现错误提示：
/usr/local/bin/brew: line 26: /usr/local/Library/brew.rb: Undefined error: 0。

输入以下命令进行修复即可：

cd /usr/local/Library

git pull origin master

###mac下安装Docker的管理工具Panamax
brew install http://download.panamax.io/installer/brew/panamax.rb && panamax init

###mac下安装Docker

mac下通过boot2docker 进行下载安装，其地址为：
https://github.com/boot2docker/osx-installer/releases

Docker Hub 为：https://hub.docker.com/， 请注册一个账号，用以管理你的docker image，国内下载 Docker HUB 官方的相关镜像可能比较慢，可以使用 docker.cn 镜像

安装好的下载的boot2docker后，点击启动，当看见To connect the Docker client to the Docker daemon的字样，表示启动完毕。
建议将下面关于docker的变量属性设置为环境变量，设置方式为：
打开 `~/.bash_profile` 文件，然后在其后面追加docker的变量即可，最后输入`source ~/.bash_profile` 重置系统的环境变量，是否重置成功，可以输入 echo $XXX的方式 进行查看。

启动好的docker ，可以通过以下命令来开发端口的访问权限：
比如：docker run --rm -i -t -p 80:80 nginx
再用：boot2docker ip  来获取当前docker的ip
然后就可以通过该ip＋端口，就可以访问到docker里面运行的nginx了。

注意在boot2docker里面，默认的用户和密码是:`docker/tcuser`

在docker中运行image，需要先到docker hub网站选择你需要的image来运行。

####docker常用命令
docker ps 显示正在运行的image

docker ps -a  显示所有运行过的image，通过里面的STATUS字段，可以查看image状态

docker ps -l  显示刚刚运行的image 信息

docker run xxx 运行image,如果该image不存在，则其会先自动到docker hub上下载， 加上参数-d表示以后台进程运行，-p表示指定端口映射,如果想容器外，能访问到docker运行的image服务，需要设定-p参数，来对外开放端口，比如 docker run -d -p 6379:6379 redi 启动后，可以通过boot2docker ip的在外部ip+6379进行访问。

dokcer stop xxx 根据image ID 来停止某个image。其会有10秒的强杀时间。

docker run `-i -t` centos:centos6 `/bin/bash` 可以通过这个命令运行os的shell,输入exit 则就可以退出该进程, -i标志保证容器中STDIN是开启的以提供持久的标准输入是交互式shell，-t标志则告诉Docker为要创建的容器分配一个伪tty终端。这样，新创建的容器才能提供一个交互式shell。


###docker需要下载的image
docker pull redis
docker pull mongo
docker pull mysql
docker pull nginx
docker pull node

###docker 启动node项目
我们可以把源代码放在host上，然后利用docker run -v选项把源代码的文件路径映射到docker container中，这样container就可以直接使用host上的Node代码开启服务。
首先你要进入到node的目录中，然后输入以下命令就可以启动。

docker run -it --rm --name YYY -v "$PWD":XXX -w XXX node ZZZ

其中YYY为自定义这次启动的image进程的名字，通过不同的命名，可以启动多个image,
XXX为映射的路径，ZZZ为执行的node命令。

比如：docker run -it --rm --name `nodeTest` -v "$PWD":`/Users/tangnian/WebstormProjects/huodongla` -w `/Users/tangnian/WebstormProjects/huodongla` node `npm start`
      

###制作node服务的image

方式有2种：

1:一种是run container 的时候通过 -v 参数将 host 文件或目录加载/共享到 container 里。  
命令为：`docker run -v XXX:YYY -i -t ZZZ AAA`  
XXX为host上文件地址，YYY为image中地址，ZZZ为image名称，AAA为在image中需要运行的命令  
如: docker run -v /Users/tangnian/nodeDeveloper/src:/root -i -t easefit/node node /root/huodongla/index.js   
2:制作包含host中node代码的image，然后直接运行该image。
对于第二种方式，可以采取以下命令来实现：  
 1. 创建一个新的文件夹，如：mkdir test,进入该目录,并将app放入该目录
 2. 用命令：touch Dockerfile 创建一个Dockerfile，并在Dockerfile中输入以下配置 属性，如: 
 
```
from node
copy . /usr/src
WORKDIR /usr/src/app

```
 最后再执行`docker build -t XXX` . 的命令。其中XXX为新创建的image的名称，注意要用easefit/node 这样的方式来取名。对于创建好的image，可以通过运行 `docker rmi XX`来删除。注意add . /user/src 是将当前目录下面的文件，都添加到/usr/src下面去,所以最后image中的文件路径为/usr/src/app。
 
 通过运行 docker run -p 8111:8111 XXX npm start 来运行自己创建的node ima###制作node服务的image
 
###DockerFile中添加的参数说明
FROM 是作为镜像的基础  
RUN 可以理解为在FROM下来的镜像做一些环境的部署。 
CMD 是创建容器后，会运行的命令  
EXPOSE 是暴露的端口  
MAINTAINER 通知的邮件  
ADD/COPY 里面2个参数，第一个为需要放进image的data路径，第二个image的路径。注意路径需要为相对路径  
VOLUME  是本地的路径的映射  
WORKDIR 是执行的路径，也就是cmd entrypoint执行的路径。 
比如：
 
```
FROM centos:centos6
# Enable EPEL for Node.js
RUN rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
# Install Node.js and npm
RUN yum -y update
RUN yum install -y npm
# Bundle app source
COPY . /src
# Install app dependencies
RUN cd /src; npm install
EXPOSE  8080
CMD ["node", "/src/index.js"]
 
```

 

 
            








