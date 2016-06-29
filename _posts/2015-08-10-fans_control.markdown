---
layout:     post
title:      "控制远程服务器上 Nvidia 显卡的风扇转速"
subtitle:   "Fan Control of Nvidia Video Card over SSH"
date:       2015-08-10 15:03:00
author:     "Coldmooon"
header-img: "img/home-bg.jpg"
---

## 一、前言
显卡散热对于 `Deep Learning` 来说非常重要。打造一个显卡的水冷系统成本较高，且需要一定的动手能力。如果对水冷系统不熟，安装时出了问题，导致水冷液漏了，那整个电脑就报废了。而显卡的风冷相对来说实施简单，成本较低。所以，对于大部分人来说，风冷是第一选择。此外，如果显卡安装在服务器上，我们可以通过 `ssh` 登录服务器，然后在服务器上做 DL 实验。据此，本文主要介绍如何控制远程服务器上的 `Nvidia` 显卡的风扇转速。

## 二、本地 GPU 风扇
在介绍远程服务器的 GPU 风扇控制之前，先来看看如何控制本地 GPU 的风扇转速:

首先，打开 `coolbits` 关键字。

```
$ cd /etc/X11
$ sudo nvidia-xconfig --cool-bits=28
```
关于 `coolbits` 字段值的含义，可以看[这里](http://us.download.nvidia.com/XFree86/Linux-x86_64/346.59/README/xconfigoptions.html)。此时不妨先查看一下显卡的统计信息:

```
$ nvidia-smi

+------------------------------------------------------+                       
| NVIDIA-SMI 352.30     Driver Version: 352.30         |                       
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX TIT...  Off  | 0000:01:00.0      On |                  N/A |
| 70%   30C    P8    19W / 250W |    251MiB / 12284MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|    0      1294    G   /usr/bin/X                                     143MiB |
|    0      2230    G   compiz                                          82MiB |
+-----------------------------------------------------------------------------+
```
`nvidia-smi` 命令显示了 GPU 的风扇转速，温度，功耗等信息。如果只需要 GPU 温度，可以使用下面的命令:

```
$ nvidia-smi --query-gpu=temperature.gpu --format=csv,noheader
```
当温度过高，可以通过下面的命令提高风扇转速:

```
查询与 GPU 风扇相关的关键字，例如当前风扇的转速等
$ nvidia-settings -q all | grep Fan

设定风扇转速
$ nvidia-settings -a "[gpu:0]/GPUFanControlState=1" -a "[fan:0]/GPUTargetFanSpeed=70"
```
上述命令可以把风扇转速设定在最大转速的 70%。

现在，我们可以读取 GPU 温度、当前风扇转速，还能调节风扇转速。据此，不难编写出来一个脚本，根据 GPU 温度，来自动调节风扇转速。

## 三、远程服务器的 GPU 风扇
要控制远程服务器的 GPU 风扇，首先要登录到服务器上:

```
$ ssh -XY -p 6666 username@server_ip_address 
```
其中，`-X` 表示启用 `X11` 转发，因为 `nvidia-settings` 是 `X` 客户端; `-Y` 表示启用可信 X11 转发，`-p` 指定 ssh 连接端口。如果不采用 `-XY` 选项，就会出现下列错误:

```
$ nvidia-settings -q all

ERROR: The control display is undefined; please run `nvidia-settings --help` for usage
       information.
```

然后，我们需要指定使用哪个 `X Display`。在 `ssh` 下，输入:

```
$ export DISPLAY=:0.0
```
如果不行就输入

```
$ export DISPLAY=IP:0.0
```
大部分情况下，IP 可以省略不写，直接写 `export DISPLAY=:0.0`。但有时候省略不写会出错。这种情况下可以尝试 `export DISPLAY=127.0.0.1:0.0`。
后面的 `0.0` 可以去 `/etc/ssh/sshd_config` 查看 `X11DisplayOffset` 这个选项。

上述命令可以将 `nvidia-settings` 的信息输出到远程主机上。如果不这样做，将得到下列错误:

```
$ nvidia-settings -q all

** (nvidia-settings:417): WARNING **: Couldn't connect to accessibility bus: Failed to connect to socket /tmp/dbus-adPHqaBCOf: Connection refused
```

现在，可以像本地操作那样，控制远程服务器上的 GPU 风扇了。PS: 别忘了打开服务器上的 `coolbits` 字段。


参考链接:
<https://devtalk.nvidia.com/default/topic/821563/linux/can-t-control-fan-speed-with-beta-driver-349-12/2/>
<http://discourse.ubuntu.com/t/lets-burn-it-nvidia-overcloking-now-available-on-linux/1610>
<http://us.download.nvidia.com/XFree86/Linux-x86_64/346.59/README/xconfigoptions.html>