## I. 简介

Docker 是一个开源的**应用容器引擎**，让**开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化**。容器是完全使用沙箱机制，相互之间不会有任何接口。

由于本地开发好的程序往往都需要部署到服务器上进行运行，这就导致了程序需要运行在不同的环境上，这通常是一个令人头痛的事情。在过去，开发团队需要清楚的告诉运维部署团队，其所使用的全部配置文件+所有软件环境。不过，即便如此，仍然常常发生部署失败的状况。

于是乎，**虚拟化**技术应运而生。开发团队将开发好的程序在虚拟机上运行，这样就能解决运维的问题。但是由于虚拟机技术过重的特性导致了其**资源占用多、冗余步骤多以及启动慢的缺陷**。而这个时候**一种新的虚拟化技术搭配上容器化的思想**的产品便出现了，而它就是Docker。

下图是虚拟机技术和容器化技术架构的对比。我们可以得出以下总结：

- 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程
- 而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。 因此容器要比传统虚拟机更为轻便。
- 每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。

![containers-vs-virtual-machines](C:\study\狂神\Ops\docker\docker_img\containers-vs-virtual-machines.jpg)

于是乎相比于虚拟机技术，容器化技术具有以下**优势：**

- **更快速的应用交付和部署**

	传统的应用开发完成后，需要提供一堆安装程序和配置说明文档，安装部署后需根据配置文档进行繁杂 的配置才能正常运行。Docker化之后只需要交付少量容器镜像文件，在正式生产环境加载镜像并运行即 可，应用安装配置在镜像里已经内置好，大大节省部署配置和测试验证时间

- **更便捷的升级和扩缩容**

	随着微服务架构和Docker的发展，大量的应用会通过微服务方式架构，应用的开发构建将变成搭乐高积 木一样，每个Docker容器将变成一块“积木”，应用的升级将变得非常容易。当现有的容器不足以支撑业 务处理时，可通过镜像运行新的容器进行快速扩容，使应用系统的扩容从原先的天级变成分钟级甚至秒级

- **更简单的系统运维**

	应用容器化运行后，生产环境运行的应用可与开发、测试环境的应用高度一致，容器会将应用程序相关 的环境和状态完全封装起来，不会因为底层基础架构和操作系统的不一致性给应用带来影响，产生新的 BUG。当出现程序异常时，也可以通过测试环境的相同容器进行快速定位和修复

- **更高效的计算资源利用**

	Docker是内核级虚拟化，其不像传统的虚拟化技术一样需要额外的Hypervisor [管理程序] 支持，所以在 一台物理机上可以运行很多个容器实例，可大大提升物理服务器的CPU和内存的利用率

**相关网站**

- Docker官网：http://www.docker.com 

- Docker中文网站：https://www.docker-cn.com 

- Docker Hub官网：https://hub.docker.com （仓库）

## II. 基本原理

如下图所示，Docker使用客户端-服务器（C/S）架构模式，使用远程API来管理和创建Docker容器。服务器端分为Docker daemon, Image和Container三个部分。此外还有Docker Registry。

![docker组成部分](C:\study\狂神\Ops\docker\docker_img\docker组成部分.png)

下面首先来介绍一下Docker中的主要概念

1. **镜像（Image）**

	Docker 镜像（Image）就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很 多容器。 就好似 Java 中的 类和对象，**类就是镜像，容器就是对象**！

2. **容器（Container）**

	Docker 利用**容器（Container）独立运行的一个或一组应用**。**容器是用镜像创建的运行实例**。 它可以被启动、开始、停止、删除。每个容器都是**相互隔离**的，保证安全的平台。
	可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。。
	容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

