docker的使用
============

启动docker
----------

.. code:: shell

    /etc/init.d/docker start

docker镜像
----------

搜索镜像

::

    docker search centos

下面是部分结果，分别对应，镜像名称，详细信息，星级（受欢迎程度），是否官方镜像，是否自动创建。

根据是否是官方提供，可将镜像资源分为两类。

一种是类似 centos 这样的基础镜像，被称为基础或根镜像。这些基础镜像是由
Docker 公司创建、验证、支持、提供。这样的镜像往往使用单个单词作为名字。

还有一种类型，比如 ansible/centos7-ansible 镜像，它是由 Docker
的用户创建并维护的，往往带有用户名称前缀。

::

       镜像名称                       详细信息                                         星级    是否官方镜像  是否自动创建
       NAME                          DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
       centos                        The official build of CentOS.                   2592      [OK]
       ansible/centos7-ansible       Ansible on Centos7                              83                   [OK]
       jdeathe/centos-ssh            CentOS-6 6.8 x86_64 / CentOS-7 7.2.1511 x8...   28                   [OK]
       nimmis/java-centos            This is docker images of CentOS 7 with dif...   14                   [OK]
       gluster/gluster-centos        Official GlusterFS Image [ CentOS7 +  Glus...   12                   [OK]
       million12/centos-supervisor   Base CentOS-7 with supervisord launcher, h...   12                   [OK]

获取centos的docker镜像，由于cento7版本的systemd问题，导致安装的服务无法正常启动，这里选择centos:6版本，版本在search时无法找到，只能通过https://hub.docker.com
查找，下图为centos的版本，不过centos7的systemd问题，官方给的也有解决方案。
centos7的systemd问题官方文档

.. code:: shell

       Dockerfile for systemd base image

           FROM centos:7
           MAINTAINER "you" <your@email.here>
           ENV container docker
           RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i ==
           systemd-tmpfiles-setup.service ] || rm -f $i; done); \
           rm -f /lib/systemd/system/multi-user.target.wants/*;\
           rm -f /etc/systemd/system/*.wants/*;\
           rm -f /lib/systemd/system/local-fs.target.wants/*; \
           rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
           rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
           rm -f /lib/systemd/system/basic.target.wants/*;\
           rm -f /lib/systemd/system/anaconda.target.wants/*;
           VOLUME [ "/sys/fs/cgroup" ]
           CMD ["/usr/sbin/init"]

       This Dockerfile deletes a number of unit files which might cause issues. From here, you are ready to build your base image.

           $ docker build --rm -t local/c7-systemd .

       Example systemd enabled app container
       In order to use the systemd enabled base container created above, you will need to create your Dockerfile similar to the one below.

           FROM local/c7-systemd
           RUN yum -y install httpd; yum clean all; systemctl enable httpd.service
           EXPOSE 80
           CMD ["/usr/sbin/init"]

       Build this image:

           $ docker build --rm -t local/c7-systemd-httpd

       Running a systemd enabled app container
       In order to run a container with systemd, you will need to mount the cgroups volumes from the host. Below is an example command that will run the systemd enabled httpd container created earlier.

           $ docker run -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -p 80:80 local/c7-systemd-httpd

       This container is running with systemd in a limited context, with the cgroups filesystem mounted. There have been reports that if you’re using an Ubuntu host, you will need to add -v /tmp/$(mktemp -d):/run in addition to the cgroups mount.

获取centos6镜像
---------------

::

    docker pull centos:6

列出已经获取到的docker镜像
--------------------------

.. code:: shell

    [root@localhost ~]# docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    centos              6                   273a1eca2d3a        4 weeks ago         194.6 MB

说明

-  REPOSITORY:来自于哪个仓库，比如 centos
-  TAG:镜像的标记，一般修改版本号
-  IMAGE ID:镜像的id号
-  CREATED：创建镜像的时间
-  VIRTUAL SIZE：镜像的大小

启动一个docker容器
------------------

.. code:: shell

    [root@localhost ~]# docker run -i -t centos:6 /bin/bash

    -i:开启交互式shell
    -t:为容器分配一个伪tty终端
    centos：指定镜像的名字,后面的数字表示版本
    /bin/bash：运行/bin/bash

运行命令测试

.. code:: shell

    [root@65b6cf94133f /]# ifconfig
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:01
              inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
              inet6 addr: fe80::42:acff:fe11:1/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:6 errors:0 dropped:0 overruns:0 frame:0
              TX packets:7 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:468 (468.0 b)  TX bytes:558 (558.0 b)
    lo        Link encap:Local Loopback
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)
    [root@65b6cf94133f /]# cat /etc/centos-release
    CentOS release 6.8 (Final)

退出docker镜像
--------------

因为我们只是启动了一个bash,所以当我们退出的时候,镜像也停止了。

如果想容器不退出，可以使用 ``Ctrl+P+Q进行``, 使用bash run的容器也可以不退出

::

    [root@65b6cf94133f /]# exit

查看docker镜像的状态
--------------------

.. code:: shell

    [root@localhost ~]# docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
    65b6cf94133f        centos:6            "/bin/bash"         About a minute ago   Exited (0) 16 seconds ago                       evil_engelbart
    -a表示列出所有的容器，STATUS如果为Exited为退出，UP为运行。

启动刚才退出的docker镜像
------------------------

启动时跟ID启动，也可以使用NAME启动。

.. code:: shell

    [root@localhost ~]# docker start 65b6cf94133f
    [root@localhost ~]# docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    65b6cf94133f        centos:6            "/bin/bash"         4 minutes ago       Up 53 seconds                           evil_engelbart

停止docker容器
--------------

::

    [root@localhost ~]# docker stop 65b6cf94133f

自动重启
--------

故障处理, ``--restart``\ 参数, 支持三种逻辑实现

.. code:: shell

    no：容器退出时不重启
    on-failure：容器故障退出（返回值非零）时重启
    always：容器退出时总是重启

    示例
    --restart=always

以守护进程的方式运行docker容器
------------------------------

使用-d参数

.. code:: shell

    [root@localhost ~]# docker run -d --name docker-daemon centos:6  /bin/bash -c "while true; do echo hello world; sleep 1; done"
    # -l 查看最近一次修改的docker容器
    [root@localhost ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
    5f117a57a13c        centos:6            "/bin/bash -c 'while   25 seconds ago      Up 24 seconds                           docker-daemon

进入docker容器
--------------

以守护进程的方式运行时，进入容器对容器进行操作。

首先启动docker

.. code:: shell

    [root@localhost ~]# docker run -dit centos:6
    [root@localhost ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    22fa39e2bb08        centos:6            "/bin/bash"         12 seconds ago      Up 11 seconds                           stoic_mayer

使用docker自带命令进入容器
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用docker自带的命令进入容器,可以跟名字，或者CONTAINER ID登录

::

    [root@localhost ~]# docker attach stoic_mayer

docker attach 是Docker自带的命令，但是使用 attach
命令，开多个窗口同时，所有窗口都会同步显示操作。当某个窗口因命令阻塞时,其他窗口也无法执行操作了，退出时如果使用exit或者ctrl+c也会关闭docker容器，使用快捷键先按ctrl+p,再按ctrl+q。

使用exec进入容器
~~~~~~~~~~~~~~~~

::

    docker exec -it 22fa39e2bb08 bash

使用nsenter命令
~~~~~~~~~~~~~~~

查询是否安装util-linux软件包，如果没有需要安装。

.. code:: shell

    [root@localhost ~]# rpm -qf `which nsenter`
    util-linux-ng-2.17.2-12.18.el6.x86_64

安装

.. code:: shell

    wget https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz; tar xzvf util-linux-2.24.tar.gz
    cd util-linux-2.24
    ./configure --without-ncurses && make nsenter
    cp nsenter /usr/local/bin

使用nsenter命令的话，需要找到运行的docker容器的pid才可进入。

.. code:: shell

    [root@localhost ~]# docker inspect --format "{{ .State.Pid }}"  22fa39e2bb08
    2759

进入容器

.. code:: shell

    [root@localhost ~]# nsenter --target 2759 --mount --uts --ipc --net --pid

自定义docker镜像
----------------

使用已有的容器生成镜像

进入容器
~~~~~~~~

::

    [root@localhost ~]# nsenter --target 2759 --mount --uts --ipc --net --pid

在镜像里安装httpd

.. code:: shell

    yum -y install httpd
    chkconfig httpd on
    exit

提交镜像
~~~~~~~~

.. code:: shell

    docker commit -m "centos http  server" 22fa39e2bb08  yy/httpd:v1

    -m：指定提交的说明信息
    22fa39e2bb08:容器的id
    yy/httpd:v1:指定仓库名和TAG信息

使用新提交的镜像启动docker容器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

    [root@localhost ~]# docker run -dit -p 80:80 yy/httpd:v1 /sbin/init

    ‘-p’:端口映射，第一个80为本地端口，第二个80为docker容器端口
    yy/httpd:v1:刚才提交的镜像名称
    /sbin/init:启动init程序，由它去进行chkconfig http on的操作
    正常情况下访问本机的ip即可访问docker容器中的http服务器

.. code:: shell

    [root@localhost ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                NAMES
    0c5dcf434a01        yy/httpd:v1    "/sbin/init"        4 minutes ago       Up 4 minutes        0.0.0.0:80->80/tcp   sleepy_engelbart
    [root@localhost ~]# netstat -tnlp|grep 80
    tcp        0      0 :::80                       :::*                        LISTEN      4499/docker-proxy

使用 Dockerfile 来创建镜像
--------------------------

创建dockerfile文件
~~~~~~~~~~~~~~~~~~

.. code:: shell

    mkdir docker-file
    cd docker-file/
    touch dockerfile

文件内容

.. code:: shell

    [root@localhost docker-file]# cat dockerfile
    # This is a comment
    FROM centos:6
    MAINTAINER Docker New <new@docker.com>
    RUN yum -y install httpd
    RUN chkconfig httpd on
    EXPOSE 80
    CMD ["/sbin/init"]

说明

.. code::

    FROM:指令告诉 Docker 使用哪个镜像作为基础
    MAINTAINER:维护者的信息
    RUN:要执行的操作
    EXPOSE:向外部开放端口
    CMD:命令来描述容器启动后运行的程序

生成镜像
~~~~~~~~

::

    [root@localhost docker-file]# docker build -t "yy/httpd:v2" .

其中 -t 标记来添加 tag，指定新的镜像的用户信息。“.”是 Dockerfile
所在的路径（当前目录），也可以替换为一个具体的 Dockerfile 的路径。

使用生成镜像启动容器
~~~~~~~~~~~~~~~~~~~~

::

       [root@localhost docker-file]# docker run -dit -p 81:80 yy/httpd:v2

查看状态

.. code:: shell

    [root@localhost docker-file]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                NAMES
    a2ef76ef4694        yy/httpd:v2    "/sbin/init"        49 seconds ago      Up 48 seconds       0.0.0.0:81->80/tcp   determined_colden
    [root@localhost docker-file]# netstat -tunlp|egrep "81|80"
    tcp        0      0 :::80                       :::*                        LISTEN      4499/docker-proxy
    tcp        0      0 :::81                       :::*                        LISTEN      6987/docker-proxy

删除
----

如果强制删除使用-f参数

删除镜像
~~~~~~~~

::

    docker rmi 镜像ID

删除容器
~~~~~~~~

::

    docker rm 容器id

导入导出
--------

导出镜像
~~~~~~~~

查看有哪些镜像

.. code:: shell

    [root@localhost ~]# docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    yy/httpd       v2                  05f550f29746        8 hours ago         292.4 MB
    yy/httpd       v1                  beda3a6b9e94        8 hours ago         292.4 MB
    centos              6                   273a1eca2d3a        4 weeks ago         194.6 MB
    centos              latest              d83a55af4e75        4 weeks ago         196.7 MB

导出镜像

::

    [root@localhost ~]# docker save -o httpd.tar yy/httpd:v2

导入镜像
~~~~~~~~

::

    [root@localhost ~]# docker load < httpd.tar

导出容器
~~~~~~~~

查看镜像列表，选择最近一次的容器，也可使用-a选择任意容器

.. code:: shell

    [root@localhost ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                NAMES
    a2ef76ef4694        yy/httpd:v2    "/sbin/init"        8 hours ago         Up 8 hours          0.0.0.0:81->80/tcp   determined_colden

停止该容器

::

    [root@localhost ~]# docker stop a2ef76ef4694

导出容器

::

    [root@localhost ~]# docker export a2ef76ef4694 > httpdv2.tar

删除容器

::

       [root@localhost ~]# docker rm a2ef76ef4694

导入容器到镜像
~~~~~~~~~~~~~~

::

    [root@localhost ~]# docker import - yy/httpd:v3 < httpdv2.tar

\*注：用户既可以使用 docker load
来导入镜像存储文件到本地镜像库，也可以使用 docker import
来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。

数据卷
------

挂载本机的目录作为数据卷
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

    [root@localhost ~]# docker run -dit -h apache  -v /opt:/opt  yy/httpd:v3 /bin/bash
    e4c0213e8e0b0c067296abc6f84a83ea00ea71dd287e49b827e88871ed27ad30

在本地创建文件到容器内查看

.. code:: shell

    [root@localhost ~]# cd /opt/
    [root@localhost opt]# touch good
    [root@localhost opt]# docker attach e4c0213e8e0b
    [root@apache /]# ls opt/
    good  rh

挂载本机文件作为数据卷
~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

    [root@localhost opt]# docker run -it --rm -h apache  -v /etc/hosts:/opt/hosts:ro  yy/httpd:v3 /bin/bash

ro:可以设置为只读

也可以在dockerfile中指定，参考上面的官方文档例子

::

    VOLUME [ "/sys/fs/cgroup" ]

网络
----

网络模式

https://www.cnblogs.com/gispathfinder/p/5871043.html

端口映射
~~~~~~~~

把docker里面的端口映射成为本机端口，可以通过 -P 或 -p
参数来指定端口映射。

映射端口到全部ip的80端口

::

    [root@localhost opt]# docker run  -dit -p 80:80 yy/httpd:v3 /sbin/init

如果物理机有多个ip，映射到某一ip的80端口

::

    [root@localhost opt]# docker run  -dit -p 127.0.0.1:80:80 yy/httpd:v3 /sbin/init

使用 udp 标记来指定 udp 端口

::

    [root@localhost opt]# docker run  -dit -p 127.0.0.1:80:80/udp yy/httpd:v3 /sbin/init

容器间互联
~~~~~~~~~~

创建容器时–name对容器命名。

::

    [root@localhost opt]# docker run  -dit --name web1  yy/httpd:v3 /bin/bash

查看命名的容器

.. code:: shell

    [root@localhost ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    ef5abf27a922        yy/httpd:v3    "/bin/bash"         7 seconds ago       Up 7 seconds                            web1

也可使用docker inspect查看

.. code:: shell

    [root@localhost opt]# docker inspect -f "{{ .Name }}"  ef5abf27a922
    /web1

注意：容器的名称是唯一的。如果已经命名了一个叫 web
的容器，当你要再次使用 web 这个名称的时候，需要先用docker
rm来删除之前创建的同名容器。

在执行docker
run的时候如果添加–rm标记，则容器在终止后会立刻删除。注意，–rm和 -d
参数不能同时使用。

::

    [root@localhost ~]# docker run  -dit --name web2 --link web1:web2toweb1  yy/httpd:v3 /bin/bash

登录测试

.. code:: shell

           [root@localhost ~]# docker attach web2
           [root@86a512d57ac7 /]# ping web1
           PING web2toweb1 (172.17.0.21) 56(84) bytes of data.
           64 bytes from web2toweb1 (172.17.0.21): icmp_seq=1 ttl=64 time=1.19 ms
           64 bytes from web2toweb1 (172.17.0.21): icmp_seq=2 ttl=64 time=0.058 ms
