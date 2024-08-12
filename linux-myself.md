

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

![img](https://telegraph-image-12r.pages.dev/file/7777d3bcc6988e01b990d.png)



#### hostnamectl 查询和更改系统的主机名以及相关的配置信息

#### lsblk 用来列出所有可用的存储设备及其分区的命令

>- **NAME**: 设备或分区的名称，如 `/dev/sda` 或 `/dev/sda1`。
>- **MAJ:MIN**: 主设备号和次设备号，用于唯一标识设备。
>- **RM**: 表示设备是否是可移除的（removable）。`1` 表示可移除，`0` 表示不可移除。
>- **SIZE**: 设备或分区的大小。
>- **RO**: 表示设备或分区是只读的（read-only）还是可写的（read-write）。`ro` 表示只读，否则为空。
>- **TYPE**: 设备类型，如 `disk`（磁盘）、`part`（分区）等。
>- **MOUNTPOINT**: 如果分区被挂载，这里会显示挂载点。未挂载的分区这里为空。



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





`nmcli` 用于配置和管理网络连接的命令行工具

>### 一、基本功能与用途
>
>1. 显示网络设备
>
>   ：
>
>   - 使用`nmcli device show`或`nmcli d`命令查看系统中所有的网络设备（如网卡、Wi-Fi适配器等）的详细信息，包括设备名称、类型、状态和连接状态等。
>
>2. 管理网络连接
>
>   ：
>
>   - 使用`nmcli connection show`或`nmcli c`命令显示所有的网络连接及其详细信息，包括连接名称、设备、IP地址等。
>   - 使用`nmcli connection up`命令启用一个连接，使用`nmcli connection down`命令禁用一个连接。
>   - 使用`nmcli connection add`命令添加新的网络连接，使用`nmcli connection modify`命令修改已有的连接配置，如设置连接的名称、设备、IP地址、DNS服务器等参数。
>
>3. 配置网络连接
>
>   ：
>
>   - nmcli支持配置各种类型的网络连接，包括有线连接、无线连接、VPN连接等。
>   - 可以配置连接的IP地址、子网掩码、网关、DNS服务器等参数。
>
>4. Wi-Fi设置
>
>   ：
>
>   - 使用`nmcli device wifi`命令显示可用的Wi-Fi网络。
>   - 使用`nmcli device wifi connect`命令连接到指定的Wi-Fi网络，并指定网络的SSID和密码等参数。
>
>5. 管理DNS和IP地址
>
>   ：
>
>   - 使用`nmcli connection modify`命令设置连接的DNS服务器和IP地址。
>
>6. 网络状态查询
>
>   ：
>
>   - 查询当前系统中的网络连接状态和网络设备信息，包括已连接的网络接口、IP地址、DNS服务器、网络连接类型等详细信息。

