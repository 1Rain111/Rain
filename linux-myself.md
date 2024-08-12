

# debian12配置

## 一、启用用户远程ssh登录

1.进入配置

`nano /etc/ssh/sshd_config`



与远程登陆相关

> #PasswordAuthentication yes 这个选项允许远程登陆用密码来认证, 但加了#号, 不会起作用
> 将前面的#去掉, 变为 PasswordAuthentication yes 可以允许远程用密码登录认证
>
> #PermitRootLogin prohibit-password 设为 PermitRootLogin yes 允许root远程登陆



## 二、配置国内镜像源

* 备份

  `cp /etc/apt/sources.list /etc/apt/sources.list.bak`

* 换源

  `nano /etc/apt/sources.list`

  * 华为镜像站

    ~~~bash
    deb https://mirrors.huaweicloud.com/debian/ bookworm main non-free non-free-firmware contrib
    deb-src https://mirrors.huaweicloud.com/debian/ bookworm main non-free non-free-firmware contrib
    deb https://mirrors.huaweicloud.com/debian-security/ bookworm-security main
    deb-src https://mirrors.huaweicloud.com/debian-security/ bookworm-security main
    deb https://mirrors.huaweicloud.com/debian/ bookworm-updates main non-free non-free-firmware con>
    deb-src https://mirrors.huaweicloud.com/debian/ bookworm-updates main non-free non-free-firmware>
    deb https://mirrors.huaweicloud.com/debian/ bookworm-backports main non-free non-free-firmware c>
    deb-src https://mirrors.huaweicloud.com/debian/ bookworm-backports main non-free non-free-firmwa>

    ~~~

  * 中科大镜像站

    ```````bash
    # 默认注释了源码仓库，如有需要可自行取消注释
    deb http://mirrors.ustc.edu.cn/debian bookworm main contrib non-free non-free-firmware
    # deb-src http://mirrors.ustc.edu.cn/debian bookworm main contrib non-free non-free-firmware
    deb http://mirrors.ustc.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware
    # deb-src http://mirrors.ustc.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware

    # backports 软件源，请按需启用
    # deb http://mirrors.ustc.edu.cn/debian bookworm-backports main contrib non-free non-free-firmware
    # deb-src http://mirrors.ustc.edu.cn/debian bookworm-backports main contrib non-free non-free-firmware
    ```````

* 更新

  `apt update`

* 测试网络

  `ping www.bing.com`

* 下载

  ` apt install bastet`




## 三、添加、修改或删除 DNS 服务器的地址

`nano /etc/resolv.conf`

