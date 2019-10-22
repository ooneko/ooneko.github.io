---
title:  浅谈 CentOS/RHEL 7 下的网络设备命名规则
date: 2017-12-03 10:45:10
---

使用过CentOS 7 或者RHEL 7的同学应该都见过类似 eno, em, ens 之类的网卡名称, 那么在什么情况下用什么命名方式有没有什么规则呢？
答案是，有的。

CentOS 7 中，网卡名称通常由两个组件来决定的: systemd , biosdevname。
默认情况下，由systemd来决定网卡名称，systemd会按照以下规则来命名网卡:
- 场景1：如果BIOS中存在板载网卡, 并能够提供索引，则使用eno命名，比如 eno1, 如果没有板载设备，就掉到场景2。
- 场景2：如果BIOS中存在PCI-E热插拔网卡，并提供了索引，则采用ens命名，例如ens1，如果没有则掉到场景3。
- 场景3：使用网卡的物理位置enp来命名，例如enp2s0。如果没有物理位置信息，则掉到场景5。
- 场景4：使用MAC地址命名网卡，例如enx78e7d1ea46da。不过默认不启用这个特性。
- 场景5：如果123场景都没有命中，则使用传统的命名方式eth来命名。

到这里，有人会问，那么em，pXpY之类的命名规则去哪了？

嗯，还有一种情况就是biosdevname。

如果系统中安装并启用了biosdevname , 并且系统的BIOS版本大于或等于 SMBIOS version 2.6。通常Dell的机器SMBIOS 都是大于2.6的，所以经常能在Dell的服务器上看到em1,p3p4这样的设备。systemd 和 biosdevname 这两套命名规则不能共存，在同一时间只可能用一种，所以不会出现同时使用两种命名规则的情况。

biosdevname 的命名规则:

| Device   | Name |
| --- | --- |
| 板载网卡 | `em[1234]` |
| PCI网卡  | `p<slot>p<ethernet port>` |
| 虚拟网卡 | `p<slot>p<ethernet port>_<virtual interface>` |

参考链接:
- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming