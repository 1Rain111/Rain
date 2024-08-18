

# **debian12配置**

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

* 换源-

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
    deb https://mirrors.ustc.edu.cn/debian/ bookworm main non-free non-free-firmware contrib
    deb-src https://mirrors.ustc.edu.cn/debian/ bookworm main non-free non-free-firmware contrib
    deb https://mirrors.ustc.edu.cn/debian-security/ bookworm-security main
    deb-src https://mirrors.ustc.edu.cn/debian-security/ bookworm-security main
    deb https://mirrors.ustc.edu.cn/debian/ bookworm-updates main non-free non-free-firmware contrib
    deb-src https://mirrors.ustc.edu.cn/debian/ bookworm-updates main non-free non-free-firmware contrib
    deb https://mirrors.ustc.edu.cn/debian/ bookworm-backports main non-free non-free-firmware contrib
    deb-src https://mirrors.ustc.edu.cn/debian/ bookworm-backports main non-free non-free-firmware contrib
    ```````

* 更新

  `apt update`

* 测试网络

  `ping www.bing.com`

* 下载

  ` apt install bastet`




## 三、配置静态IP

### 1. 编辑网络接口配置文件

Debian 12 使用 `NetworkManager` 作为默认的网络管理工具，但你仍然可以通过编辑 `/etc/network/interfaces` 文件来配置静态 IP。

- 打开终端，并以管理员身份编辑网络配置文件：

```
bash
复制代码
sudo nano /etc/network/interfaces
```

- 添加或修改以下内容以配置静态 IP（假设你的网络接口是 `eth0`，你可以通过 `ip a` 或 `ifconfig` 命令来查看你的实际网络接口名称）：

```
bash
复制代码
auto eth0
iface eth0 inet static
    address 192.168.1.100  # 你的静态IP地址
    netmask 255.255.255.0  # 子网掩码
    gateway 192.168.1.1    # 网关
    dns-nameservers 8.8.8.8 8.8.4.4  # DNS服务器，可以使用Google的DNS
```

### 2. 重启网络服务

保存并退出编辑器后，重启网络服务以应用新的配置：

```
bash
复制代码
sudo systemctl restart networking
```

或者：

```
bash
复制代码
sudo /etc/init.d/networking restart
```

### 3. 验证配置

使用以下命令验证网络是否正常配置：

```
bash
复制代码
ip a
```

或

```
bash
复制代码
ifconfig
```

### 4. 使用 NetworkManager 配置（可选）

如果你使用的是 `NetworkManager`，也可以通过 `nmtui` 或 `nmcli` 命令配置静态 IP。

- 使用 `nmtui`：

```
bash
复制代码
sudo nmtui
```

选择 `Edit a connection`，然后选择你的网络接口并设置静态 IP。

- 使用 `nmcli`：

```
bash
复制代码
sudo nmcli con show
sudo nmcli con modify <连接名称> ipv4.addresses 192.168.1.100/24
sudo nmcli con modify <连接名称> ipv4.gateway 192.168.1.1
sudo nmcli con modify <连接名称> ipv4.dns "8.8.8.8 8.8.4.4"
sudo nmcli con modify <连接名称> ipv4.method manual
sudo nmcli con up <连接名称>
```



##  四、添加、修改或删除 DNS 服务器的地址

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



## 五、网卡配置

### nmcli

` apt install apt-file -y`

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





## 六、防火墙



在Debian 12上使用UFW（Uncomplicated Firewall）配置防火墙非常简单。UFW 是一个用于简化`iptables`的工具，适合大多数用户管理防火墙规则。

### 1. 安装 UFW

Debian 12 通常会预装 UFW，如果没有安装，可以使用以下命令安装：

```
bash
复制代码
sudo apt update
sudo apt install ufw
```

### 2. 启用 UFW

在配置规则之前，建议首先查看 UFW 的状态，确保它是禁用的：

```
bash
复制代码
sudo ufw status
```

如果 UFW 没有启用，可以通过以下命令启用：

```
bash
复制代码
sudo ufw enable
```

### 3. 配置 UFW 规则

#### 3.1 允许 SSH（端口 22）

为了避免在配置防火墙时将自己锁定在外部服务器之外，建议首先允许 SSH 连接：

```
bash
复制代码
sudo ufw allow ssh
```

#### 3.2 允许 HTTP（端口 80）和 HTTPS（端口 443）

如果你的服务器上运行了 Web 服务，可以通过以下命令允许 HTTP 和 HTTPS 连接：

```
bash
复制代码
sudo ufw allow http
sudo ufw allow https
```

或者，可以一次性允许这两个服务：

```
bash
复制代码
sudo ufw allow 'Nginx Full'
```

或者：

```
bash
复制代码
sudo ufw allow 'Apache Full'
```

#### 3.3 允许特定端口

如果你有其他特定的端口需要开放，例如：

- 允许 MySQL（端口 3306）：

  ```
  bash
  复制代码
  sudo ufw allow 3306
  ```

- 允许特定IP访问某个端口，例如允许192.168.1.100访问端口3306：

  ```
  bash
  复制代码
  sudo ufw allow from 192.168.1.100 to any port 3306
  ```

#### 3.4 拒绝所有其他入站流量

UFW 的默认策略是允许所有传出流量并拒绝所有传入流量。如果你希望在允许必要服务后拒绝其他所有入站流量，可以确保以下规则：

```
bash
复制代码
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### 4. 启用或禁用 UFW

