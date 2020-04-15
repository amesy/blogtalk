---
title: "Docker镜像仓库Harbor搭建及配置"
date: 2018-07-14T18:31:40+08:00
lastmod: 2018-07-14T18:31:40+08:00
hidden: false
draft: false
tags: ["Docker","Harbor"]
description: ""
slug: "Set up the harbor"
---

<!--more--> 

Harbor - 基于Docker Distribution的企业级Registry服务。

### Harbor介绍

Harbor是一个由VMware公司开源的企业级的Docker Registry管理项目。它是一个用于存储和分发Docker镜像的企业级Registry服务器，通过添加一些企业必需的功能特性，例如安全、标识和管理等，扩展了开源Docker Distribution。作为一个企业级私有Registry服务器，Harbor提供了更好的性能和安全。提升用户使用Registry构建和运行环境传输镜像的效率。Harbor支持安装在多个Registry节点的镜像资源复制，镜像全部保存在私有Registry中， 确保数据和知识产权在公司内部网络中管控。另外，Harbor也提供了高级的安全特性，诸如用户管理，访问控制和活动审计等。

-   **基于角色的访问控制** - 用户与Docker镜像仓库通过“项目”进行组织管理，一个用户可以对多个镜像仓库在同一命名空间（project）里有不同的权限。
-   **镜像复制** - 镜像可以在多个Registry实例中复制（同步）。尤其适合于负载均衡，高可用，混合云和多云的场景。
-   **图形化用户界面** - 用户可以通过浏览器来浏览，检索当前Docker镜像仓库，管理项目和命名空间。
-   **AD/LDAP 支持** - Harbor可以集成企业内部已有的AD/LDAP，用于鉴权认证管理。
-   **审计管理** - 所有针对镜像仓库的操作都可以被记录追溯，用于审计管理。
-   **国际化** - 已拥有英文、中文、德文、日文和俄文的本地化版本。更多的语言将会添加进来。
-   **RESTful API** - RESTful API 提供给管理员对于Harbor更多的操控, 使得与其它管理软件集成变得更容易。
-   **部署简单** - 提供在线和离线两种安装工具， 也可以安装到vSphere平台(OVA方式)虚拟设备。


### 安装Harbor

#### 环境准备

-   CentOS：7.4


-   Docker：17.12.0-ce  
-   Docker-compose： version 1.13.0
-   Harbor： version 1.1.2

#### 开始安装

**1）安装Docker** 

