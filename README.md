# Docker的常用命令

## 帮助命令

``` shell
docker version  #显示版本信息
docker info     #显示详细信息
docker 命令 --help  #万能帮助命令
```



## 镜像命令

**docker images** 查看所有本地主机上的镜像

```shell
# root @ VM-0-17-centos in ~ [16:50:14] 
$ docker images                         
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
mysql         latest    0627ec6901db   4 weeks ago    556MB
ubuntu        latest    26b77e58432b   6 weeks ago    72.9MB
hello-world   latest    d1165f221234   2 months ago   13.3kB

#解释
RESPOSITORY 镜像的仓库源
TAG 		镜像的标签
IMAGE ID 	镜像的ID
CREATED 	镜像的创建时间
SIZE 		镜像的大小

#可选项
	-a, --all 		#列出所有镜像
	-q, --quiet		#只显示镜像的id
```

**docker search** 搜索镜像

```shell
# root @ VM-0-17-centos in ~ [16:54:37] 
$ docker search mysql     
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10899     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4110      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   809                  [OK]

#可选项，通过收藏来过滤
--filter=STARS=3000  	#搜索出的镜像为STARS大于3000的
# root @ VM-0-17-centos in ~ [16:55:30] C:1
$ docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10899     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4110      [OK]       
```

**docker pull** 下载镜像

```shell
# root @ VM-0-17-centos in ~ [16:57:10] 
$ docker pull mysql                      
Using default tag: latest	#如果不写tag，默认就是latest
latest: Pulling from library/mysql
69692152171a: Pull complete 	#分层下载，docker image 的核心 联合文件系统
1651b0be3df3: Pull complete 
951da7386bc8: Pull complete 
0f86c95aa242: Pull complete 
37ba2d8bd4fe: Pull complete 
6d278bb05e94: Pull complete 
497efbd93a3e: Pull complete 
f7fddf10c2c2: Pull complete 
16415d159dfb: Pull complete 
0e530ffc6b73: Pull complete 
b0a4a1a77178: Pull complete 
cd90f92aa9ef: Pull complete 
Digest: sha256:d50098d7fcb25b1fcb24e2d3247cae3fc55815d64fec640dc395840f8fa80969
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest	#真实地址

#等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本下载
$ docker pull mysql:5.7
5.7: Pulling from library/mysql
69692152171a: Already exists 
1651b0be3df3: Already exists 
951da7386bc8: Already exists 
0f86c95aa242: Already exists 
37ba2d8bd4fe: Already exists 
6d278bb05e94: Already exists 
497efbd93a3e: Already exists 
a023ae82eef5: Pull complete 
e76c35f20ee7: Pull complete 
e887524d2ef9: Pull complete 
ccb65627e1c3: Pull complete 
Digest: sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

![image-20210520170321057](README.assets/image-20210520170321057.png)

 

**docker rmi** 删除镜像

```shell
$ docker rmi -f 0627ec6901db	#删除指定id的容器                                             
$ docker rmi -f 容器id 容器id 容器id	#删除多个容器
$ docker rmi -f $(docker images -aq)	#删除全部的容器
```



## 容器命令

**说明：我们有了镜像才可以创建容器，linux下载一个ubuntu镜像来测试学习**

```shell
docker pull ubuntu
```

**新建容器并启动**

```shell
docker run [可选参数] image

#参数说明
--name="Name"  容器名字 tomcat01 tomcat02 用来区分容器
-d			   后台方式运行
-it			   使用交互方式运行，进入容器查看内容
-p			   指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口	(常用)
	-p 容器端口
-P			   随机指定端口

#测试，启动并进入容器
$ docker run -it ubuntu /bin/bash                                        
root@a173e0a7b47a:/# 
root@a173e0a7b47a:/# ls  #查看容器内的ubuntu
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#从容器中退回主机
root@a173e0a7b47a:/# exit
exit
```

**列出所有正在运行的容器**

```shell
#docker ps
-a, --all             #列出当前正在运行+历史运行的容器
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int       #显示最近创建的容器
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           #只显示容器的编号
  -s, --size            Display total file sizes
```

**退出容器**

```shell
exit	#直接停止容器并退出
Crtl + P + Q	#容器不停止退出
```

**删除容器**

```shell
docker rm 容器id	#删除指定容器,不能删除正在运行的容器
docker rm -f $(docker ps -aq)	#删除所有的容器
docker ps -a -q | xargs docker rm -f #删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id	#启动容器
docker restart 容器id	#重启容器
docker stop 容器id	#停止当前正在运行的容器
docker kill 容器id	#强制停止当前容器
```



## 常用其它命令

**后台启动容器**

```shell
#命令 docker run -d 镜像名
$ docker run -d ubuntu                                                   
c21cd5dd2594ec109dfb7e8eeba6bd129291de1f1095389c9b31492e98360947

