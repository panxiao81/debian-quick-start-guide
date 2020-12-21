在默认使用最小安装后的 Debian 将使用 `network` 网络管理器。

而安装 GUI 的情况下则会安装 `NetworkManager`

配置网络的方式与其他 Linux 系统没有太多区别。

即便是使用最小方式安装的 Debian，也可配置使用 `NetworkManager` 来管理网络。

# network 网络管理器

`network` 网络管理器是一直以来 Debian 默认使用的网络管理器。

与早期版本的 Red Hat 发行版类似，`network` 把网络配置写在一个文件内，并从文件内加载配置。

但与 Red Hat 系发行版不同的是，Red Hat 发行版将网卡配置文件保存在 `/etc/sysconfig/network-scripts/ifcfg-*name*` 文件中，而 Debian 将网络配置文件统一写在 `/etc/network/interfaces` 文件中。

默认情况下，在一个新安装的 Debian 中，网络是这样配置的，但 Debian 给出的最佳实现是将网络配置分网卡保存到 `/etc/network/interface.d/` 下。

在我的安装好的系统中，网卡安装后默认配置为 DHCP 方式获取 IP 地址，`/etc/network/interfaces` 内容如下：

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

当下，使用 systemd 的系统会使用 `biosdevname` 方案来对网卡命名，他的好处是网卡名不会因重启或一些原因而改变，但结果会导致网卡名不再像过去那种 `eth0` 一类的名字易于推断。

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

如配置静态地址，则写作如下：

```bash
auto ens33
iface ens33 inet static
  address 192.168.0.3/24  # IP/CIDR 形式的 IP 地址
  network 192.168.0.0  # 网络号
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

# NetworkManager