官网安装文档：[https://docs.docker.com/install/linux/docker-ce/centos/](https://docs.docker.com/install/linux/docker-ce/centos/)

**2）安装Docker-compose** 

-   下载指定版本的docker-compose 

    ```shell 
    $ curl -L https://github.com/docker/compose/releases/download/1.13.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    ```

-   对二进制文件赋可执行权限

    ```shell 
    $ sudo chmod +x /usr/local/bin/docker-compose
    ```

-   测试 `docker-compose` 是否安装成功 

    ```shell 
    $ docker-compose --version
    docker-compose version 1.13.0, build 1719ceb
    ```

**3）Harbor服务搭建** 

-   下载安装包并解压缩

    ```shell 
    $ wget https://github.com/vmware/harbor/releases/download/v1.1.2/harbor-online-installer-v1.1.2.tgz
    $ tar xvf harbor-online-installer-v1.1.2.tgz
    ```

-   解压缩之后，可以看到 `harbor.cfg` 就是Harbor的配置文件。 

    ```shell 
    $ ls harbor
    common                     docker-compose.yml     harbor.cfg  LICENSE  prepare
    docker-compose.notary.yml  harbor_1_1_0_template  install.sh  NOTICE   upgrade
    ```

    ```shell 
    $ sudo vim harbor.cfg 

    ## Configuration file of Harbor

    # hostname设置访问地址，可以使用ip、域名，不可以设置为127.0.0.1或localhost。
    hostname = 10.68.7.20

    # 访问协议，默认是http，也可以设置https，如果设置https，则nginx ssl需要设置on
    ui_url_protocol = http

    # mysql数据库root用户默认密码root123，实际使用时修改下
    db_password = root123

    max_job_workers = 3 
    customize_crt = on
    ssl_cert = /data/cert/server.crt
    ssl_cert_key = /data/cert/server.key
    secretkey_path = /data
    admiral_url = NA

    # 邮件设置，发送重置密码邮件时使用
    email_identity = 
    email_server = smtp.mydomain.com
    email_server_port = 25
    email_username = sample_admin@mydomain.com
    email_password = abc
    email_from = admin <sample_admin@mydomain.com>
    email_ssl = false

    # 启动Harbor后，管理员UI登录的密码，默认是Harbor12345
    harbor_admin_password = Harbor12345

    # 认证方式，这里支持多种认证方式，如LADP、本次存储、数据库认证。默认是db_auth，mysql数据库认证
    auth_mode = db_auth

    # LDAP认证时配置项
    #ldap_url = ldaps://ldap.mydomain.com
    #ldap_searchdn = uid=searchuser,ou=people,dc=mydomain,dc=com
    #ldap_search_pwd = password
    #ldap_basedn = ou=people,dc=mydomain,dc=com
    #ldap_filter = (objectClass=person)
    #ldap_uid = uid 
    #ldap_scope = 3 
    #ldap_timeout = 5

    # 是否开启自注册
    self_registration = on

    # Token有效时间，默认30分钟
    token_expiration = 30

    # 用户创建项目权限控制，默认是everyone（所有人），也可以设置为adminonly（只能管理员）
    project_creation_restriction = everyone

    verify_remote_cert = on
    ```

**4）启动 Harbor** 

修改完配置文件后，在的当前目录执行`./install.sh`，Harbor服务就会根据当期目录下的`docker-compose.yml`开始下载依赖的镜像，检测并按照顺序依次启动各个服务。 

```shell 
$ ./install.sh

... 

✔ ----Harbor has been installed and started successfully.----

Now you should be able to visit the admin portal at http://10.68.7.20	. 
For more details, please visit https://github.com/vmware/harbor .
```

**Harbor依赖的镜像及启动服务如下：** 

```shell 
$ docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
hello-world                 latest              f2a91732366c        7 months ago        1.85kB
vmware/harbor-jobservice    v1.1.2              4ef0a7a33734        13 months ago       163MB
vmware/harbor-ui            v1.1.2              4ee8f190f366        13 months ago       183MB
vmware/harbor-adminserver   v1.1.2              cdcf1bed7eb4        13 months ago       142MB
vmware/harbor-db            v1.1.2              fcb8aa7a0640        13 months ago       329MB
vmware/registry             2.6.1-photon        0f6c96580032        14 months ago       150MB
vmware/nginx                1.11.5-patched      8ddadb143133        15 months ago       199MB
vmware/harbor-log           v1.1.2              9c46a7b5e517        16 months ago       192MB

$ docker-compose ps
       Name                     Command               State                                Ports                               
------------------------------------------------------------------------------------------------------------------------------
harbor-adminserver   /harbor/harbor_adminserver       Up                                                                       
harbor-db            docker-entrypoint.sh mysqld      Up      3306/tcp                                                         
harbor-jobservice    /harbor/harbor_jobservice        Up                                                                       
harbor-log           /bin/sh -c crond && rm -f  ...   Up      127.0.0.1:1514->514/tcp                                          
harbor-ui            /harbor/harbor_ui                Up                                                                       
nginx                nginx -g daemon off;             Up      0.0.0.0:443->443/tcp, 0.0.0.0:4443->4443/tcp, 0.0.0.0:80->80/tcp 
registry             /entrypoint.sh serve /etc/ ...   Up      5000/tcp      
```

启动完成后，通过访问配置文件设置的 hostname <http://10.68.7.20/>，默认是80端口。如果端口占用，可以去修改 `docker-compose.yml`文件中对应服务的端口映射，然后再次执行 `./install.sh` 即可。

如修改端口映射为本地的81端口对应容器的80端口： 

```shell 
vim docker-compose.yml 
...
ports:
  - 81:80
  - 443:443
  - 4443:4443
```

然后再次执行 `./install.sh`： 

```shell 
./install.sh
```

然后执行 `docker-compose ps` 就可以看到相关映射端口的信息。 

**浏览器访问：** 

注：更改或清空防火墙规则，否则访问时会报405错误。 

![img01](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img01.png)

### 登录 Web Harbor

用户名为 `admin`， 密码为 `docker-compose.cfg` 中 admin 对应的密码。 

登录完后界面如下图所示： 

![img02](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img02.png)   

可以看到系统各个模块如下：

-   项目：新增/删除项目，查看镜像仓库，给项目添加成员、查看操作日志、复制项目等。
-   日志：仓库各个镜像create、push、pull等操作日志。
-   系统管理
    -   用户管理：新增/删除用户、设置管理员等
    -   复制管理：新增/删除从库目标、新建/删除/启停复制规则等
    -   配置管理：认证模式、复制、邮箱设置、系统设置等
-   其他设置
    -   用户设置：修改用户名、邮箱、名称信息
    -   修改密码：修改用户密码

注：非系统管理员用户登录，只能看到有权限的项目和日志，其他模块不可见。

### 新建项目

新建一个项目，可以选择设置项目为公开或不公开。此处我创建了两个仓库，一个私有仓库和一个公开仓库： 

![img03](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img03.png) 

注意：当项目设为公开后，任何人都有此项目下镜像的读权限。命令行用户不需要 `docker login` 就可以拉取此项目下的镜像。

新建项目完毕后，我们就可以用admin账户提交本地镜像到Harbor仓库了。例如提交本地nginx镜像：

注：此处容器映射端口由 `81` 换回 `80` 。

```shell 
# 1、admin登录 
$ docker login 10.68.7.20
Username (admin): admin
Password: 
Login Succeeded

# 2、给镜像打tag 
$ docker tag nginx 10.68.7.20/docker_private/nginx:latest
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
10.68.7.20/docker_private/nginx   latest              e548f1a579cf        4 months ago        109MB
nginx                             latest              e548f1a579cf        4 months ago        109MB

# 3、push到仓库 
$ docker push 10.68.7.20/docker_private/nginx
The push refers to repository [10.68.7.20/docker_private/nginx]
e89b70d28795: Pushed 
832a3ae4ac84: Pushed 
014cf8bfcb2d: Pushed 
latest: digest: sha256:600bff7fb36d7992512f8c07abd50aac08db8f17c94e3c83e47d53435a1a6f7c size: 948
```

注：此处第一步，执行本地登录操作可能会报错：

```shell 
$ docker login 10.68.7.20
Username: admin
Password: 
Error response from daemon: Get https://10.68.7.20/v2/: read tcp 10.68.7.20:34348->10.68.7.20:443: read: connection reset by peer
```

解决办法： 

```shell 
$ vim /etc/docker/daemon.json 
# 添加该行。
{ "insecure-registries":["10.68.7.20"] }
```

然后重启 docker 服务即可。

CentOS或Ubuntu系统均可采用该方法解决。

上传完毕后，登录Web Harbor，选择对应项目名称 docker_private，就可以查看到刚才上传的 nginx image。 

![img04](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img04.png) 

### 创建用户并分配权限

我们刚一直是用 `admin` 操作，实际应用中我们使用每个人自己的账户登录。所以就需要新建用户，同时为了让用户有权限操作已经创建的项目，还必须将该用户添加到所属项目成员中。 

创建用户名为 amesy 的测试用户，点击系统管理 ->用户管理 -> 创建用户，输入用户名、邮箱、密码等信息。

![img05](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img05.png) 

将 amesy 用户添加到 docker_private 项目成员中，点击项目 -> docker_private -> 成员 -> 新建成员，填写姓名，选择角色。

![img06](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img06.png) 

现在我们使用 amesy 账户在本地模拟操作 pull 刚上传的nginx镜像。

**1、先移除tag** 

```shell 
$ docker rmi -f e548f1a579cf
Untagged: 10.68.7.20/docker_private/nginx:latest
Untagged: 10.68.7.20/docker_private/nginx@sha256:600bff7fb36d7992512f8c07abd50aac08db8f17c94e3c83e47d53435a1a6f7c
Untagged: nginx:latest
Untagged: nginx@sha256:4771d09578c7c6a65299e110b3ee1c0a2592f5ea2618d23e4ffe7a4cab1ce5de
Deleted: sha256:e548f1a579cf529320a3c1d8789399e5fea0bfaa04cbb70d03890afafb748a2f
Deleted: sha256:8a75dc522d5a065b4cb16e88a34bbaf263b556e6b391def1f0e1d0570f304846
Deleted: sha256:9fd6be84ea2038d2a8c3d8bd369b2b51a4a19cb14af4ee7ac5dedd1e702d0b9c
Deleted: sha256:014cf8bfcb2d50b7b519c4714ac716cda5b660eae34189593ad880dc72ba4526
```

**2、退出 admin 账号，使用 amesy 账户登录** 

```shell 
$ docker logout 10.68.7.20
Removing login credentials for 10.68.7.20
$ docker login 10.68.7.20
Username: amesy
Password: 
Login Succeeded
```

**3、pull 相应镜像到本地** 

```shell 
# docker pull 10.68.7.20/docker_private/nginx:latest
latest: Pulling from docker_private/nginx
8176e34d5d92: Pull complete 
5b19c1bdd74b: Pull complete 
4e9f6296fa34: Pull complete 
Digest: sha256:600bff7fb36d7992512f8c07abd50aac08db8f17c94e3c83e47d53435a1a6f7c
Status: Downloaded newer image for 10.68.7.20/docker_private/nginx:latest
```

此时，通过 `docker images` 命令就可以查看到 pull 到本地的镜像。 

### 配置 Docker 镜像复制

首先提供至少两台Harbor服务： 

Harbor1：10.68.7.20

Harbor2：10.68.7.30

之前的操作已经往Harbor1上面push了一个镜像，那么就把Harbor1当做主节点，Harbor2当做复制节点，我们要把Harbor1上的镜像自动复制到Harbor2上去。

**配置复制规则**

点击项目 -> docker_private -> 复制 -> 新建复制规则，填写名称、目标名、目标URL等信息。

注：目标URL这里指Harbor2的地址，用户名和密码为Harbor2配置的admin账户和密码，一旦勾选启用，那么新建复制规则完成后，立马就会检测需要同步的image自动同步。

![img07](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img07.png) 

然后查看Harbor2节点信息，可以看到数据镜像已同步完成，对应的日志信息等也已生成。

![img08](https://raw.githubusercontent.com/amesy/blogtalk/master/blogImages/post/Set-up-the-Harbor/img08.png) 

之后，因为配置了复制规则的缘故，Harbor1的 docker_private 项目下有新的镜像生成，就会自动同步到 Harbor2 的 docker_private 项目下。

参考文档：

-   https://vmware.github.io/harbor/cn/
-   https://github.com/vmware/harbor/