#问题docker ps，发现ubuntu停止了

#常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用了，就会自动停止
#nginx，容器启动后发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
docker logs -f -t --tail [num] 容器

#没有日志，自己编写shell脚本
$ docker run -d ubuntu /bin/bash -c "while true;do echo hello;sleep 1;done"
#查看进程
$ docker ps                                                                
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
454ed06e28cb   ubuntu    "/bin/bash -c 'while…"   11 seconds ago   Up 10 seconds             focused_benz
#查看日志
-tf
--tail number #要显示日志条数

$ docker logs -f -t --tail 10 454ed06e28cb            
2021-05-20T09:42:28.697246635Z hello
2021-05-20T09:42:29.698190655Z hello
2021-05-20T09:42:30.699146088Z hello
2021-05-20T09:42:31.700095424Z hello
2021-05-20T09:42:32.700998382Z hello
2021-05-20T09:42:33.702032016Z hello
2021-05-20T09:42:34.702959477Z hello
2021-05-20T09:42:35.703901694Z hello
2021-05-20T09:42:36.704853291Z hello
2021-05-20T09:42:37.705818654Z hello
2021-05-20T09:42:38.706799062Z hello
2021-05-20T09:42:39.707778758Z hello
2021-05-20T09:42:40.708738878Z hello
2021-05-20T09:42:41.709711728Z hello
```

**查看容器中进程信息** top

```shell
#命令 docker top 容器id
$ docker top 8196ca9cbf32            
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                31218               31201               0                   17:51               ?                   00:00:00            /bin/bash -c while true;do echo hello;sleep 1;done
root                31295               31218               0                   17:51               ?                   00:00:00            sleep 1
```

**查看镜像的元数据**

```shell
#命令 docker inspect 容器id

