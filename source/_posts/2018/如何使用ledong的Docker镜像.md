---
title: '如何使用ledong的Docker镜像'
author: ledong
comments: true
tags:
  - Docker
  - Docker镜像
  - ledong
categories:
  - 技术构架
  - 说明文档
description: '本文介绍如何使用`ledong`的Docker镜像`save_release.tar`和`export_DataAnaWeb20181115.tar`。'
abbrlink: b2a94349
date: 2018-11-17 12:00:00
keywords:
---

[toc]

# 如何使用`ledong`的Docker镜像

## 说明

- 镜像保存在本地`/data/dockerdata/images/`或者232服务器上`/datadisk/shared/ledong/mydockerImages/`目录里。
- 两个镜像：`save_release.tar`这个没有配R环境和Web环境，`export_DataAnaWeb20181115.tar`配置好了全部的环境。而且两个的加载方式不一样：

```bash
## 加载镜像`release:latest`,名字改不了
sudo docker load  -i  save_release.tar
```

```bash
## 加载镜像可以随意命名，比如`data_ana_web`
sudo docker import  ./export_DataAnaWeb20181115.tar data_ana_web
```

- 本镜像，默认有三个账户：`root(pw: LeDong111111)`、`ld(pw: 111111)`、`guest(pw: 111111)`，其中ld有sudo权限，guest是对外服务账号。
- 本镜像，配置了三个环境：
  * /home/guest/.conda/envs/DataAnaWeb(只存在于`export_DataAnaWeb20181115.tar`)
  * /opt/conda
  * /opt/conda/envs/ld20180607py365r343

- 本镜像，安装了如下东西：
  * 基本的ubuntu
  * 三个账户（见上面）
  * texlive-full、Mysql、R、Conda

- 建议用guest账户里的DataAnaWeb虚环境运行，安装的程序也放在这个环境里。如果需要root权限，再用ld账户的sudo。

- 先保证Docker能正常运行。在Centos或Ubuntu中若出现错误`Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?`，可以运行：

```bash
sudo systemctl restart docker.service
```

- 以下在Centos7和Ubuntu16:04上运行成功，CPU是4核以上，内存12G以上（实际配置可能不需要这么高）。

## 基本使用——以`export_DataAnaWeb20181115.tar`运行DataAnaWeb项目为例

第一步，加载镜像，需要等待两三分钟——因为有10-20G。，得到镜像`data_ana_web`：

```bash
## 加载镜像可以随意命名，比如`data_ana_web`
sudo docker import  ./export_DataAnaWeb20181115.tar data_ana_web
```

第二步，用镜像运行容器并提供服务。假设项目文件在文件夹`example_DataAnaWeb/DataAnaWeb/`。如果运行有误，请按照其中的`run.sh`文件显示的步骤来调试。如果没错，等几十秒，即可在浏览器输入网址`http://172.18.98.232:9000/login/`查看。

```bash
sudo docker rm -f DataAnaWeb;
sudo docker run -it --name "DataAnaWeb" -d -v $PWD/example_DataAnaWeb/:/home/guest/savedata/ --privileged=true -e LANG=C.UTF-8 -p 9000:8000 data_ana_web /bin/bash -c "cd /home/guest/savedata/DataAnaWeb; bash run.sh; bash"
```

