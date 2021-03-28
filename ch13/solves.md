> 写在前面的小建议
>
> 拿到服务器首先记得修改主机名与 IP！
>
> vim 使用不习惯不用强求，没人拦着你用 nano
>
> 如果你是 vim 用户，记得在新系统里安装好 vim 软件包：`vim` `vim-common` `vim-scrtpts` `ctags`，会加快写配置文件的时间，事半功倍
>
> 防火墙放在最后配置
>
> 可以把所有机器的 SSH 全部打开，在 Client 上 SSH 上去配置会快一点

# 登录

- Username：root
- Password：ChinaSkill20!
- Username：Chinaskills20
- Password：Chinaskills20!

除特别指定外，所有账号密码均为 `Chinaskills20!`

# 系统配置

- Region：China
- Locale：en_US.UTF-8
- KeyMap：English US

当任务为配置 SSL 或 TLS，如要求隐藏所有警告，请小心执行。

# 竞赛环境

物理机：

文件夹路径：

虚拟机:[datastore1] ( VMware ESXi )

ISO 镜像：[DATA]ISO/ ( VMware ESXi )

软件：D:\软件 ( Host )

# 项目任务描述

拓扑见样题

基本配置：

Device|Hostname|System|FQDN|IP Address|Service
-|-|-|-|-|-
Server01|Server01|预装 Linux|Server01.sdskills.com|172.16.100.201/25|DNS <br> Web <br> SSH <br> LDAP <br> DBMS 
Server02|Server02|待安装 Linux|Server02.sdskills.com|172.16.100.202/25|RAID5 <br> NFS <br> FTP <br> Mail <br> SSH <br> |
Server03|Server03|预装 Linux|Server03.skills.com|192.168.10.3/28|NTP <br> OpenVPN
Server04|Server04|待安装 Linux|Server04.skills.com|192.168.10.4/28|DNS <br> Web <br>
Rserver|Rserver|预装 Linux|Rserver.sdskills.com|172.16.100.254/25 <br> 192.168.10.2/28 <br> 10.10.10.254/24|firewall <br> DHCP <br> SSH <br> CA
Client|Client|Linux Desktop||10.10.100.x|none

网络：

Network|CIDR
-|-
office|10.10.100.0/24
service|172.16.100.128/25
internet|192.168.10.0/28


# Client Task

Client 已预装简易 Debian，要求如下：

- 要求能访问所有服务器，用于测试应用服务
- 为主机安装 GNOME 桌面环境
- 调整分辨率至 1280x768
- 测试 DHCP，IPv4 地址为自动获取
- 测试 DNS，安装 dnsutils 与 dig 命令行程序
- 测试 Web，安装 Firefox 浏览器，cUrl 命令行工具，在任何时候进行访问测试不允许弹出安全警告信息
- 测试 SSH，安装 SSH 命令行工具
- 测试 VPN 安装 VPN 客户端
- 测试 FTP，安装 FTP 客户端
- 测试文件共享，安装 Samba 命令行工具
- 测试 Mail，安装 Thunderbird，并能正常进行邮件收发
- 其他测试采用默认设定

## 题解

先配置主机名

```sh
$ hostnamectl set-hostname client
```

并且不要忘记修改 `hosts` 文件

```sh
$ vi /etc/hosts
```

默认的 vi 运行在 vi 兼容模式下，如果使用很别扭的话可安装 `vim` 软件包，或直接在 vi 中输入 `:set nocp` 关闭 vi 兼容模式。

将原来的主机名 `debian` 修改为新的主机名 `client`

此题目仅安装一堆软件包即可

但一部分题目需要后续做完其他服务器配置后返回来进行，所以这一阶段如果 Client 作为首题只能做一部分。

可以先安装所需软件包：

```sh
$ apt install gnome dnsutils firefox-esr curl openssh-client ftp thunderbird openvpn smbclient
```

安装完后 `reboot` 重启后进入桌面环境

```sh
$ reboot
```

桌面环境默认无法使用 `root` 账户登录，使用 `chinaskills20` 可登录进入桌面环境。

右键选择 `Display Settings`，打开显示器设置，将分辨率改为 1280x768

在配置后续机型时，会回来此机配置后续项目。

# Client Task

* 要求能访问所有服务器，用于测试应用服务
* 为主机安装 GNOME 桌面环境
* 调整显示分辨率为 1280x768
* 测试 DHCP，网卡 IPv4 地址为自动获取
* 测试 DNS，安装 dnsutils 与 dig 命令行工具
* 测试 Web，安装 Firefox 浏览器，curl 命令行测试工具，在任何时候进行访问测试时不允许弹出安全警告信息
* 测试 SSH，安装 SSH 命令行工具
* 测试 VPN，安装 OPENVPN 客户端工具软件
* 测试 FTP，安装 lftp 客户端工具
* 测试文件共享，安装 smbclient 客户端工具
* 测试 Mail，安装 Thunderbird，并能正常进行邮件收发
* 其他设定均采用默认设定

