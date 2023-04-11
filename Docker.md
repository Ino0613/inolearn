# Docker

#### Docker是一个快速交付应用、运行应用的技术

1. 可以将程序机器依赖、运行环境一起打包为一个镜像，可以迁移到任意Linux操作系统
2. 运行时利用沙箱机制形成隔离容器，各个应用互不干扰
3. 启动、移除都可以通过一行命令完成，方便快捷

#### 镜像：

将应用及其依赖、环境、配置打包在一起

#### 容器：

镜像运行起来就是容器，一个镜像可以运行多个容器

#### Docker结构：

服务端：接受命令或远程请求，操作镜像或容器

客户端：发送命令或者请求到Docker服务端

## Docker基本操作-镜像

#### 1.基本操作

docker images 查看镜像

docker rmi 删除指定镜像

docker pull 拉取镜像

docker push 推送镜像

docker save 保存镜像tar

docker load 加载tar镜像

docker ps 查看所有运行的容器及状态

docker rm 删除容器（不能删除运行中的容器，除非添加 -f 参数）

docker logs 查看容器运行日志

docker exec -it [容器名] [要执行的命令] 进入容器执行命令

#### 2.容器命令

#### ①.创建运行一个Nginx容器

步骤一：去docker hub查看Nginx的容器运行命令

```
docker run --name containerName -p 80:80 -d nginx 
```

**命令解读：**

- docker run：创建并运行一个容器
- --name：给容器起一个名字，比如叫做ng
- -p：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- -d：后台运行容器
- nginx：镜像名称，例如nginx

##### docker run命令的常见参数

- --name：指定容器名称
- -p：指定端口映射
- -d：让容器后台运行

##### 查看容器日志的命令

docker logs

添加 -f 参数可以持续查看日志

##### 查看容器状态

docker ps



#### 2.1 进入Nginx容器，修改HTML文件内容，添加“Ino欢迎你”

##### 步骤一：进入容器

```
docker exec -it mn bash 
```

##### 命令解读：

- docker exec：进入容器内部，执行一个命令

- -it：给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互

- mn：要进入的容器的名称

- bash：进入容器后执行的命令，bash是一个linux终端交互命令

  

#### ②.操作数据卷

数据卷的作用：

将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全

```
docker volume [COMMAND]
```

docker volume命令是数据卷操作，根据命令后跟随的command来确定下一步的操作：

- create      创建一个volume
- inspect    显示一个或多个volume的信息
- ls              列出所有的volume
- prune      删除未使用的volume

- rm            删除一个或多个指定的volume


1. 创建数据卷

```
docker volume craete html
```

   2.查看所有数据

```
docker volume ls
```

   3.查看数据卷详细信息卷

```yaml
docker volume inspect html
```

#### 2.2 利用数据卷操作nginx容器

① 创建容器并挂载数据卷到容器内的HTML目录

```
docker run --name mn -v html:/usr/share/nginx/html -p 80:80 -d nginx
```

② 进入html数据卷所在位置，并修改HTML内容

```yaml
# 查看html数据卷的位置
docker volume inspect html
# 进入该目录
cd /var/lib/docker/volumes/html/_data
# 修改文件
vi index.html
```

#### 2.3 创建并运行一个MySQL容器，将宿主机目录直接挂载到容器

提示:目录挂载与数据卷挂载的语法是类似的:

- -v[宿主机目录]:[容器内目录]

- -v[宿主机文件]:[容器内文件]

  实现思路如下:

  1．在将课前资料中的mysql.tar文件上传到虚拟机，通过load命令加载为镜像

  2．创建目录/tmp/myql/data

  3．创建目录/tmp/myql/conf，将课前资料提供的hmy.cnf文件上传到/tmp/myql/conf

  4．去DockerHub查阅资料，创建并运行MySQL容器，要求:

  - 挂载/tmp/myql/data到mysql容器内数据存储目录

  - 挂载/tmp/myql/conf/hmy.cnf到mysql容器的配置文件

  - 设置MySQL密码

    ```
    docker run --name mysql -e MYSQL_ROOT_PASSWORD=adg153 -p 3307:3306 -v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf -v /tmp/mysql/data:/var/lib/mysql -d mysql:5.7.25
    ```

    

## 自定义镜像 - 镜像结构

竟像是分层结构，每一层成为一个Layer（层）

BaseImage层：包含基本的系统函数库、环境变量、文件系统

Entrypoint：入口，是镜像中应用启动的命令（systemctl start mysql.service）

其他：在BaseImage基础上添加依赖、安装程序、完成整个应用的安装和配置



# 2.CentOS7安装DockerCompose

 

## 2.1.下载

Linux下需要通过命令下载：

```sh
# 安装
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

如果下载速度较慢，或者下载失败，可以使用课前资料提供的docker-compose文件：

上传到`/usr/local/bin/`目录也可以。

 

## 2.2.修改文件权限

修改文件权限：

```sh
# 修改权限
chmod +x /usr/local/bin/docker-compose
```

 

 

## 2.3.Base自动补全命令：

```sh
# 补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

如果这里出现错误，需要修改自己的hosts文件：

```sh
echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
```

 

 

# 3.Docker镜像仓库

搭建镜像仓库可以基于Docker官方提供的DockerRegistry来实现。

官网地址：https://hub.docker.com/_/registry

 

## 3.1.简化版镜像仓库

Docker官方的Docker Registry是一个基础版本的Docker镜像仓库，具备仓库管理的完整功能，但是没有图形化界面。

搭建方式比较简单，命令如下：

```sh
docker run -d \
    --restart=always \
    --name registry \
    -p 5000:5000 \
    -v registry-data:/var/lib/registry \
    registry
```

 

命令中挂载了一个数据卷registry-data到容器内的/var/lib/registry 目录，这是私有镜像库存放数据的目录。

访问http://YourIp:5000/v2/_catalog 可以查看当前私有镜像服务中包含的镜像

 

## 3.2.带有图形化界面版本

使用DockerCompose部署带有图象界面的DockerRegistry，命令如下：

```yaml
version: '3.0'
services:
  registry:
    image: registry
    volumes:
      - ./registry-data:/var/lib/registry
  ui:
    image: joxit/docker-registry-ui:static
    ports:
      - 8080:80
    environment:
      - REGISTRY_TITLE=传智教育私有仓库
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
```

 

## 3.3.配置Docker信任地址

我们的私服采用的是http协议，默认不被Docker信任，所以需要做一个配置：

```sh
# 打开要修改的文件
vi /etc/docker/daemon.json
# 添加内容：
"insecure-registries":["http://192.168.150.101:8080"]
# 重加载
systemctl daemon-reload
# 重启docker
systemctl restart docker
```



