​	![img](https://telegraph-image-12r.pages.dev/file/7777d3bcc6988e01b990d.png)



#### hostnamectl 查询和更改系统的主机名以及相关的配置信息

#### lsblk 用来列出所有可用的存储设备及其分区的命令

>- **NAME**: 设备或分区的名称，如 `/dev/sda` 或 `/dev/sda1`。
>- **MAJ:MIN**: 主设备号和次设备号，用于唯一标识设备。
>- **RM**: 表示设备是否是可移除的（removable）。`1` 表示可移除，`0` 表示不可移除。
>- **SIZE**: 设备或分区的大小。
>- **RO**: 表示设备或分区是只读的（read-only）还是可写的（read-write）。`ro` 表示只读，否则为空。
>- **TYPE**: 设备类型，如 `disk`（磁盘）、`part`（分区）等。
>- **MOUNTPOINT**: 如果分区被挂载，这里会显示挂载点。未挂载的分区这里为空。



## 四、网卡配置

### nmcli

`apt install apt-file -y`

`apt-file update`

`apt-file search nmcli`

`apt install network-manager -y`

`nano NetworkManager.conf `

>[main]
>plugins=ifupdown,keyfile
>
>[ifupdown]
>managed=True

`systemctl restart NetworkManager`







# centOS8配置

## 一、启用用户远程ssh登录

1.进入配置

`nano /etc/ssh/sshd_config`



与远程登陆相关

> #PasswordAuthentication yes 这个选项允许远程登陆用密码来认证, 但加了#号, 不会起作用
> 将前面的#去掉, 变为 PasswordAuthentication yes 可以允许远程用密码登录认证
>
> #PermitRootLogin prohibit-password 设为 PermitRootLogin yes 允许root远程登陆





## 二、配置本地YUM源

进入 /etc/yum.repos.d/ 存放 YUM 仓库配置文件的目录

1.挂载CentOS安装光盘

```mount /dev/cdrom  /mnt/cd```

2.禁用网络yum源

```bash
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.bak
```

3.配置光盘yum源

```bash
vi /etc/yum.repos.d/CentOS-Media.repo
```

``````bash
[BaseOS]
name=CentOS Linux BaseOS
baseurl=/mnt/cd/BaseOS
gpgcheck=0
enabled=1

[AppStream]
name=CentOS Linux AppStream
baseurl=/mnt/cd/AppStream
gpgcheck=0
enabled=1
``````

4. yum makecache 用于构建或更新 YUM 仓库的缓存   

`dnf list installed`  DNF  包管理器 用于列出系统上所有已安装的软件包

`dnf provides filename `用于查找提供特定文件或功能的软件包

`dnf module list [模块名]`用于列出可用的软件模块及其流（stream）和配置文件（profiles）

`dnf module install <模块名> `  用于安装特定版本的软件模块及其依赖项



`systemctl disable firewalld.service `

`nano /etc/selinux/config`

![img](https://telegraph-image-12r.pages.dev/file/1acf8f314ff651108484c.png)

`reboot`



## 三、配置网卡方式

方式一：编辑文件

```nano /etc/sysconfig/network-scripts/ifcfg-ens160```

添加

```bash
IPADDR=192.168.7.66
NETMASK=255.255.255.0
DNS1=1.1.1.1
DNS2=8.8.8.8
GATEWAY=192.168.7.2
PREFIX=24
```

方式二：nmcli

1.创建

`nmcli c 查看连接`

`nmcli d 查看网卡设备`

``` bash
nmcli connection add type ethernet ifname ens160 ipv4.method manual con-name rainconnection ipv4.addresses "192.168.7.26/24" ipv4.gateway "192.168.7.2" ipv4.dns 8.8.8.8 autoconnect yes
```

`nmcli c modify ens160  autoconnect no`

2.启用

`nmcli connection up rainconnection  `



3.更改

`nmcli c modify rainconnection2 ipv4.addresses "192.168.7.31/24"`

虚拟网卡

`nmcli connection add con-name mybond0 type bond mode active-backup ipv4.addresses "192.168.7.30/24" ipv4.gateway "192.168.7.2" ipv4.dns 8.8.8.8`



## 四、链路聚合

- 主链路 虚拟网卡

  ```nmcli connection add con-name new-bond ifname bond0 type bond mode active-backup ipv4.addresses   "192.168.7.30/24" ipv4.gateway "192.168.7.2" ipv4.dns 8.8.8.8  ipv4.method manual```



- 网卡分链路  物理网卡

  `nmcli connection  add con-name mybond0-224 ifname ens224 type bond-slave master bond0  `

   `nmcli connection add con-name mybond0-160  ifname ens160 type bond-slave master bond0 `





`yum install httpd -y`   安装

`systemctl status httpd`

`systemctl start  httpd`   启动http

`ss -tunlp`   查看端口

`curl 127.0.0.1`   本地访问



开启防火墙

`systemctl start firewalld.service `



`cd /usr/lib/firewalld/services`

`firewall-cmd  --list-all-zones `  查看所有策略

`firewall-cmd  --list-all`  查看当前策略

`firewall-cmd  --set-default-zone=public`  设置默认策略

`firewall-cmd --permanent --zone=public --add-service=http `  添加服务

`firewall-cmd reload ` 重载

 