## 题解

安装桌面环境 

```sh
$ apt install gnome
```

重启即可



# Rserver Task

* Network
  * 请根据基本配置信息配置服务器主机名，网卡 IP 地址配置，域名等
  * 开启路由转发功能
* iptables
  * 默认阻挡所有流量
  * 添加必要的 NAT 规则，使 Office 和 Service 网络能够访问 Internet
  * 添加必要的规则以满足其他服务所需，禁止创建放行所有流量的规则
* DHCP
  * 为客户端分配 IP 范围为 10.10.100.1 - 10.10.100.50
  * 按照实际配置 DNS 与 GATEWAY
  * 配置 Client 固定获取 IP 地址为 10.10.100.51
* SSH
  * 安装 SSH 正常监听
  * 限制除 Client 以外的客户端登录，其他主机都应拒绝
  * 配置 Client 只能在 Chinaskills20 用户可以免密登录，端口为 2222，且具有 root 控制权限
* CA
  * CA 根证书路径 /CA/cacert.pem
  * 签发数字证书，签发者信息
    * 国家 = CN
    * 单位 = Inc
    * 组织机构 = www.skills.com
    * 公用名 = Skill Global Root CA

## 题解

### Network

首先查看网卡名称，使用 `ip link`

```sh
$ ip link
1: lo: (LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 
2: ens192: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000 
    link/ether 00:0c:29:a4:90:60 brd ff:ff:ff:ff:ff:ff 
2: ens224: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group DEFAULT qlen 1000 
    link/ether 00:0c:29:a4:90:Ba brd ff:ff:ff:ff:ff:ff 
4: ens256: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000 
    link/ether 00:0c:29:a4:90:94 brd ff:ff:ff:ff:ff:ff
```

共三块网卡，分别按照对应网络设定。

编辑 `/etc/network/interfaces`

```sh
$ vi /etc/network/interfaces
```

```sh
auto ens192
iface ens192 inet static
address 172.16.100.254/25

auto ens224
iface ens224 inet static
address 10.10.100.254/24

auto ens256
iface ens256 inet static
address 192.168.10.2/28
```

接下来启用网卡

```sh
$ ifup ens192 ens224 ens256
```

配置主机名

```sh
$ hostnamectl set-hostname Rserver
```

修改 `/etc/hosts`,将 FQDN 写入 `hosts` 文件

打开内核的 IPv4 转发，编辑 `/etc/sysctl.conf`，28 行处的 `net.ipv4.ip_forward=1` 去掉前面的注释，保存文件。

使配置文件生效

```sh
$ sysctl -p
```

这台电脑实际上要作为拓扑中的接入路由器来使用，因此后续的网关设备全部指定此计算机

FQDN 在 Linux 上是通过 DNS 解析实现的，验证配置使用 `hostname -f`

## iptables

这道题做出来的不多，我怀疑应该是没看懂题目。

这个实验环境是将 `192.168.10.0/28` 网段看作公有 Internet，将 `service` 以及 `office` 接入互联网，因此应在 Rserver 配置 NAT 使其通过 Rserver 的 IP 地址接入互联网，能理解到这个程度那么做起来也就不难了。

iptables 配置最烦人的点就是必须按顺序将防火墙规则写进 iptables 配置中，一旦写错就要清除配置重来，不像现在的 firewalld 是并行处理的，所以实际使用的时候先全部放行，等待全部配置完成之后再来思考防火墙规则可能更好。

比较方便的方法是写一个配置规则的 Shell 脚本，这样后续修改起来会相对方便。

```sh
#!/bin/bash

# 待补全

# 清除现有规则
iptables -F
iptables -X
iptables -Z

# 设定默认规则
iptables -P INPUT DROP
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT

# 设定规则
iptables -A INPUT -i lo -j ACCEPT # 放行所有本机流量
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT # 放行状态流量
iptables -A INPUT -i ens224 -p tcp -dport 2222 -j ACCEPT # 放行 SSH 流量

```

## DHCP

安装软件包 `isc-dhcp-server`

```sh
$ apt install isc-dhcp-server
```

没有做配置的情况下，安装时会提示服务无法启动，这是正常的。

要修改的文件有 `/etc/default/isc-dhcp-server` 与 `/etc/dhcp/dhcpd.conf`

首先修改 `/etc/default/isc-dhcp-server` 指定监听网卡

```sh
INTERFACESv4="ens224"
```

接下来编辑 `/etc/dhcp/dhcpd.conf`

