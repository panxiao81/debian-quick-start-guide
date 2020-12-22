在配置集群等服务时，需要保持多台主机的时间统一。

本文不涉及配置 NTP 服务器对外服务，而是如何配置 NTP 客户端。

对于简单的时间同步， `systemd` 自带的 NTP 客户端就足以满足要求，但如果有多一点的需求 ( 例如你需要连接一个硬件来提供时钟 ) ，则需要安装 `ntpd` 服务。

# systemd-timesyncd

当系统安装后，只要安装时网络畅通，则 Debian 会自动配置 `systemd-timesyncd` 并与 Debian 的官方时间服务器同步。

要增加 NTP 服务器，则需要修改 `/etc/systemd/timesyncd.conf`

```bash
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See timesyncd.conf(5) for details.

[Time]
#NTP=
#FallbackNTP=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
#RootDistanceMaxSec=5
#PollIntervalMinSec=32
#PollIntervalMaxSec=2048
```

添加服务器需要取消 `NTP=` 这一行的注释，并填入 NTP 服务器地址。

例如：
```bash
NTP=pool.ntp.org 0.asia.pool.ntp.org 1.asia.pool.ntp.org 2.asia.pool.ntp.org
```

多服务器间用空格分割。

保存更改后，重启 `systemd-timesyncd` 服务

```sh
systemctl restart systemd-timesyncd
```

之后验证配置

使用 `timedatectl show-timesync --all`

```console
root@debian:~# timedatectl show-timesync --all
LinkNTPServers=
SystemNTPServers=0.asia.pool.ntp.org 1.asia.pool.ntp.org
FallbackNTPServers=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
ServerName=0.asia.pool.ntp.org
ServerAddress=139.59.15.185
RootDistanceMaxUSec=5s
PollIntervalMinUSec=32s
PollIntervalMaxUSec=34min 8s
PollIntervalUSec=1min 4s
NTPMessage={ Leap=0, Version=4, Mode=4, Stratum=4, Precision=-24, RootDelay=86.166ms, RootDispersion=14.190ms, Reference=A29FC801, OriginateTimestamp=Tue 2020-12-22 19:34:01 CST, ReceiveTimestamp=Tue 2020-12-22 19:34:02 CST, TransmitTimestamp=Tue 2020-12-22 19:34:02 CST, DestinationTimestamp=Tue 2020-12-22 19:34:02 CST, Ignored=no PacketCount=1, Jitter=0 }
Frequency=7623778
```

NTP 服务器选择顺序如下：

- `systemd-networkd.service(8)` 中针对每个接口的配置，或者 DHCP 服务优先。
- `/etc/systemd/timesyncd.conf` 中定义的 NTP服务器会在运行时被添加到针对每个接口的服务器列表中，守护进程会轮流连接这些服务器直到某个有应答。
- 如果上两步没有取到 NTP 服务器， 那么就是用 `FallbackNTP=` 中定义的服务器。

要启用并运行：

```sh
timedatectl set-ntp true
```

同步需要一点时间，可能会卡住。

查看状态使用：

```sh
timedatectl timesync-status
```

# ntpd