UFW 规则配置好后，可以通过以下命令启用 UFW：

```
bash
复制代码
sudo ufw enable
```

如果需要临时禁用 UFW，可以使用：

```
bash
复制代码
sudo ufw disable
```

### 5. 查看 UFW 状态和规则

要查看当前的防火墙状态和规则列表，可以运行：

```
bash
复制代码
sudo ufw status verbose
```

### 6. 删除或修改规则

- 删除规则：

  ```
  bash
  复制代码
  sudo ufw delete allow 3306
  ```

- 修改规则则需要先删除旧规则，然后添加新规则。

### 7. 重新加载规则

如果你对配置进行了修改，UFW会自动应用新的规则。你也可以手动重新加载规则：

```
bash
复制代码
sudo ufw reload
```

### 8. 日志记录

如果需要启用日志记录来监控防火墙活动，可以使用以下命令：

```
bash
复制代码
sudo ufw logging on
```

可以通过 `/var/log/ufw.log` 查看日志。

### 结论

通过 UFW，您可以轻松地在 Debian 12 上配置并管理防火墙，确保服务器的安全性。

# **centOS8配置**



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

### 方式一：编辑文件

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

### 方式二：nmcli

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





### 配置静态ip

1. **编辑配置文件**：
   使用文本编辑器（如vim或nano）编辑对应的网络接口配置文件。对于大多数CentOS 8系统，网络接口配置文件位于`/etc/sysconfig/network-scripts/`目录下，文件名通常为`ifcfg-`加上网卡名称（如`ifcfg-ens33`）。

   ```
   sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33
   ```

2. **修改配置内容**：
   在配置文件中，你需要将`BOOTPROTO`设置为`static`，将`ONBOOT`设置为`yes`，并添加或修改`IPADDR`（IP地址）、`NETMASK`（子网掩码）、`GATEWAY`（默认网关）和`DNS1`（首选DNS服务器）等参数。例如：

   ```
   bash复制代码

   TYPE=Ethernet  
   BOOTPROTO=static  
   DEFROUTE=yes  
   PEERDNS=yes  
   PEERROUTES=yes  
   IPV4_FAILURE_FATAL=no  
   IPV6INIT=yes  
   IPV6_AUTOCONF=yes  
   IPV6_DEFROUTE=yes  
   IPV6_FAILURE_FATAL=no  
   IPV6_ADDR_GEN_MODE=stable-privacy  
   NAME=ens33  
   UUID=你的UUID值（保持原样或删除此行）  
   DEVICE=ens33  
   ONBOOT=yes  
   IPADDR=192.168.1.100  
   PREFIX=24  
   GATEWAY=192.168.1.1  
   DNS1=8.8.8.8
   ```

   注意：根据你的网络环境，`UUID`值可能不需要修改，但如果你不确定，可以删除此行或保持原样。`PREFIX`参数是子网掩码的一种表示方式，`24`代表子网掩码为`255.255.255.0`。

3. **重启网络服务**：
   ​

   ```
   bash复制代码

   sudo systemctl restart NetworkManager
   ```



## 四、链路聚合

- nmcli device


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



## 五、防火墙

`systemctl start firewalld.service `

`cd /usr/lib/firewalld/services`

`firewall-cmd  --list-all-zones `  查看所有策略

`firewall-cmd  --list-all`  查看当前策略

`firewall-cmd  --set-default-zone=public`  设置默认策略

`firewall-cmd --permanent --zone=public --add-service=http `  添加服务

`firewall-cmd reload ` 重载

 

在 CentOS 8 中，防火墙主要通过 `firewalld` 管理，而 `iptables` 在大多数情况下已经被弃用。`firewalld` 提供了一种更高级别的接口来管理防火墙规则，并且支持区域和服务的配置。

以下是如何在 CentOS 8 上配置防火墙的基本步骤：

