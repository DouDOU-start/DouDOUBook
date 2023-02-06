# docker安装nexus3快速搭建私有仓库

**前言**

依赖下载示意图

![依赖下载示意图](https://github.com/DouDOU-start/DouDOUBook/blob/master/docker/docker%E5%AE%89%E8%A3%85nexus3%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA%E7%A7%81%E6%9C%89%E4%BB%93%E5%BA%93/image/b2b1f9c6d2b74ba5970e9b93f1900405.png)

通常我们开发项目并没有使用到虚线标识部分，基本都是通过本机直接访问中央仓库，下载依赖到本地仓库。现在我们需要搭建中间虚线部分。

## 搭建nexus过程

```shell
# 拉取镜像
$ docker pull sonatype/nexus3

# 持久化目录
$ mkdir -p /home/nexus/data
$ chmod 777 -R /home/nexus/data

# 启动镜像
$ docker run -d --name nexus3 -p 8081:8081 --restart always \
	-v /home/nexus/data:/nexus-data \
	sonatype/nexus3

# 日志查看，稍等一下，出现 Started Sonatype Nexus OSS 表示启动好了
$ docker logs -f nexus3

# Nexus访问，访问地址：http://ip:8081

# 登录用户名为admin，查看默认密码
# 第一次登录会提示修改密码！修改密码之后，重新登录！
# 配置是否启用匿名访问，没有特殊要求一般选择Enable anonymous access启动匿名访问
$ more /home/nexus/data/admin.password
```

## 配置Nexus

**默认仓库说明**

```tex
maven-central：maven中央库，默认从https://repo1.maven.org/maven2/拉取jar
maven-releases：私库发行版jar，初次安装请将Deployment policy设置为Allow redeploy
maven-snapshots：私库快照（调试版本）jar
maven-public：仓库分组，把上面三个仓库组合在一起对外提供服务，在本地maven基础配settings.xml或项目pom.xml中使用
```

**创建Blob Stores**

```tex
在创建repository之前，还需要先指定文件存储目录，便于统一管理。就需要创建Blob Stores，不创建则使用的是default。
Blob Stores存储目录在/home/nexus/data/blobs/路径下可以看到。
```

![创建blob1][]

![创建blob2][]

**创建仓库**

1. 创建proxy代理仓库
2. 创建hosted托管仓库
3. 创建group组仓库

**组仓库注意仓库顺序**

hosted->proxy->central

```tex
官方文档中建议：

It is recommended practice to place hosted repositories higher in the list than proxy repositories. For proxy repositories, the repository manager needs to check the remote repository which will incur more overhead than a hosted repository lookup.

希望将hosted repositories【托管资源库】的顺序放在proxy repositories【代理资源库】之前，因为一个group【组资源库】中可以涵括这些托管资源库和代理资源库。而一整个的group是作为一个public，一个接口给别人使用的。所以当查找架包的时候，如果代理资源库在前面，那就是先从远程去查找jar，而不是先从托管资源库（本地仓库）去查找是否有jar。这样访问外网的消耗比起来在本地查找，当然是将托管资源库放在代理资源库之前的优先位置了。
```