$ docker inspect 8196ca9cbf32 
[
    {
        "Id": "8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522",
        "Created": "2021-05-20T09:51:37.707444282Z",
        "Path": "/bin/bash",
        "Args": [
            "-c",
            "while true;do echo hello;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 31218,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-05-20T09:51:38.059012857Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:26b77e58432b01665d7e876248c9056fa58bf4a7ab82576a024f5cf3dac146d6",
        "ResolvConfPath": "/var/lib/docker/containers/8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522/hostname",
        "HostsPath": "/var/lib/docker/containers/8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522/hosts",
        "LogPath": "/var/lib/docker/containers/8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522/8196ca9cbf32705a557c39b3b8dae4373f07cf1f3450619a0287b5ae1f179522-json.log",
        "Name": "/vigilant_wilbur",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/b22a6747c9d3fb5a282e94726c359d154003b76999b438ee0f4ba801d20fbb34-init/diff:/var/lib/docker/overlay2/8aef4b49c62b0b2cf18a6c3151f903465a6a3fc5d46b9a7d11be860c615039cc/diff:/var/lib/docker/overlay2/dc9dbe45eab2321f7737e6582cee91747d34ef68edc45c2d84a637d9ee0882d7/diff:/var/lib/docker/overlay2/a75cff0ed349d987caab4695bd0edb3e71e70da3754dfee9386e9ba89a3da674/diff",
                "MergedDir": "/var/lib/docker/overlay2/b22a6747c9d3fb5a282e94726c359d154003b76999b438ee0f4ba801d20fbb34/merged",
                "UpperDir": "/var/lib/docker/overlay2/b22a6747c9d3fb5a282e94726c359d154003b76999b438ee0f4ba801d20fbb34/diff",
                "WorkDir": "/var/lib/docker/overlay2/b22a6747c9d3fb5a282e94726c359d154003b76999b438ee0f4ba801d20fbb34/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "8196ca9cbf32",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash",
                "-c",
                "while true;do echo hello;sleep 1;done"
            ],
            "Image": "ubuntu",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "de7ab1ec3092d62d4017bcc77f94f2f9aa86e95f175b037a337c8c3a88747776",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/de7ab1ec3092",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "203d398f0bcdac1f2ced6374053e7b56896418d701644153ecd3171156cc6f9e",
            "Gateway": "172.18.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.18.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:12:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "cea1f96a9599b18ef92650f478f349595b7b5a7a12e49a1b409052d6cbfa3404",
                    "EndpointID": "203d398f0bcdac1f2ced6374053e7b56896418d701644153ecd3171156cc6f9e",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

**进入当前正在运行的容器**

```shell
#我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

#命令
docker exec -it 容器id bashShell
#测试
$ docker exec -it 8196ca9cbf32 /bin/bash           
root@8196ca9cbf32:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@8196ca9cbf32:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 09:51 ?        00:00:00 /bin/bash -c while true;do echo hello;sleep 1;done
root       363     0  0 09:57 pts/0    00:00:00 /bin/bash
root       400     1  0 09:58 ?        00:00:00 sleep 1
root       401   363  0 09:58 pts/0    00:00:00 ps -ef

#方式二
docker attach 容器id
#测试
$ docker attach 8196ca9cbf32
正在执行当前的代码...

#docker exec	#进入容器后开启一个新的终端，可以在里面操作(常用)
#docker attach	#进入容器中正在执行的终端，不会启动新的进程！
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的主机路径
#进入容器中
# root @ VM-0-17-centos in /home [18:06:33] 
$ docker cp 8b84603c410a:/home/test.java /home

# root @ VM-0-17-centos in /home [18:07:05] 
$ ls
alex  arod  hello.java  neos  test.java

#拷贝只是一个手动功能，未来我们使用 -v 卷的技术，可以实现自动同步
```

## 小结

![img2020.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=](README.assets/img2020.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=.jpeg)



## 作业练习

> **作业一：Docker安装nginx**

```shell
#1.搜索镜像 search
#2.下载镜像 pull
#3.运行测试
# root @ VM-0-17-centos in /home [22:51:35] 
$ docker run -d --name nginx01 -p 3344:80 nginx   #映射端口 --name给容器命名， -p 宿主机端口，容器内部端口           
c102d70ac437b7c94182490c8d8085b1e5bec085bd50f95cdd66df54fc6f9470

# root @ VM-0-17-centos in /home [22:52:26] 
$ docker ps                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
c102d70ac437   nginx     "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds   0.0.0.0:3344->80/tcp   nginx01

#4.进入容器查看
$ docker exec -it nginx01 /bin/bash 
root@c102d70ac437:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@c102d70ac437:/# cd /etc/nginx/
root@c102d70ac437:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
```

思考问题：我们每次改动nginx配置文件，都需要进入容器内部，十分麻烦，要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改 -v 数据卷



> **作业二：docker安装tomcat**

```shell
#官方的使用
docker run -it --rm tomcat:9.0

#我们之前的启动都是后台，停止了容器之后，容器还是可以查到， docker run -it --rm，一般用来测试，用完就删除

#下载再启动
docker pull tomcat:9.0

#启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

#测试访问没有问题
#进入容器
$ docker exec -it tomcat01 /bin/bash 
#发现问题：1.linux命令少了；2.没有webapps，云镜像的原因，默认是最小的镜像，所有不必要的都删除掉
#保证最小可运行的环境
```

思考问题：我们以后要部署项目，都需要进入容器内部，十分麻烦，要是可以在容器外部提供一个映射路径，webapps，我们在外部防止项目，就自动同步到内部就好了！



> **作业三：部署es+kibana**

```shell
#es 暴露的端口很多
#es 十分的耗内存
#es 的数据一般需要放置到安全目录挂载
# --net somenetwork 网络配置

#启动
$ docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.12.1

#增加内存限制，修改配置文件 -e 环境配置修改
$ docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.12.1
```

![image-20210521214228600](README.assets/image-20210521214228600.png)



## 可视化

* portainer(先用这个)

  ```shell
  docker run -d -p 8088:9000 \
  --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

* Rancher(CI/CD再用)

  

**什么是portainer？**

Docker图形化界面管理工具，提供一个后台面板供我们操作

```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试： 外网:8088

![image-20210521220756588](README.assets/image-20210521220756588.png)



# Docker镜像讲解

## 镜像是什么

Docker镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含某个软件所需的所有内容，包括代码、库、环境变量、配置文件、运行时环境等。
所有的应用，直接打包成Docker镜像，然后通过镜像创建出容器，然后就可以直接跑起来。

如何得到镜像：

* 从远程仓库下载，比如docker hub、阿里云的镜像仓库等。
* 朋友拷贝给你。
* 自己制作一个镜像DockerFile。

通过对原有的镜像创建的容器进行一些修改（也可以不修改），然后通过Commit命令提交一个新的镜像。
## Docker镜像加载原理
> UnionFS（联合文件系统）

联合文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层叠加，联合文件系统是Docker镜像的基础，镜像可以通过分层来进行继承，基于基础镜像（没有父镜像的镜像）比如centos镜像，可以制作出各种具体应用的镜像，比如mysql镜像。一个成型的应用进行都是由若干个镜像一层层组成的。

> Docker镜像加载原理

docker镜像实际上是由一层层的文件系统组成，这种层级文件系统就是联合文件系统。

bootfs（boot file system）主要包含BootLoader和kernel，BootLoader主要负责引导加载kernel，Linux刚启动时会加载bootfs文件系统来引导内核的加载，Docker镜像的最底层就是bootfs。这一层与我们典型的unix系统是一样的，包含boot引导器和内核，当boot加载完成后整个内核就在内存中了，此时内存的使用权已经由bootfs转交给内核，此时系统会卸载bootfs。

rootfs（root file system）在bootfs之上，包含的就是典型的unix系统的/dev、 /proc、 /etc等标准目录和文件和一些命令，rootfs就是不同unix系统的发行版，比如Ubuntu、centos等。

![image-20210521222150979](README.assets/image-20210521222150979-1625414517335.png)

我们平时安装的虚拟机centos镜像好几个G，Docker安装的才200多m，因为对于一个精简的OS，rootfs可以很小，只需包含最基本的命令，工具和程序库就行了，因为底层直接使用宿主机的内核，自己只需提供rootfs（相当于操作内核的客户端）就可以，由此可见不同发行版的bootfs基本是一致的，roorfs有差别，因此不同的发行版可以公有bootfs。虚拟机是分钟级别，容器是秒级。

![在这里插入图片描述](README.assets/20200820180619143.png)

第一个图仅仅是bootfs+rootfs，然后如果要制作一个emacs环境的镜像，就在这个基础上新加一层emacs镜像，如图二。如果要在添加一个Apache环境，那就再图二基础上加一个apache镜像。如图三。图中的每一层镜像都能进行复用。

## 分层理解

> 分层的镜像

![image-20210521222350269](README.assets/image-20210521222350269.png)

比如：上面的redis镜像。使用docker inspect redis镜像的ID 命令查看镜像的元信息，找到layer信息。

![image-20210521222535031](README.assets/image-20210521222535031.png)

由上图可以看到下载的redis镜像是由6个镜像一层层组成的。

![在这里插入图片描述](README.assets/20200820174621185.png)

这些镜像都是一个个独立可复用的镜像，如果下载其他镜像是，某一层镜像是已经存在本地的了，就不用在下载，直接复用该镜像，节省空间。比如上面下载redis镜像时，提示某个镜像已经存在。

**注意**：
Docker镜像都是只读的，用镜像创建容器启动时，实际上是在原本的镜像上新建了一层可写层到原本镜像的顶部，这一层我们叫作容器层，容器层之下的叫作镜像层。

![在这里插入图片描述](README.assets/20200820181518835.png)


如上图，使用Tomcat镜像创建容器后，会在Tomcat镜像的基础上新建一个可写层，容器的写入是在可写层进行记录，然后使用commit命令把该容器创建一个新的镜像，实际上新的镜像是tomcat镜像+可写层镜像，以tomcat镜像为基础。通过下面介绍使用容器构建镜像，可以更好地理解。

## commit镜像
命令：

```shell
docker commit 提交容器成为一个新的副本
docker commit -m="信息，类似git提交的信息" -a="作者" 容器id  目标镜像名:[tag]
```

实战测试

```shell
#1.启动一个默认的tomcat
docker run -it -p 8080:8080 tomcat
#2.发现这个默认的tomcat是没有webapps的应用，镜像的原因，官方的镜像默认webapps下面是没有文件的

#3.自己拷贝进去基本的文件

#4.将修改后的容器通过commit提交为新的镜像
```

![image-20210521224755933](README.assets/image-20210521224755933.png)

到这里属于入门境界



# 容器数据卷

## 什么是容器数据卷

**docker的理念回顾**

将应用和环境打包成一个镜像

数据如果都在容器中，那么我们容器一旦删除，数据就会丢失  ==需求：数据可以持久化==

 MySQL，容器删了，删库跑路 ==需求：MySQL数据可以存储在本地==

容器之间可以有一个数据共享的技术，Docker容器中产生的数据同步到本地

这就是卷技术，目录的挂载将我们容器内的目录挂载到Linux上面

**容器的持久化和同步操作，容器间也是可以做数据共享的**

## 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
#测试
$ docker run -it -v /home/ceshi:/home ubuntu /bin/bash

#启动后查看挂载情况
$ docker inspect 9de74fa0a4b6 
```

![image-20210524210327691](README.assets/image-20210524210327691.png)

测试文件的同步

![image-20210524210808281](README.assets/image-20210524210808281.png)

![image-20210524211359932](README.assets/image-20210524211359932.png)

好处：我们以后只要在本地进行修改即可，不需要每次都进入容器内部都能实现自动同步



## 实战：安装MySQL

思考：

```shell
#获取镜像
$ docker pull mysql

#运行容器，需要做数据挂载 #安装启动mysql需要配置密码
#官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#启动自己的
-d 后台运行
-p 端口映射
-e 环境配置
--name 容器名字

$ docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=[password] --name mysql01 mysql

#启动成功之后，在本地用数据库连接工具测试连接
#sql可视化工具连接到服务器的3310 ---- 3310 和容器内的3306映射，成功连接

#在本地测试创建一个数据库，发现mysql/data 中增加了数据库数据
```

假设删除了docker镜像

![image-20210524213345565](README.assets/image-20210524213345565.png)

在本地依然有文件映射

![image-20210524213356650](README.assets/image-20210524213356650.png)



## 具名和匿名挂载

```shell
#匿名挂载
-v 容器内挂载
docker run -d -P --name nginx01 -v /etc/nginx nginx

#查看所有的volume的情况
$ docker volume ls   
#这里发现这种就是匿名挂载，我们在 -v 只写了容器内的路径，没有容器名称

#具名挂载
# root @ VM-0-17-centos in /home [10:15:16] 
$ docker run -d -P --name nginx03 -v jumping-nginx:/etc/nginx nginx
b0c308a91e0c1256d28311849d8c4d2adf94bc566c8fffd349adab35e4ace122

# root @ VM-0-17-centos in /home [10:15:37] 
$ docker volume ls                                                 
DRIVER    VOLUME NAME
local     a3a734b0e2fcdd372e5a120d70743f509a3c4e51c29925875b567ef5ae981d6b
local     a7e8e5c5e13f741d7c9742f48e26f12b9a1366de182a161a06fa4e0d55b64f59
local     e7d60d7e7d0b679f92a7f5ed7348a314946fc5a70c78529dccc85715f1bb9b5e
local     jumping-nginx

#通过 -v 卷名：容器内路径
#查看一下这个卷

```

![image-20210524225504807](README.assets/image-20210524225504807.png)

所有docker容器内的卷，没有指定目录的情况下都是在==/var/lib/docker/volumes/xxxx/==

我们通过具名挂载可以方便找到我们的一个卷，大多数情况都使用==具名挂载==

![image-20210620102018751](README.assets/image-20210620102018751.png)

![image-20210620102423407](README.assets/image-20210620102423407.png)

```shell
#如何确定是具名挂载还是匿名挂载，还是指定路径挂载
-v 容器内路径 		 #匿名挂载
-v 卷名:容器内路径	    #具名挂载
-v /宿主机路径::容器内路径  #指定路径挂载
```

拓展

```shell
#通过-v容器内路径，ro rw改变读写权限
ro		read only #只读
rw		read write #可读可写

#一旦这个设置了容器权限，容器对我们挂载出来的内容就有限定了
$ docker run -d -P --name nginx02 -v jumping-nginx:/etc/nginx:ro nginx  
$ docker run -d -P --name nginx02 -v jumping-nginx:/etc/nginx:rw nginx  

#ro 只要看到ro就说明这个路径是只能通过宿主机来操作，容器内部是无法操作
```



## 初识DockerFile

Dockerfile就是用来构建docker镜像的构建文件！也就是命令脚本

通过这个脚本可以生成一个镜像，镜像是一层一层的，脚本一个个的命令对应层数

```shell
#创建一个dockerfile文件，名字可以随意，建议Dockerfile
#文件中的内容 指令（大写） 参数
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "----end----"
CMD /bin/bash    

#这里的每个命令，就是镜像的一层
```

部署容器![image-20210620172543437](README.assets/image-20210620172543437.png)

![image-20210620172609644](README.assets/image-20210620172609644.png)

启动自己的容器

![image-20210620173311293](README.assets/image-20210620173311293.png)

**这个卷和外部一定有一个同步的目录！**

![image-20210620173602742](README.assets/image-20210620173602742.png)

在目录下新建一个测试用的文件

![image-20210620174352103](README.assets/image-20210620174352103.png)

查看卷挂载的路径

![image-20210620174237722](README.assets/image-20210620174237722.png)

测试一下刚才的文件是否同步出去了

![image-20210620174458989](README.assets/image-20210620174458989.png)

成功。

这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像

假设构建镜像时没有挂载卷，要手动镜像挂载 -v 卷名:容器内路径



## 数据卷容器

多个mysql同步数据！

![image-20210620211129209](README.assets/image-20210620211129209.png)

```shell
#启动三个容器，通过我们自己写的镜像启动
```

![image-20210620211430272](README.assets/image-20210620211430272.png)

通过docker01挂载docker02

![image-20210620212157315](README.assets/image-20210620212157315.png)

![image-20210620212343049](README.assets/image-20210620212343049.png)

同理创建一个docker03，在数据卷上新建的文件也会在其它容器上同步

![image-20210620212618218](README.assets/image-20210620212618218.png)

```shell
#只要通过 --volumes-from 我们就可以实现容器间的数据共享

#当删除了docker01时，docker02和docker03经测试依然可以访问建立的文件
```

![image-20210620213449351](README.assets/image-20210620213449351.png)

**多个mysql实现数据共享**

```shell
$ docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=[password] --name mysql01 mysql

$ docker run -d -p 3311:3306 -e MYSQL_ROOT_PASSWORD=[password] --name mysql02 --volumes-from mysql01 mysql

#这个时候可以实现两个容器数据同步
```



结论：

容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用为止。

但是一旦持久化到了本地，这个时候，本地的数据是不会被删除的。



# DockerFile

## DockerFile介绍

Dockerfile是用来构建docker镜像的文件，命令参数脚本

构建步骤：

1. 构建一个dockerfile文件
2. docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DockerHub、阿里云镜像仓库）

查看一下官方是怎么做的

![image-20210705113944949](README.assets/image-20210705113944949.png)



![image-20210705114222367](README.assets/image-20210705114222367.png)

很多官方镜像都是基础包，我们通常会自己搭建自己的镜像。

官方既然可以制作镜像，那我们也可以自己构建。

## DockerFile构建过程

**基础知识：**

1.每个保留关键字（指令）都必须是大写字母

2.执行顺序从上到下

3.#表示注释

4.每一个指令都会创建提交一个新的镜像层，并提交！

<img src="README.assets/pic2.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=.jpeg" alt="img" style="zoom:80%;" />

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单。

步骤：开发、部署、上线运维

Dockerfile：构建文件，定义了一切的步骤，源代码

Dockerimage：通过Dockerfile构建生成的镜像，最终发布和运行的产品

Docker容器：镜像运行起来提供服务

## DockerFile指令

以前的时候我们是使用别人的，现在我们尝试用dockerfile构建一个自己的镜像

```shell
FROM 		#基础镜像，一切从这里开始构建
MAINTAINER	#镜像是谁写的，姓名+邮箱
RUN			#镜像构建时需要运行的命令
ADD			#步骤，tomcat镜像，这个tomcat压缩包；添加内容
WORKDIR		#镜像工作目录
VOLUME		#挂载的目录
EXPOSE		#暴露端口配置
CMD			#指定这个容器启动的时候要运行的命令,只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD		#当构建一个被继承DockerFile 这个时候就会运行 ONBUILD 的指令，触发指令
COPY		#类似ADD,将我们的文件拷贝至镜像中
ENV			#构建的时候设置环境变量
```

![image-20210705115927756](README.assets/image-20210705115927756.png)



## 实战测试

DockerHub中99%的镜像都是从基础镜像过来的 FROM scratch，然后配置需要的软件和配置来进行构建

![image-20210705172214080](README.assets/image-20210705172214080.png)

>创建一个自己的centos

```shell
#1.编写DockerFile的文件
# root @ VM-0-17-centos in /home/dockerfile [17:30:02] 
$ cat mydockerfile-centos 
FROM centos
MAINTAINER judai<judai520@163.com>

ENV MYPATH /user/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 90

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash

#2.通过这个文件构建镜像
#命令 docker build -f dockerfile文件路径 -t 镜像名:[tag]

Successfully built d48774c7d6f2
Successfully tagged mycentos:0.1

#3.测试运行
```

![image-20210705173509295](README.assets/image-20210705173509295.png)

对比之前原生的centos：增加之后的镜像默认进入了工作目录，且可以使用vim

![image-20210705173804660](README.assets/image-20210705173804660.png)

![image-20210705174024343](README.assets/image-20210705174024343.png)

我们可以列出本地进行的变更历史

![image-20210705174242783](README.assets/image-20210705174242783.png)

我们平时拿到一个镜像，可以研究一下它是如何构建的了。



> CMD 和 ENTRYPOINT 区别

```shell
CMD			#指定这个容器启动的时候要运行的命令,只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动的时候要运行的命令，可以追加命令
```

测试cmd

```shell
#1.编写dockerfile文件
# root @ VM-0-17-centos in /home/dockerfile [20:00:39] 
$ cat dockerfile-cmd     
FROM centos
CMD ["ls","-a"]

#2.构建镜像
# root @ VM-0-17-centos in /home/dockerfile [19:59:55] C:1
$ docker build -f dockerfile-cmd -t cmdtest .

#3.启动容器
# root @ VM-0-17-centos in /home/dockerfile [20:00:14] 
$ docker run 608fe2c633ee               
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

#想追加一个命令 -ls -al
# root @ VM-0-17-centos in /home/dockerfile [20:04:46] 
$ docker run 608fe2c633ee -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:367: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.

#cmd的清理下 -l替换了["ls","-a"]命令，-l不是命令所以报错 ！
```

测试ENTRYPOINT

```shell
# root @ VM-0-17-centos in /home/dockerfile [20:14:03] 
$ cat dockerfile-cmd-entrypoint 
FROM centos
ENTRYPOINT ["ls","-a"]

$ docker run ca11f6c2d1fc              
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

#ENTRYPOINT追加命令时会相应拼接执行
# root @ VM-0-17-centos in /home/dockerfile [20:15:11] 
$ docker run ca11f6c2d1fc -l
total 56
drwxr-xr-x   1 root root 4096 Jul  5 12:17 .
drwxr-xr-x   1 root root 4096 Jul  5 12:17 ..
-rwxr-xr-x   1 root root    0 Jul  5 12:17 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  340 Jul  5 12:17 dev
drwxr-xr-x   1 root root 4096 Jul  5 12:17 etc
drwxr-xr-x   2 root root 4096 Nov  3  2020 home
lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root 4096 Dec  4  2020 lost+found
drwxr-xr-x   2 root root 4096 Nov  3  2020 media
drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
dr-xr-xr-x 230 root root    0 Jul  5 12:17 proc
dr-xr-x---   2 root root 4096 Dec  4  2020 root
drwxr-xr-x  11 root root 4096 Dec  4  2020 run
lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
dr-xr-xr-x  13 root root    0 Jun 20 09:29 sys
drwxrwxrwt   7 root root 4096 Dec  4  2020 tmp
drwxr-xr-x  12 root root 4096 Dec  4  2020 usr
drwxr-xr-x  20 root root 4096 Dec  4  2020 var
```

DockerFile中很多命令都十分相似，我们需要了解他们的区别，最好的学习方式就是测试并对比效果



## 实战：Tomcat镜像

1.准备镜像文件 tomcat 压缩包

![image-20210705211145787](README.assets/image-20210705211145787.png)

2.编写dockerfile文件，官方命名==Dockerfile==，build会自动寻找这个文件，就不需要 -f 进行指定了

```shell
# root @ VM-0-17-centos in /home/kuangshen/build/tomcat [22:25:59] 
$ cat Dockerfile
FROM centos
MAINTAINER judai<judai520@163.com>

COPY README.txt /user/local/README.txt

ADD jdk-8u141-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.50.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_141
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.50
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.50
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.50/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.50/bin/logs/catalina.out
```

3.构建镜像

```shell
#docker build -t diytomcat .
```

4.启动镜像

```shell
# root @ VM-0-17-centos in /home/kuangshen/build/tomcat [22:35:51] 
$ docker run -d -p 9090:8080 --name judaitomcat -v /home/kuangshen/build/tomcat/test:/usr/local/apache-tomcat-9.0.50/webapps/test -v /home/kuangshen/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.50/logs diytomcat
```

5.访问测试

6.发布项目（由于做了卷挂载，我们直接在本地编写项目就可以发布了）

> 在tomcat的test文件夹下建立文件夹WEB-INF ，在其中创建web.xml

```shell
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>FirstWebFontEnd</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

> 在test文件夹下创建index.jsp文件

```shell
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>首页</title>
<style>
    *{
    padding:0;
    margin:0;
    font-family:"微软雅黑";
}
.header{
    height:72px;
    background:#458fce ;
}
.header .logo{
    color:#fff ;
    line-height:70px;
    font-size:30px;
    margin-left:20px;
    display:inline-block;
    text-align:center;

}
a {
    color: #fff ;
    text-decoration: none ;
}
.header .login{
    float:right;
    color:#fff ;
    line-height:72px;
    margin-right:2px;
    display:inline-block;
}
.banner{
    height:380px;
    overflow:hidden;
    background: #ddd;
}
</style>
</head>
<body>
    <div class="header">
        <div class="logo">web实践</div>
        <div class ="login">
            <a href ="javascript:void(0)">登录</a>
            <span>|</span> 
            <a href ="javascript:void(0)">故事</a>
        </div>
    </div>
</body>
</html>
```

发现：项目部署成功，可以正常访问页面

![image-20210705230244852](README.assets/image-20210705230244852.png)



以后开发的步骤：需要掌握Dockerfile的编写，我们之后的一切都是使用docker镜像来进行



##  发布自己的镜像

> DockerHub

1.地址https://hub.docker.com/ 注册自己的账号

2.确定这个账号可以登录

3.在我们的服务器上提交自己的镜像

```shell
# root @ VM-0-17-centos in ~ [11:39:28] 
$ docker login --help         

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
```

4.登录完毕后就可以提交镜像了 docker push

```shell
# root @ VM-0-17-centos in ~ [11:49:33] C:1
$ docker login -u [username]
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

5.提交镜像

```shell
#如果报错可以更改镜像名称试试 尽量带上版本号
# root @ VM-0-17-centos in ~ [11:54:05] C:1
$ docker tag diytomcat judaiplus/tomcat:1.0  

# root @ VM-0-17-centos in ~ [11:54:42] C:1
$ docker push judaiplus/tomcat:1.0 
The push refers to repository [docker.io/judaiplus/tomcat]
3b4f055d7ccd: Pushed 
3a204f840eef: Pushed 
1b63d65066a5: Pushed 
89ac6c338bdd: Pushed 
2653d992f4ef: Mounted from library/centos 
1.0: digest: sha256:368e242a1d38cf3f3b1995d8ae8947148bd09099893511bd55e59bc1ca770532 size: 1373
```

提交的时候也是按照镜像层级来进行提交的



> 阿里云镜像服务

1.登录阿里云

2.找到容器镜像服务

3.创建命名空间

4.创建容器镜像

5.浏览阿里云

![image-20210706200640523](README.assets/image-20210706200640523.png)



**小结：**

<img src="README.assets/www.itdaan.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=.jpeg" alt="img" style="zoom:80%;" />



# Docker 网络

## 理解Docker0

清空所有环境

```shell
docker rmi -f $(docker images -q)
```

![image-20210706211355689](README.assets/image-20210706211355689.png)

三个网络

```shell
#问题： docker是如何处理容器网络访问的？
```

![image-20210706211531177](README.assets/image-20210706211531177.png)

```shell
# root @ VM-0-17-centos in ~ [21:24:15] 
$ docker run -d -P --name tomcat01 tomcat    

#查看容器的内部ip地址  ip addr ,  发现容器启动的时候会得到一个 eth0@if88 ip地址，这是由docker分配的
# root @ VM-0-17-centos in ~ [21:24:15] 
$ docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
87: eth0@if88: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever

#思考：linux能不能 ping 通容器内部？
# root @ VM-0-17-centos in ~ [21:37:18] 
$ ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.048 ms
64 bytes from 172.18.0.2: icmp_seq=3 ttl=64 time=0.052 ms

#linux可以 ping 通docker容器内部
```

> 原理： 

1.我们每启动一个docker容器，docker就会给docker容器分配一个id，我们只要安装了docker，就会有一个网卡docker0

桥接模式，使用的技术是veth-pair技术

再次测试 ip addr

![image-20210706220507265](README.assets/image-20210706220507265.png)

2.再启动一个容器测试，发现又多了一对网卡

![image-20210706220721742](README.assets/image-20210706220721742.png)

```shell
#我们发现这个容器带来的网卡都是一对一对的
#evth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连（可以通信）
#正因为有这个特性， veth-pair 充当一个桥梁，连接各种虚拟网络设备的
#Openstac，Docker容器之间的连接，OVS的连接，都是使用veth-pair技术
```

3.我们来测试下tomcat01和tomcat02是否可以ping通

```shell
# root @ VM-0-17-centos in ~ [22:25:19] 
$ docker exec -it tomcat01 ping 172.18.0.3
PING 172.18.0.3 (172.18.0.3) 56(84) bytes of data.
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=0.086 ms
64 bytes from 172.18.0.3: icmp_seq=2 ttl=64 time=0.060 ms
64 bytes from 172.18.0.3: icmp_seq=3 ttl=64 time=0.064 ms
64 bytes from 172.18.0.3: icmp_seq=4 ttl=64 time=0.059 ms

#结论：容器和容器之间是可以互相ping通的
```

绘制一个网络模型图

<img src="README.assets/image-20210706223103797.png" alt="image-20210706223103797" style="zoom:80%;" />

结论：tomcat01 和tomcat02 是共用的一个路由器，docker0。

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP

> 小结

docker使用的是Linux的桥接，宿主机中是一个docker容器的网桥 docker0

<img src="README.assets/image-20210706224041635.png" alt="image-20210706224041635" style="zoom:80%;" />

docker中所有网络接口都是虚拟的，虚拟的转发效率高！（内网传递文件）

只要容器删除，对应网桥一对就没了！



**--link**

> 思考一个场景，我们编写一个微服务，database url=ip: ，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以用名字来访问容器？

