### 1. 安装并启用 `firewalld`

`firewalld` 通常在 CentOS 8 中默认安装，如果没有，可以通过以下命令安装：

```bash
sudo dnf install firewalld
```

然后启动并启用 `firewalld` 服务：

```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

### 2. 检查防火墙状态

可以使用以下命令查看 `firewalld` 的状态：

```bash
sudo firewall-cmd --state
```

### 3. 配置基本的防火墙规则

#### 查看当前区域
`firewalld` 使用区域（zone）来管理网络接口。首先，可以查看当前活跃的区域：

```bash
sudo firewall-cmd --get-active-zones
```

#### 将接口分配到指定区域

你可以将网络接口分配到特定的区域，例如将 `eth0` 接口分配到 `public` 区域：

```bash
sudo firewall-cmd --zone=public --change-interface=eth0 --permanent
```

#### 允许特定服务或端口

- **允许服务**: `firewalld` 使用预定义的服务，可以通过服务名来允许特定流量。例如，允许 HTTP 和 HTTPS 流量：

  ```bash
  sudo firewall-cmd --zone=public --add-service=http --permanent
  sudo firewall-cmd --zone=public --add-service=https --permanent
  ```

- **允许端口**: 如果服务没有预定义的规则，也可以直接允许特定端口。例如，允许 8080 端口的 TCP 流量：

  ```bash
  sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
  ```

#### 删除服务或端口
- **删除服务**: 如果你想删除一个已允许的服务，例如 SSH 服务：

  ```bash
  sudo firewall-cmd --zone=public --remove-service=ssh --permanent
  ```

- **删除端口**: 如果你想删除一个已允许的端口，例如 8080 端口：

  ```bash
  sudo firewall-cmd --zone=public --remove-port=8080/tcp --permanent
  ```

### 4. 重新加载防火墙规则
在修改防火墙规则后，需要重新加载以使更改生效：

```bash
sudo firewall-cmd --reload
```

### 5. 查看当前的规则
要查看当前配置的防火墙规则，可以使用以下命令：

```bash
sudo firewall-cmd --list-all
```

### 6. 设置默认区域
默认区域是所有未明确分配到其他区域的接口使用的区域。可以通过以下命令设置默认区域：

```bash
sudo firewall-cmd --set-default-zone=public
```

### 7. 开启或关闭防火墙
如果你需要临时禁用或启用防火墙，可以使用以下命令：

- **关闭防火墙**:
  ```bash
  sudo systemctl stop firewalld
  sudo systemctl disable firewalld
  ```

- **开启防火墙**:
  ```bash
  sudo systemctl start firewalld
  sudo systemctl enable firewalld
  ```

### 8. 使用 `rich rules` 创建更复杂的规则
`firewalld` 支持使用 `rich rules` 创建更加复杂和细粒度的规则。例如，你可以允许特定 IP 地址访问某个端口：

```bash
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port protocol="tcp" port="22" accept' --permanent
```

配置完成后，你可以使用 `firewalld` 来根据需要进行进一步的配置和调整，确保你的系统安全并且开放必要的网络服务。



## 六、包管理 (`dnf` 是一个包管理器)

- 列出系统上已安装的所有软件包 使用`dnf`命令配合`list`和`installed`选项

`dnf list installed`

- 如果你只对特定类型的软件包感兴趣，或者想要通过搜索特定关键字来过滤结果，你可以使用`grep`命令来辅助完成。例如，要列出所有已安装的与`python`相关的软件包，你可以这样做：

`dnf list installed | grep python`

- `dnf provides`命令用于查找提供特定文件或功能的软件包。

例：

`dnf provides ping `   系统会搜索并列出所有包含`ping`命令的软件包

- 通过软件模块（modules）来管理不同版本的软件包

1. **查看可用的Python模块**：
   首先，你可以使用`dnf module list python`命令来查看所有可用的Python模块及其版本。这个命令会列出所有与Python相关的模块，包括它们的版本和状态（如默认、启用、禁用等）。

2. **启用Python 3.9模块**（如果可用）：
   如果`dnf module list python`显示有Python 3.9的模块可用，你可以使用`dnf module enable python39`命令来启用它。注意，这里的`python39`可能需要根据实际列出的模块名称进行调整。

3. **安装Python 3.9**：
   启用模块后，你可以使用`dnf install python3`（注意不是`python39`，因为通常安装命令是针对软件包名称的，而不是模块名称）来安装Python 3。但是，由于你已经启用了Python 3.9模块，`dnf`将会安装该模块提供的Python版本，即Python 3.9。

   如果你想要确保安装的是特定版本的Python（例如，当系统中有多个Python版本时），你可以使用`dnf install python3.9`（但这通常不是必要的，因为`python3`命令通常会指向启用的模块版本）。然而，请注意，并不是所有的发行版都会为Python的每个版本都提供`pythonX.Y`这样的软件包名称。

4. **验证安装**：
   安装完成后，你可以通过运行`python3 --version`来验证安装的Python版本。







# **磁盘管理**

`fdisk` 和 `gdisk` 是两个用于在 Linux 系统上管理磁盘分区的命令行工具。它们主要用于创建、删除和修改磁盘分区，但它们适用于不同类型的分区表。

### 1. `fdisk`

#### 1.1 概述

`fdisk` 是一个经典的分区管理工具，主要用于管理使用 **MBR（Master Boot Record）** 分区表的磁盘。MBR 是一种较旧的分区表格式，支持最多四个主分区或三个主分区加一个扩展分区，并且最大支持 2TB 的磁盘。

#### 1.2 常见操作

1. **列出分区**:
   ```bash
   sudo fdisk -l
   ```

2. **选择磁盘**:
   ```bash
   sudo fdisk /dev/sdX
   ```
   `X` 是磁盘设备的标识符，比如 `/dev/sda`。

3. **查看现有分区**:
   在 `fdisk` 交互模式下，按 `p` 键可以查看现有的分区表。

4. **创建新分区**:
   按 `n` 键，然后选择要创建的分区类型（主分区或逻辑分区），接着指定分区号和大小。

5. **删除分区**:
   按 `d` 键，然后选择要删除的分区号。

6. **写入分区表**:
   按 `w` 键将更改写入磁盘。

7. **退出**:
   按 `q` 键退出而不保存更改。

### 2. `gdisk`

#### 2.1 概述

`gdisk` 是一个类似于 `fdisk` 的工具，但它是专门为管理 **GPT（GUID Partition Table）** 分区表设计的。GPT 是一种较新的分区表格式，相较于 MBR，它支持更多的分区（通常支持 128 个分区）和更大的磁盘（超过 2TB），并且具有更高的可靠性和灵活性。

#### 2.2 常见操作

1. **列出 GPT 分区**:
   ```bash
   sudo gdisk -l /dev/sdX
   ```

2. **选择磁盘**:
   ```bash
   sudo gdisk /dev/sdX
   ```

3. **查看现有分区**:
   在 `gdisk` 交互模式下，按 `p` 键可以查看现有的分区表。

4. **创建新分区**:
   按 `n` 键，然后选择分区号、起始扇区和结束扇区。

5. **删除分区**:
   按 `d` 键，然后选择要删除的分区号。

6. **转换 MBR 到 GPT**:
   如果你有一个使用 MBR 的磁盘并希望转换为 GPT，可以使用 `gdisk` 的转换功能。注意，这个操作可能会导致数据丢失，因此建议先备份数据。

7. **写入分区表**:
   按 `w` 键将更改写入磁盘。

8. **退出**:
   按 `q` 键退出而不保存更改。

### 3. 主要区别

- **分区表类型**:
  - `fdisk` 主要用于 MBR 分区表。
  - `gdisk` 专为 GPT 分区表设计。

- **支持的磁盘大小**:
  - MBR（`fdisk`）支持的磁盘最大为 2TB。
  - GPT（`gdisk`）可以支持超过 2TB 的磁盘。

- **分区数量**:
  - MBR 支持最多 4 个主分区（或者 3 个主分区 + 1 个扩展分区）。
  - GPT 支持更多分区，通常可以达到 128 个。

### 4. 使用建议

- 对于较新的系统和大容量磁盘（超过 2TB），建议使用 GPT（即 `gdisk`）。
- 对于旧系统或者需要兼容性（如某些BIOS无法引导GPT磁盘），则可能需要使用 MBR（即 `fdisk`）。





### 5.格式化

1. **列出所有分区**：使用 `lsblk` 或 `fdisk -l` 命令来列出系统中的所有分区。

   ```
   bash
   复制代码
   lsblk
   ```

   或

   ```
   bash
   复制代码
   sudo fdisk -l
   ```

2. **选择要格式化的分区**：假设要格式化的分区是 `/dev/sdX1`，请将 `sdX1` 替换为你的实际分区名称。

3. **卸载分区（如果已经挂载）**：如果目标分区已经挂载，需要先卸载它。使用 `umount` 命令。

   ```
   bash
   复制代码
   sudo umount /dev/sdX1
   ```

4. **格式化分区**：可以使用 `mkfs` 命令选择你想要的文件系统类型进行格式化。常用的文件系统类型包括 `ext4`, `xfs`, `vfat` (FAT32) 等。

   - 格式化为 

     ```
     ext4
     ```

      文件系统：

     ```
     bash
     复制代码
     sudo mkfs.ext4 /dev/sdX1
     ```

   - 格式化为 

     ```
     xfs
     ```

      文件系统：

     ```
     bash
     复制代码
     sudo mkfs.xfs /dev/sdX1
     ```

   - 格式化为 

     ```
     vfat
     ```

      (FAT32) 文件系统：

     ```
     bash
     复制代码
     sudo mkfs.vfat /dev/sdX1
     ```

5. **验证格式化是否成功**：你可以再次使用 `lsblk` 或 `blkid` 来检查分区并验证格式化是否成功。

   ```
   bash
   复制代码
   lsblk
   ```

   或

   ```
   bash
   复制代码
   sudo blkid /dev/sdX1
   ```

6. **重新挂载分区（可选）**：如果需要使用该分区，可以重新挂载它。

   ```
   bash
   复制代码
   sudo mount /dev/sdX1 /mnt
   ```

完成这些步骤后，你的分区将被格式化为指定的文件系统。





6.

UUID=630b91a6-5d0b-4caa-b592-6a63d32bc0db /                       xfs     defaults        0 0









#  **swap交换分区**



![72360133909](C:\Users\rain2\AppData\Local\Temp\1723601339097.png)



![72360136318](C:\Users\rain2\AppData\Local\Temp\1723601363187.png)





新加硬盘作为虚拟分区使用    格式化启用 写入永久挂载

![72360212813](C:\Users\rain2\AppData\Local\Temp\1723602128139.png)

![72360229000](C:\Users\rain2\AppData\Local\Temp\1723602290000.png)

​	![72360225531](C:\Users\rain2\AppData\Local\Temp\1723602255314.png)



# dd

在Linux系统中，交换分区（swap partition）是一种特殊的分区，用于在物理内存（RAM）不足时，临时存储内存中的数据。这可以帮助系统在高负载下维持运行，而不至于因为内存不足而崩溃。

创建并启用Linux交换分区的步骤如下：

### 1. 检查现有的交换分区

首先，检查系统中是否已经有交换分区：

```
bash
复制代码
sudo swapon --show
```

如果没有输出，说明当前系统没有启用的交换分区。

### 2. 创建交换分区

#### a. 使用`fallocate`命令创建交换文件

你可以使用`fallocate`命令快速创建一个交换文件，例如创建一个2GB的交换文件：

```
bash
复制代码
sudo fallocate -l 2G /swapfile
```

#### b. 如果系统不支持`fallocate`，使用`dd`命令

如果`fallocate`命令不可用，可以使用`dd`命令创建交换文件：

```
bash
复制代码
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
```

此命令将创建一个2GB的交换文件。

### 3. 设置交换文件的权限

为确保交换文件的安全性，需要修改文件的权限：

```
bash
复制代码
sudo chmod 600 /swapfile
```

### 4. 将文件设为交换空间

将文件转换为交换空间：

```
bash
复制代码
sudo mkswap /swapfile
```

### 5. 启用交换空间

启用刚刚创建的交换空间：

```
bash
复制代码
sudo swapon /swapfile
```

### 6. 验证交换空间是否启用

再次检查交换空间是否已启用：

```
bash
复制代码
sudo swapon --show
```

### 7. 配置交换文件在系统启动时自动挂载

要使交换文件在系统启动时自动挂载，需要将其添加到`/etc/fstab`文件中：

```
bash
复制代码
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