- `-it` 使得客户端有交互。
- `--name` 指定容器名。
- `-d` 直接运行完返回容器的id。
- `-v` 这里把`example_DataAnaWeb/`文件夹加载到Docke里的`/home/guest/savedata/`；
- `--privileged=true` 解决`-v`参数可能出现的错误`cannot open directory`（[参考](https://blog.csdn.net/rznice/article/details/52170085)）。使用该参数，container内的root拥有真正的root权限（[参考](https://blog.csdn.net/halcyonbaby/article/details/43499409)）。
- `-e` 系统的编码方式。
- `-p` 这里把系统的`9000`端口映射到Docker容器的`8000`端口——即我们`DataAnaWeb`服务的端口，就直接可以通过网页访问啦。
- `/bin/bash -c “”` 用于指定容器运行后执行的指令。

> **注意**
> 以上在Centos7中没问题，但是在Ubuntu16:04中，运行MySQL会出现错误`/usr/sbin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: Permission denied`。按照[网上记载](https://github.com/moby/moby/issues/5430)，解决方案是不要`--privileged`参数，方案可行。但即便如此，运行命令`ls -l /usr/lib`或者`ls -l /lib/x86_64-linux-gnu/`会发现，所有".so"文件还是没有执行权限，但是没有权限错误了。原因不详……

第三步，如果有需要，可以以进入容器查看：

```bash
sudo docker exec -it DataAnaWeb /bin/bash -c "cd /home/guest/savedata/; bash"
```

## 如何基于本镜像创建新的镜像——以`save_release.tar`安装新环境为例

比如我们要为DataAnaWeb这个项目安装一些东西，假设安装文件在文件夹`example_DataAnaWeb/installEnv/`。

第一步，加载镜像，需要等待分分钟——因为有10-20G。，得到镜像`release:latest`：

```bash
sudo docker load  -i  save_release.tar
```

- 这一步，也可以参考上面的小节，加载`export_DataAnaWeb20181115.tar`为`release`，在其基础上安装新环境。

第二步，运行镜像`release`：

```bash
##### 如果已经存在，先删除
###sudo docker ps -a | grep release
###sudo docker stop release
###sudo docker rm release
sudo docker run -it -v $PWD/example_DataAnaWeb/:/home/guest/savedata/ --privileged=true -e LANG=C.UTF-8 release /bin/bash
```

第三步，安装东西：

```bash
cd /home/guest/savedata/installEnv/
bash run.sh
```

- 按上一步做的话，进入`/home/guest/savedata/`其实就是`example_DataAnaWeb/`文件夹啦。
- 用guest账户，在`DataAnaWeb`虚环境下面，安装需要的R包时，有的R包安装不成功，但不影响，主要是我们自己写的包都能装上了，就够了。
- 如果需要root权限，可以切换到ld账户，比如要用`sudo apt-get install ...`（使用apt-get先更新`sudo apt-get update`）。

第四步，保存容器为本地镜像，这个还是要等两三分钟：

```bash
sudo docker export -o ./export_DataAnaWeb.tar <CONTAINER ID OR NAME>
##### 上面这个是持久化容器，下面这个是持久化镜像，不能用下面这个
###sudo docker save -o ./save_release20181113.tar release
```

- 其中`<CONTAINER ID OR NAME>`可以用命令`sudo docker ps -a`查看到。

## 容器使用注意事项

- 镜像里已经有的东西，只能改，没法删除——就算删除了，下次打开容器还在！用rm命令强制删除无效，大家不要白费力气。
- 一般所有保留数据的地方，建议放在/home/yourUserName/目录，并把一个本地目录挂载到这个目录。使用如下语句运行容器：

``` bash
sudo docker run -it --rm -v ~/tmp/:/home/guest/savedata/ -e LANG=C.UTF-8 release
```

- 要注意权限和拥有者。比如上面挂载的`~/tmp/`对应的`/home/guest/savedata/`的拥有者，一般和在主机上的一致，在Docker里的guest账号未必有操作权限，而在Docker里更改了拥有者的话，主机上的拥有者等也会变。更改拥有者和权限的命令：

```bash
sudo chown -R guest <dir or file> # 改拥有者
sudo chgrp -R users <dir or file> # 改组别
sudo chmod -R 755   <dir or file> # 改权限
```

### 容器使用中一些有用的命令

```bash
###### 查看
sudo docker history <ID>
sudo docker ps -a
sudo docker system df
sudo docker system prune -a
sudo docker images <image>

###### 容器备份到镜像
sudo docker commit  <CONTAINER ID>  [imageNewName]

###### 保存和加载
sudo docker save  -o  save.tar <image>
sudo docker load  -i  save.tar

sudo docker export <CONTAINER ID> > export.tar
sudo docker export -o export.tar <CONTAINER ID OR NAME>
sudo docker import  export.tar [imageNewName]

###### 用dockerfile创建
sudo docker build -t  release -f ./release.dockerfile  .

###### 运行镜像
sudo docker run -it -v ~/tmp/:/home/guest/savedata/ --privileged=true -e LANG=C.UTF-8 release /bin/bash -c "cd /home/guest/savedata/; bash"
sudo docker run -it --rm -v ~/tmp/:/home/guest/savedata/ --privileged=true -e LANG=C.UTF-8 release /bin/bash -c "cd /home/guest/savedata/; bash"

###### 运行已有的容器
sudo docker start <CONTAINER ID>
sudo docker exec -it <CONTAINER ID> /bin/bash -c "cd /home/guest/savedata/; bash"
```