在文件 50 行处有一个写的较完整的示例配置，可以直接复制使用

```sh
subnet 10.10.100.0 netmask 255.255.255.0 {
  range 10.10.100.1 10.10.100.50;
  option domain-name-servers 172.16.100.201;
  option domain-name "sdskills.com";
  option routers 10.10.100.254;
  option broadcast-address 10.10.100.255;
  default-lease-time 600;
  max-lease-time 7200;
}

host client {
  hardware ethernet 00:0c:29:4c:e3:a7;
  fixed-address 10.10.100.51;
}
```

其中，client 的 MAC 地址可通过在 Client 上使用 `ip link` 查看

启动服务，并确保开机启动

```sh
$ systemctl start isc-dhcp-server
$ systemctl enable isc-dhcp-server
```

## SSH

每次修改配置文件时，使用 `systemctl reload ssh` 使配置生效

编辑 `/etc/ssh/sshd_config`

修改 Port 为 2222

临时修改 PermitRootLogin 为 yes

拷贝 SSH key

```sh
# 在 Client 上
$ ssh-keygen
# 无脑回车
$ ssh-copy-id root@10.10.100.254 -p 2222
# 先在 Rserver 端允许 Root 账户登录，后再修改回来
```

编辑 `/etc/hosts.deny` 来限制登录

```sh
sshd:ALL except 10.10.100.51: deny
# 意为除去 10.10.100.51 主机以外的其他所有主机均禁止访问本机的 SSH 服务
```

确保服务开机启动

```sh
$ systemctl start ssh
$ systemctl enable ssh
```

## CA

在配置 CA 之前，确保当前时间正确，时区正确，最好先配置 NTP 服务器再回来配置 CA

修改 `/usr/lib/ssl/openssl.cnf`

第 28 行处，修改根目录为 `/CA`

创建子目录：

```sh
$ mkdir newcerts certs crl private
```

并创建两个文件：

```sh
$ touch ./index.txt
$ echo 1000 > serial
```

生成根证书私钥

```sh
$ openssl genrsa -aes256 -out private/cakey.pem 4096
# 输入密码
```

使用上一步生成的私钥签发证书：

```sh
$ openssl req -x509 -new -key /CA/private/cakey.pem -out /CA/cacert.pem
```

# Server01

## Network

配置 IP 与主机名跟上面方法相同，不多赘述

## DNS

Debian 将 Bind 的配置文件分成了很多份

对于 DNS 转发器，修改 `/etc/bind/named.conf.options`,文件中有一段被注释掉的 forwarders,取消注释后修改即可

将正向与反向解析配置放进 `/etc/bind/named.conf.local`，如下:

```sh
zone "sdskills.com {
  type master;
  file "/etc/bind/db.sdskills.com";
};

zone "100.16.172.in-addr.aroa" {
  type master;
  file "/etc/bind/db.172.16.100";
};
```

不要忘记末尾的分号！

接下来创建区域文件，从已有的区域文件复制修改即可

```sh
$ cp /etc/bind/db.local /etc/bind/db.sdskills.com
$ cp /etc/bind/db.127 /etc/bind/db.172.16.100
```

将 SOA 修改为自己的域名 ( 注意域名后的点！ )

添加自己需要的记录即可

使用 named-checkconf 和 named-checkzone 来检查语法格式是否正确

```console
root@Server01:~# named-checkconf
root@Server01:~# named-checkzone sdskills.com /etc/bind/db.sdskills.com
zone sdskills.com/IN:  loaded serial 2
OK
root@Server01:~#
```

当前的 Bind 版本默认只允许本地与已知的本地局域网访问服务器进行查询，因此需要修改配置文件，改回以前的放行模式：

修改 `/etc/bind/named.conf.options`，在后面增加三行：

```sh
options {
     ...
     allow-recursion { any; };
     allow-query { any; };
     allow-query-cache { any; };
     ...
 };
 ```

放行所有主机。

## apache

题目中需要使用 LDAP 做认证，配置完 LDAP 后再来



## SSH

几乎同理，参照 Rserver 配置

## LDAP

安装配置 LDAP 前，先创建好需求的 100 个用户

这个肯定不能手敲，所以上 Shell 脚本:

```sh
#!/bin/bash
# 在一个等宽的 100 个数中循环
for i in $(seq -w 100)
do
  # 新建用户
  useradd lduser$i
  # 给用户设置密码
  echo "lduser$i:Chinaskill20!" | chpasswd
done
```

红帽系可以使用 `passwd --stdin` ，但 Debian 的 `passwd` 不支持这个参数，所以需要使用另一个工具 `chpasswd`

脚本写完，保存为 useradd.sh，赋予执行权限直接运行，或者：

```sh
$ bash useradd.sh
```