这样，交换分区就会在每次系统启动时自动挂载并启用。

### 8. 调整交换使用策略（可选）

你可以通过调整`/proc/sys/vm/swappiness`来控制系统何时开始使用交换空间。默认值通常是60（范围是0到100），值越大，系统越倾向于使用交换空间。你可以通过以下命令查看当前值：

```
bash
复制代码
cat /proc/sys/vm/swappiness
```

如果你想调整这个值，例如将其设置为10：

```
bash
复制代码
sudo sysctl vm.swappiness=10
```

要使这个更改在系统重启后保持有效，可以将它添加到`/etc/sysctl.conf`文件中：

```
bash
复制代码
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

完成以上步骤后，你的Linux系统就会拥有一个有效的交换分区，能够在物理内存不足时提供辅助支持。







# 文件管理 

### 1. `more`

`more`命令用于逐页查看长文件的内容。它会显示文件的前几行，并在屏幕满时暂停，等待用户按下按键（通常是空格键）以显示下一页。

**使用示例：**

```
bash
复制代码
more filename.txt
```

**常用操作：**

- `空格键`: 显示下一屏内容。
- `Enter键`: 显示下一行内容。
- `q`: 退出查看。

### 2. `less`

`less`命令与`more`类似，但功能更强大。它允许在文件中前后滚动，并提供更多的搜索和导航功能。

**使用示例：**

```
bash
复制代码
less filename.txt
```

**常用操作：**

- `空格键`: 显示下一屏内容。
- `b`: 显示上一屏内容。
- `/pattern`: 向前搜索指定的模式。
- `?pattern`: 向后搜索指定的模式。
- `n`: 重复前一个搜索。
- `q`: 退出查看。

### 3. `tail`

`tail`命令用于查看文件的最后几行，通常用于实时监控日志文件的更新。

**使用示例：**

```
bash
复制代码
tail filename.txt
```

默认情况下，`tail`会显示文件的最后10行。

**常用选项：**

- `-n N`: 显示最后N行内容。例如，显示最后20行：

  ```
  bash
  复制代码
  tail -n 20 filename.txt
  ```

- `-f`: 实时跟踪文件的新增内容，通常用于监控日志文件：

  ```
  bash
  复制代码
  tail -f filename.txt
  ```

### 4. `head`

`head`命令用于查看文件的前几行内容，默认显示前10行。

**使用示例：**

```
bash
复制代码
head filename.txt
```

**常用选项：**

- `-n N`: 显示前N行内容。例如，显示前20行：

  ```
  bash
  复制代码
  head -n 20 filename.txt
  ```

### 5. `grep`

`grep`命令用于在文件中搜索匹配指定模式的行，并将这些行输出到终端。它是一个非常强大的文本搜索工具，支持正则表达式。

**使用示例：**

```
bash
复制代码
grep "pattern" filename.txt
```

**常用选项：**

- `-i`: 忽略大小写进行搜索。
- `-r` 或 `-R`: 递归地在目录中搜索。
- `-v`: 反向搜索，显示不匹配模式的行。
- `-n`: 显示匹配行的行号。
- `-o`: 只输出匹配到的部分。

**示例：**

- 在文件中搜索包含"error"的行，忽略大小写：

  ```
  bash
  复制代码
  grep -i "error" filename.txt
  ```

- 在当前目录及其子目录中递归搜索包含"TODO"的文件：

  ```
  bash
  复制代码
  grep -r "TODO" .
  ```







`find` 命令是 Linux 和 Unix 系统中非常强大的工具，用于在目录树中搜索文件和目录。它可以根据各种条件（如文件名、类型、修改时间、大小等）搜索文件，并且可以对搜索结果执行操作。以下是 `find` 命令的常用选项和示例：

### 基本语法

```
bash
复制代码
find [起始目录] [搜索条件] [执行的操作]
```

### 常用选项和搜索条件

1. **按名称搜索文件 (-name)**

   ```
   bash
   复制代码
   find /path/to/search -name "filename"
   ```

   - 搜索名称为 `filename` 的文件或目录。支持通配符（例如 `*.txt`）。

2. **按类型搜索 (-type)**

   ```
   bash
   复制代码
   find /path/to/search -type f  # 搜索普通文件
   find /path/to/search -type d  # 搜索目录
   ```

   - `f` 表示普通文件，`d` 表示目录。还可以搜索符号链接 (`-type l`)、字符设备 (`-type c`) 等。

3. **按大小搜索 (-size)**

   ```
   bash
   复制代码
   find /path/to/search -size +50M  # 搜索大于50MB的文件
   find /path/to/search -size -100k  # 搜索小于100KB的文件
   ```

   - `+` 表示大于，`-` 表示小于。单位可以是 `c` (字节)，`k` (KB)，`M` (MB)，`G` (GB) 等。

4. **按修改时间搜索 (-mtime)**

   ```
   bash
   复制代码
   find /path/to/search -mtime -7  # 搜索7天内修改过的文件
   find /path/to/search -mtime +30  # 搜索30天前修改过的文件
   ```

   - `-mtime` 按修改时间搜索，以天为单位。`-atime` 搜索按访问时间，`-ctime` 按更改时间（如权限或所有权变化）。

5. **按文件权限搜索 (-perm)**

   ```
   bash
   复制代码
   find /path/to/search -perm 644  # 搜索权限为644的文件
   find /path/to/search -perm /u+x  # 搜索所有者可执行的文件
   ```

   - 可以指定特定的权限模式，或使用符号模式（如 `/u+x`，表示所有者可执行）。

6. **按用户或组搜索 (-user, -group)**

   ```
   bash
   复制代码
   find /path/to/search -user username  # 搜索属于指定用户的文件
   find /path/to/search -group groupname  # 搜索属于指定组的文件
   ```

7. **按深度限制搜索 (-maxdepth, -mindepth)**

   ```
   bash
   复制代码
   find /path/to/search -maxdepth 2 -name "*.log"  # 只搜索到第2层目录
   find /path/to/search -mindepth 3 -name "*.log"  # 只搜索第3层及更深的目录
   ```

### 执行操作

你可以对搜索结果执行各种操作，如删除、复制、移动等。

1. **删除搜索到的文件 (-delete)**

   ```
   bash
   复制代码
   find /path/to/search -name "*.tmp" -delete  # 删除所有.tmp文件
   ```

2. **对搜索到的文件执行命令 (-exec)**

   ```
   bash
   复制代码
   find /path/to/search -name "*.sh" -exec chmod +x {} \;  # 将所有.sh文件设为可执行
   ```

   - `{}` 代表找到的每个文件，`\;` 表示命令结束。`-exec` 允许你对每个匹配的文件执行命令。

3. **打印搜索结果 (-print)**

   ```
   bash
   复制代码
   find /path/to/search -name "*.conf" -print
   ```

   - 默认情况下，`find` 会打印搜索结果，但你可以明确指定。

### 示例用法

1. **查找大于100MB的所有 .log 文件**

   ```
   bash
   复制代码
   find /var/log -name "*.log" -size +100M
   ```

2. **查找30天内修改的 .conf 文件并将其复制到备份目录**

   ```
   bash
   复制代码
   find /etc -name "*.conf" -mtime -30 -exec cp {} /backup/ \;
   ```

3. **查找并删除所有的临时文件**

   ```
   bash
   复制代码
   find /tmp -type f -name "*.tmp" -delete
   ```

4. **查找并列出所有普通用户可执行的文件**

   ```
   bash
   复制代码
   find /usr/local/bin -type f -perm /u+x -print
   ```

5. **查找所有属于特定用户的文件**

   ```
   bash
   复制代码
   find /home -user username
   ```

`find` 是非常强大的命令行工具，通过灵活组合各种选项，可以实现复杂的文件搜索和操作任务。











   



#  逻辑卷管理（LVM）

### LVM的主要组件：

1. **物理卷 (Physical Volume, PV)**：
   - 物理卷是实际的物理存储设备，比如硬盘或分区，你可以将这些设备加入到LVM系统中。
2. **卷组 (Volume Group, VG)**：
   - 卷组是通过组合一个或多个物理卷创建的存储池。可以将其看作一个大的存储空间，用来创建逻辑卷。
3. **逻辑卷 (Logical Volume, LV)**：
   - 逻辑卷相当于卷组中的“分区”。你可以根据需要创建、调整大小或删除这些逻辑卷。使用LV的好处在于它们的灵活性——你可以轻松调整大小，甚至可以跨多个物理磁盘扩展它们。
4. **逻辑区块 (Logical Extents, LE) 和物理区块 (Physical Extents, PE)**：
   - 逻辑卷被划分为逻辑区块（LE），而物理卷被划分为物理区块（PE）。这些区块是存储的基本单元，默认大小为4 MB，不过这个大小可以更改。

### 基本的LVM工作流程：

1. **创建物理卷 (PV)**：

   - 使用

     ```
     pvcreate
     ```

     命令将物理设备（如分区或整个硬盘）转换为物理卷。例如：

     ```
     pvcreate /dev/sda1
     ```

2. **创建卷组 (VG)**：

   - 使用

     ```
     vgcreate
     ```

     命令将一个或多个物理卷合并为卷组。例如：

     ```
     vgcreate my_vg /dev/sda1 /dev/sdb1
     ```

3. **创建逻辑卷 (LV)**：

   - 使用

     ```
     lvcreate
     ```

     命令从卷组中分配空间来创建逻辑卷。例如：

     ```
     lvcreate -L 10G -n my_lv my_vg
     ```

4. **格式化和挂载逻辑卷**：

   - 创建好逻辑卷后，需要格式化并挂载。例如：

     ```
     mkfs.ext4 /dev/my_vg/my_lv
     mount /dev/my_vg/my_lv /mnt
     ```

5. **调整逻辑卷大小**：

   - 使用

     ```
     lvextend
     ```

     或

     ```
     lvreduce
     ```

     命令来扩展或缩小逻辑卷。例如，扩展逻辑卷：

     ```bash
     lvextend -L +5G /dev/my_vg/my_lv
     resize2fs /dev/my_vg/my_lv
     ```

LVM提供了非常灵活的磁盘管理功能，特别是在需要动态调整磁盘空间或管理多个磁盘的情况下非常有用。



---



#  容器



## debian12-Docker安装

### 手动安装

更新 apt 包索引

`apt update` 

安装 apt 依赖包，用于通过 HTTPS 来获取仓库。

- apt install -y lsb-release gnupg2 apt-transport-https ca-certificates curl software-properties-common

添加docker源

- curl -sS <https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg

  `echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] <https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian> $(lsb_release -sc) stable" | tee /etc/apt/sources.list.d/docker.list`

