

# debian配置

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