3. **Registry和仓库（Repository）**

	仓库（Repository）是集中存放镜像文件的场所。
	仓库(Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。 仓库分为公开仓库（Public）和私有仓库（Private）两种形式。
	最大的公开仓库是 Docker Hub(https://hub.docker.com/)，存放了数量庞大的镜像供用户下载。 国内的公开仓库包括阿里云 、网易云等。

Docker的运行原理如下：客户端可以将docker命令发送到服务器端的Docker daemon上，再由Docker damon根据指令创建、选择或者从Docker仓库中拉取（pull）镜像。接着客户端可以通过镜像创建容器。当我们需要使用程序时，运行相应的容器即可。

**小结**

需要正确的理解仓储/镜像/容器这几个概念 :

- Docker 本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个 可交付的运行环境，这个打包好的运行环境就似乎image镜像文件。只有通过这个镜像文件才能生成Docker容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。 同一个 image 文件，可以生成多个同时运行的容器实例。 
- image 文件生成的容器实例，本身也是一个文件，称为镜像文件。一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例， 也就是我们的容器
- 至于仓库，就是放了一堆镜像的地方，我们可以把镜像发布到仓库中，需要的时候从仓库中拉下来 就可以了。

## III. 基本使用

1. **Docker安装**

	详见：https://docs.docker.com/engine/install/centos/

2. **帮助命令**

	```shell
	docker version # 显示Docker版本信息
	docker info # 显示Docker系统信息，包括镜像和容器数
	docker --help # 帮助
	```

	

3. **镜像命令**

	```shell
	docker images # 列出本机所有镜像
	
	# 可选项
	-a：列出本地所有镜像
	-q： 只显示镜像id
	--digests： 显示镜像的摘要信息
	
	# 搜索镜像
	docker search image_name # 在DockerHub仓库中搜索某个镜像
	
	# 下载（拉取）镜像
	docker pull image_name:edition # 在DockeHub上下载某个镜像
	
	# 删除镜像
	docker rmi -f image_id # 删除单个镜像
	docker rmi -f image_name:tag # 删除多个镜像
	docker rmi -f $(docker images -qa) # 删除全部镜像
	```

	

4. **容器命令**

  - **以某个镜像为基础生成并运行容器**

       ```shell
       docker run [OPTIONS] IMAGE [COMMAND][ARG...]
       
       # 常用参数说明 
       --name="Name" # 给容器指定一个名字
       -d # 后台方式运行容器，并返回容器的id
       -i # 以交互模式运行容器，通常和 -t 一起使用
       -t  # 给容器重新分配一个终端，通常和 -i 一起使用
       -P  # 随机端口映射（大写）
       -p # 指定端口映射（小结），一般可以有四种写法
           ip:hostPort:containerPort 
           ip:containerPort 
           hostPort:containerPort (常用) containerPort
       
       # 例子
       docker run -it centos /bin/bash # 使用centos进行用交互模式启动容器，在容器内执行/bin/bash命令
       ```
       
  - **列出所有容器**
    
       ```shell
       docker ps [OPTIONS]
       
       # 常用参数说明
       
       -a # 列出当前所有正在运行的容器 + 历史运行过的容器
       -l # 显示最近创建的容器
       -n=? # 显示最近n个创建的容器
       -q # 静默模式，只显示容器编号。
       ```
       
       
       
   - **退出容器**

       ``` shell
       exit # 容器停止退出
       ctrl+P+Q # 容器不停止退出

  

   - **启动已停止的容器**

  	```shell
  	docker start (容器id or 容器名) # 启动容器
  	docker restart (容器id or 容器名) # 重启容器
  	docker stop (容器id or 容器名)  # 停止容器
  	docker kill (容器id or 容器名) # 强制停止容器
  	```

  	

  - **删除容器**

  	```shell
  	docker rm 容器id # 删除指定容器
  	docker rm -f $(docker ps -a -q) # 删除所有容器 
  	docker ps -a -q|xargs docker rm # 删除所有容器
  	```

5. **常用命令总结**

	<img src="C:\study\狂神\Ops\docker\docker_img\docker常用命令3.png" alt="docker常用命令1" style="zoom:140%;" />

	![docker常用命令1](C:\study\狂神\Ops\docker\docker_img\docker常用命令1.png)

![docker常用命令1](C:\study\狂神\Ops\docker\docker_img\docker常用命令2.png)

## III. 容器数据卷

在外面使用容器的时候，我们不希望容器中的数据在容器被删除后也一并删除了，这时候我们就可以**通过使用容器数据卷，将数据储存在本地并用Docker将其挂载到容器中，这样我们即使删除了容器，数据也依旧存在服务器中，也就实现了数据持久化。**

**特点**

- 数据卷可在容器之间共享或重用数据 
- 卷中的更改可以直接生效
- 数据卷中的更改不会包含在镜像的更新中 
- 数据卷的生命周期一直持续到没有容器使用它为止

**容器数据卷挂载命令（-v）**

```shell
# 匿名挂载 
-v 容器内路径 docker run -d -P --name nginx01 -v /etc/nginx nginx

# 匿名挂载的缺点，就是不好维护，通常使用命令 docker volume维护 
docker volume ls

# 具名挂载 -v 卷名:/容器内路径 
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx nginx

# 查看挂载的路径 [root@kuangshen ~]# 
docker volume inspect nginxconfig

# 怎么判断挂载的是卷名而不是本机目录名？ 
不是/开始就是卷名，是/开始就是目录名

# 改变文件的读写权限 
# ro: readonly 
# rw: readwrite 
# 指定容器对我们挂载出来的内容的读写权限 
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:ro nginx 
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:rw nginx
```

**Dockerfile 挂载容器数据卷**

```shell
# 1、我们在宿主机 /home 目录下新建一个 docker-test-volume文件夹 
[root@kuangshen home]# mkdir docker-test-volume
# 说明：在编写DockerFile文件中使用 VOLUME 指令来给镜像添加一个或多个数据卷 
VOLUME["/dataVolumeContainer1","/dataVolumeContainer2","/dataVolumeContainer 3"] # 出于可移植和分享的考虑，我们之前使用的 -v 主机目录:容器目录 这种方式不能够直接在 DockerFile中实现。 # 由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有宿主机上都存在这样的特定目录.

# 2、编写DockerFile文件 
[root@kuangshen docker-test-volume]# pwd /home/docker-test-volume 
[root@kuangshen docker-test-volume]# vim dockerfile1 
[root@kuangshen docker-test-volume]# cat dockerfile1 

# volume test 
FROM centos VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"] 
CMD echo "-------end------" 
CMD /bin/bash

# 3、build后生成镜像，获得一个新镜像 kuangshen/centos
docker build -f /home/docker-test-volume/dockerfile1 -t kuangshen/centos . # 注意最后有个.
```



## IV. 构建镜像：Dockerfile

我们除了可以从仓库中拉取镜像以外，我们也可以**自己创建镜像**，这就要用到Docerfile。

**dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本**。 

构建步骤： 

1. 编写DockerFile文件 
2. docker build 构建镜像 
3. docker run

### DockerFile构建过程

**基础知识：** 

- 每条保留字指令都必须为大写字母且后面要跟随至少一个参数 
- 指令按照从上到下，顺序执行 
- \# 表示注释 每条指令都会创建一个新的镜像层，并对镜像进行提交 

**流程：** 

1. docker从基础镜像运行一个容器 
2. 执行一条指令并对容器做出修改 
3. 执行类似 docker commit 的操作提交一个新的镜像层 
4. Docker再基于刚提交的镜像运行一个新容器 
5. 执行dockerfile中的下一条指令直到所有指令都执行完成！ 

**说明：**

- 从应用软件的角度来看，DockerFile，docker镜像与docker容器分别代表软件的三个不同阶段。
- DockerFile 是软件的原材料 （代码） Docker 镜像则是软件的交付品 （.apk） 
- Docker 容器则是软件的运行状态 （客户下载安装执行）
- DockerFile 面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可！

![Dockerfile](C:\study\狂神\Ops\docker\docker_img\Dockerfile.png)

- **DockerFile**

	需要定义一个DockerFile，DockerFile定义了进程需要的一切东西。DockerFile涉及的内容 包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进 程和内核进程（当引用进行需要和系统服务和内核进程打交道，这时需要考虑如何设计 namespace的权 限控制）等等。

- **Docker镜像**

	在DockerFile 定义了一个文件之后，Docker build 时会产生一个Docker镜像，当运行 Docker 镜像时，会真正开始提供服务；

- **Docker容器**

	容器是直接提供服务的

### Dockerfile指令

```shell
FROM  # 基础镜像，当前新镜像是基于哪个镜像的
MAINTAINER # 镜像维护者的姓名混合邮箱地址
RUN  # 容器构建时需要运行的命令
EXPOSE  # 当前容器对外保留出的端口
WORKDIR # 指定在创建容器后，终端默认登录的进来工作目录，一个落脚点
ENV  # 用来在构建镜像过程中设置环境变量
ADD # 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
COPY  # 类似ADD，拷贝文件和目录到镜像中！
VOLUME  # 容器数据卷，用于数据保存和持久化工作
CMD # 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最后一个生效！
ENTRYPOINT  # 指定一个容器启动时要运行的命令！和CMD一样
ONBUILD # 当构建一个被继承的DockerFile时运行命令，父镜像在被子镜像继承后，父镜像的ONBUILD被触发
```

<img src="C:\study\狂神\Ops\docker\docker_img\dockerfile指令.png" alt="dockerfile指令" style="zoom:100%;" />

## V. Docker 网络

在实际场景中，我们会遇到**多个Container之间通讯**的问题。而Docker网络就是用于解决此问题的技术。docker会给每个容器都分配一个ip，且容器和容器之间是可以互相访问的。

**Docker网络原理**

每一个安装了Docker的linux主机都有一个docker0的虚拟网卡。这是个桥接网卡，使用了**veth-pair 技术**。Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据 Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网 关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接通信。

![docker网络](C:\study\狂神\Ops\docker\docker_img\docker网络.png)

Docker容器网络就很好的利用了Linux虚拟网络技术，在本地主机和容器内分别创建一个虚拟接口，并 让他们彼此联通（这样一对接口叫veth pair）； 

Docker中的网络接口默认都是虚拟的接口。虚拟接口的优势就是转发效率极高（因为Linux是在内核中 进行数据的复制来实现虚拟接口之间的数据转发，无需通过外部的网络设备交换），对于本地系统和容 器系统来说，虚拟接口跟一个正常的以太网卡相比并没有区别，只是他的速度快很多。

![docker网络](C:\study\狂神\Ops\docker\docker_img\docker网络2.png)

## VI. 定义并运行多容器：Docker Compose

**Docker Compose是一个用于定义并运行多容器应用的工具**。

Docker Compose的**步骤如下**：

1. 为每个容器（自定义容器）**编写Dockerfile**
2. **编写docker-compose.yml文件**，用于定义如何运行多容器以及定义容器配置
3. 执行`docker-compose up`命令，用于启动项目。



**NOTE: Compose ：重要的概念。**

- 服务services： 容器。应用。（web、redis、mysql....） 
- 项目project： 一组关联的容器。 博客。web、mysql。



**docker-compose.yml编写**

```yaml
# 3层！
version: '' # 版本 
services: # 服务 
	服务1: web 
		# 服务配置 
		images 
		build 
		network 
		.....
    服务2: redis 
    	....
    服务3: redis
# 其他配置 网络/卷、全局规则 
volumes: 
networks: 
configs:
```

Note: docker-compose会自动为多容器之间创建网络，保证通讯。



## VII. 集群部署：Docker Swarm 

**Docker Swarm 是Docker** 的集群管理工具。 它将**Docker** 主机池转变为单个虚拟**Docker** 主机。 **Docker Swarm** 提供了标准的**Docker** API，所有任何已经与**Docker** 守护程序通信的工具都可以使用**Swarm** 轻松地扩展到多个主机。

## VIII. 总结

Docker 是一个开源的**应用容器引擎**，它的出现大大简化了运维的难度，提高了运维效率。过去我们需要在服务器上安装程序所需要的所有依赖，而如今我们只需要编写好docker-compose和Dockefile的脚本，就可以使程序一键跑通。在企业级的应用中，我们必然会惊颤使用到Docker和容器化技术。

## IX. 参考

[狂神说](https://www.bilibili.com/video/BV1og4y1q7M4)