`apt update`

安装docker

apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin



##  更换Docker镜像源

- nano /etc/docker/daemon.json

```
{
    "registry-mirrors": [
        "https://web3.185500.xyz:30008",
        "https://dqmpfwb6.mirror.aliyuncs.com"
    ]
}
```



- 重启Docker服务

  `systemctl restart docker`

- 查看是否成功配置：

  `docker info`






`ps aux`查看进程



![72369223710](C:\Users\rain2\AppData\Local\Temp\1723692237101.png)







![72369325445](C:\Users\rain2\AppData\Local\Temp\1723693254459.png)



![72369344717](C:\Users\rain2\AppData\Local\Temp\1723693447175.png)



`docker pull nginx`

`docker images`  查看已拉取镜像



启动一个nginx容器

`docker run -d -p 80:80 -v /data:/usr/share/nginx/html/ 900dca2a61f5 tail -f /dev/null`

进入容器

`docker exec -it ece43bc35f23 bash`

编辑html网页

`nano /data/index.html`

创建httpd容器

![72378768626](C:\Users\rain2\AppData\Local\Temp\1723787686267.png)

进入容器

![72378783250](C:\Users\rain2\AppData\Local\Temp\1723787832508.png)



`cd etc/apt/sources.list.d---->rm -rf  *  `

