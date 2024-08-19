# 如何使用frp实现服务器的内网穿透

这种实现方法本质上就是将内网服务器的地址和端口映射到外网,因此有很大的风险,需要自己做好服务器的防护.

在此之前，确保双方ssh正常使用，并且有一个带有公网ip的服务器。

## 1.下载rfp软件

[frp软件下载](https://github.com/fatedier/frp/releases/tag/v0.57.0)

按照平台类型下载。在下载下来的文件分两类，一个是以frps开头，一个以froc开头。复制两份，一份只留下一类。s=service，c=client.

## 2.获取一个公网ip

获取一个公网ip，打开端口6000，7000.这两个端口可以自定义，但是后面的配置端口也需要做对应的修改。

## 3.修改配置

服务器端就是指拥有公网ip的服务器，客户端就是指我们需要登上去的服务器。

唯一需要修改的地方是：

```shell

serverAddr = "1.94.132.156"

serverPort = 7000


[[proxies]]

name = "test-tcp"

type = "tcp"

localIP = "127.0.0.1"x`

localPort = 22

remotePort = 6000


```

将serverAddr替换为公网即可。

服务器端的配置只有一个：

```shell

bindPort = 7000

```

这里的7000和客户端的7000对应。

## 4.上传frp软件到服务器端和客户端

服务器端就是指拥有公网ip的服务器，客户端就是指我们需要登上去的服务器。

## 5.运行

找到上传的文件，按照相同的方法运行：

```shell

./frpc-cfrpc.toml

```

```shell

./frps-cfrps.toml

```

## 6.开机自启动

客户端一般都是linux，ssh连接windows的意义不大（可能），所以这里只讲linux。编辑/新增文件：/lib/systemd/system/frps.service，打开它并且添加下面的语句：

```shell

[Unit]

 Description=Frp Server Service

 After=network.target

 [Service]

 Type=simple

 User=username

 Restart=on-failure

 RestartSec=5s

 ExecStart=/home/username/frps/frps -c /home/username/frps/frps.ini

 LimitNOFILE=1048576

 [Install]

 WantedBy=multi-user.target


```

需要修改的是user为自己客户端的用户名，execstart为运行的绝对路径。最后以管理权限运行：

```shell

 systemctldaemon-reload

 systemctlenablefrps

 systemctlstartfrps

 systemctlstatusfrps

```
