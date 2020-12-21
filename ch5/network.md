使用最小安装 ( 未安装 GUI 情况下 ) 的 Debian 将使用 `network` 网络管理器。

而安装 GUI 的情况下则会安装 `NetworkManager`

配置网络的方式与其他 Linux 系统并没有太多区别。

即便是使用最小方式安装的 Debian，也可配置使用 `NetworkManager` 来管理网络。

# network 网络管理器

`network` 网络管理器是一直以来 Debian 默认使用的网络管理器。

与早期版本的 Red Hat 发行版类似，`network` 把网络配置写在一个文件内，并从文件内加载配置。

但与 Red Hat 系发行版不同的是，Red Hat 发行版将网卡配置文件保存在 `/etc/sysconfig/network-scripts/ifcfg-*name*` 文件中，而 Debian 将网络配置文件统一写在 `/etc/network/interfaces` 文件中。

默认情况下，在一个新安装的 Debian 中，网络是这样配置的，但 Debian 给出的最佳实现是将网络配置分网卡保存到 `/etc/network/interface.d/` 下。

在我的安装好的系统中，网卡默认配置为 DHCP 方式获取 IP 地址，`/etc/network/interfaces` 内容如下：

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens33
iface ens33 inet dhcp
```

当下，使用 systemd 的系统默认会使用 `biosdevname` 方案来对网卡命名，他的好处是网卡名不会因重启或更换物理硬件等其他情况而改变，但结果会导致网卡名不再像过去那种 `eth0` 一类的名字易于推断。

因此配置网卡前，可能我们需要使用 `ip addr` 命令 ( [来自 iproute2 工具包](ch10/iproute2.md) ), 在我的例子中，运行如下：

```console
root@debian:~# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:f7:01:7f brd ff:ff:ff:ff:ff:ff
    inet 192.168.28.128/24 brd 192.168.28.255 scope global dynamic ens33
       valid_lft 1498sec preferred_lft 1498sec
    inet6 fe80::20c:29ff:fef7:17f/64 scope link
       valid_lft forever preferred_lft forever
```

如此可见，本例的网卡名为 `ens33`。

在主配置文件中，或在 `/etc/network/interface.d/` 中，创建配置文件 ( 文件名不限 )，对网卡进行配置。

如需要 DHCP，写作如下：

```bash
auto ens33
iface ens33 inet dhcp
```

如配置静态 IPv4 地址，则写作如下：

```bash
auto ens33
iface ens33 inet static
  address 192.168.0.3/24  # IP/CIDR 形式的 IP 地址
  gateway 192.168.0.1  # 网关地址
```

DNS 信息填入 `/etc/resolv.conf`

```bash
nameserver 114.114.114.114
```

重启网卡生效

```sh
# 重启网卡
ifdown ens33
ifup ens33
```
> 文档： [man interface(5)](http://man.he.net/?topic=interfaces&section=all)

# NetworkManager

NetworkManager 是一个由 GNOME 项目开发的，使得 Linux 的网络配置尽可能简单，开箱即用而开发的软件包。

目前 Red Hat 发行版已经自带并默认使用 NetworkManager 来管理网络。

如何查看当前的 Debian 安装是否使用 NetworkManager？

- 使用 `nmcli` 或 `nmtui` 命令，如果运行了 NetworkManager 的管理工具，则当前系统可能在使用 NetworkManager 管理网络
- 使用 `systemctl status network-manager` 查看服务状态，如果具有该服务，并且服务正在运行，则说明当前系统正在使用 NetworkManager 管理网络

NetworkManager 与传统的 network 网络管理器不可同时使用，如果使用两者同时管理同一网卡会造成冲突。

最小化安装的 Debian 不会安装 NetworkManager，若要使用 NetworkManager，需要安装软件包。

```sh
apt install networkmanager
```

屏幕输出

```console

```

安装 NetworkManager 后，原有在 `/etc/network/interfaces` 中的网络配置会被注释掉，并被 NetworkManager 的配置取代。

NetworkManager 会带来两个新的网络配置工具，`nmcli` 与 `nmtui` 。

但 NetworkManager 会覆盖已有的所有网络配置，包括 DNS 在内，因此所有配置要使用 NetworkManager 配套工具进行。

`nmtui` 是一个图形化方式配置网络的工具，易于上手。

在终端中输入

```sh
nmtui
```

即可打开 `nmtui` 工具。