`cd /etc/apt--->echo"">sources.list `





![72379110285](C:\Users\rain2\AppData\Local\Temp\1723791102855.png)



!72379057925](C:\Users\rain2\AppData\Local\Temp\1723790579254.png

## centOS8-Docker安装







# Docker常用命令

```bash
查看镜像
docker images
查看正在运行的容器
docker ps
查看所有容器（包括运行和没有运行的）
docker ps -a
删除/启动/停止/强制结束容器
docker rm/start/stop/kill 容器id
删除镜像
docker rmi 镜像id

从镜像启动一个容器
docker run
	-d #在后台运行
	-p 主机端口:容器端口 #主机与容器端口之间的映射
	-it #进入容器，必须与bash连用
	-v 主机目录:容器目录 #主机目录与容器目录之间的映射
例子:
docker run -d -p 30001:80 镜像id
作用:从指定镜像运行一个容器，-d将它放在后台运行,-p将这个容器的80端口映射到主机的30001端口

docker run -it 镜像id bash
作用:从指定镜像运行一个容器,-it  bash直接进入容器

docker exec -it 镜像id bash
作用：exec参数只用于正在运行的容器,-it bash进入容器

docker run -d -p 30002:80 -v /root/:/usr/local/ 镜像id
作用从指定镜像运行一个容器，-d将它放在后台运行,-p将这个容器的80端口映射到主机的30002端口,并将现在这台主机的/root目录映射到创建的这个容器的/usr/local目录,在其中一方目录里面创建修改文件会自动同步到另一方目录
```









 



# Harbor

```
apt install unzip 
unzip harbor-v2.7.0.zip
cd  harbor-v2.7.0/harbor
cp harbor.yml.tmp1 harbor.yml
nano harbor.yml
#修改hostname为ip地址 注释https系列服务 端口证书 key  
cd 
chmod -R 777 *
cd  harbor-v2.7.0/harbor
./install.sh



网页创建项目

admin 是账号

重开一台虚拟机带有docker容器
信任连接
nano /etc/docker/daemon.json
{
    "insecure-registries":["192.168.7.66"]
}

#docker login
docker login 192.168.7.66 

打包镜像
docker commit -a xiayu 容器id 镜像名：版本号 
docker commit -a xiayu 88709c62e7e9 http-server:1.0 
标签镜像
docker tag http-server:1.0 192.168.7.66/xy_2024/xiayu:1.0
推送镜像
docker push 192.168.7.66/xy_2024/xiayu:1.0


新开一台docker机器
信任连接
nano /etc/docker/daemon.json
{
    "insecure-registries":["192.168.7.66"]
}
拉取镜像
docker pull 192.168.7.66/xy_2024/xiayu@sha256:41fc97b410595d558767790c4f689f2afe40ac532ad8544373ed1e3ee43f1ef9
```





![72399540991](MyLinux.assets/1723995409911.png)





















































