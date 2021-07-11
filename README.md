
<p align="center"><img src="./Images/logo.png" alt="FiberPunk's logo" /></p>

<h1 align="center">Beam-ESP32</h1>
<p align="center">
  <img src="https://img.shields.io/badge/arduino-opensource-brightgreen" alt="opensource"/>
   <img src="https://img.shields.io/badge/hardware-lowcost-blue" alt="lowcost"/>

</p>

## 1. 工程目录说明

- 依赖: Espressif System 1.0.6


## 2. Beam项目介绍

### 2.1 什么是Beam？

为什么要做Beam？ 我们都知道，目前市面上，90%以上的FDM设备都是没有联网能力的，也包括我自己所拥有的几台设备。当遇到我要使用多台打印机，打印
很多零部件的时候，我要不停的走来走去，插拔我的SD卡，这个很繁琐，并且容易出错。 我想到了用Raspberry来运行OctoPi来减轻我的负担，但是对于我这种有多台
打印机，同时又经常用树莓派来做些其他事情的人来说，这个太奢侈了。于是我想实现一个简单的模块，它包含如下几个特点:

- 够便宜，简单，配置傻瓜
- 能够像树莓派作为host一样，直接链接打印机的USB使用
- 能够远程传输，管理打印文件
- 可以支持监控
- 提供简单的RestfulAPI 来让其他的软件控制(比如postman这种调试工具)
- 主板拥有专门的引脚来接受其他的信号输入，来控制打印机

为了满足上述几个条件，我们使用了一个ESP32+SD+USB-Host的硬件组合，然后开发了Beam固件，固件主要是模拟Printrun的实现，来达到控制打印机的目的。
我们的Beam固件专注于打印机的监控，提供了几个restfulAPI外，没有运行web-ui，但是任何web-ui理论上都能控制他。Beam是一个非常轻量级的系统，在满足了上述的使用需求同时，我们
想到了一些实用细致的功能，他们如下:

- 在SD卡中配置cfg文件，来设置wifi的ssid和password，以及设备名等信息
- 提供了设备发现接口，其他的软件只需要向局域网广播一定的指令，就能发现设备，设备会返回自己的名称以及ip地址
- 提供restfulAPI的同时，提供socket长链接，在进行打印换层时，通过socket向外发送信号,实现打印延时摄影功能

需要注意的是，目前的Beam固件只能运行在BeamModule上。

### 2.2 连接其他

Beam的主要概念是作为一个单点存在，通过Beam，开发者们可以轻松开发自己的管理工具软件，他可以很容易的嵌入到你的OctoPi系统里面，让OctoPi或者你的PC来同时控制多太设备。
我们提供了免费的多设备使用PC工具BeamManager，该工具主要具备如下功能:

- 自动查找局域网内所有的Beam节点并连接，每个单独的节点有一个单独的控制面板
- 实现多台打印机上传文件，删除文件，打印，暂停等功能
- 管理USB摄像头(未来扩展位IP Camera)，同时监听打印机的换层信息，每次换层拍摄一张图片
- 上述图片一键生成视屏的工具，通过这个视屏，可以看到我们生成的效果



## 3. 使用说明

### 3.1 上位机的网络要求

请确保您的网络中有2.4G网络，ESP32目前只能连接2.4G的wifi。 此外，360或者一些杀毒软件，电脑设置了静态IP, VPN等，会对设备查找产生影响。 请确保您的电脑的IP网段跟路由器的网段保持一致。

打开上位机后，点击扫描设备，会自动扫描局域网中所有的设备，随后生成对应的操作窗口。


### 3.2 如何配置SD卡中的config.txt

如下是一个典型的配置:
```
ssid:you-wifi-ssid
pass_word:you-wifi-password
device_name:device-name
stop_x:200
stop_y:210
react_length:4.5
b_time_laspe:1

```
stop_x和stop_y表示摄像头拍摄时，喷头暂停的位置，react_length是摄像头要拍摄前，喷头移动到固定位置时，需要回抽的距离。
>注意1: 上述的冒号之后的设置参数，不能有空格

>注意2: 在最后一行之后，要有一个换行符

>注意3: config.txt文件一定更要有，否则Beam无法正常工作

### 3.3 灯效说明

- 电源正常: 上电后正常，小红灯常亮
- SD卡验证失败: 红灯闪烁
- Wifi链接失败: 红灯常亮
- 初始化正常: 绿灯(IDLE状态)
- 打印运行中: 蓝灯(PRINTING状态)

## 4.Beam-API

Beam对外提供了核心的API，来实现让更多的平台接入他进行控制。下面列举出Beam已经包含的API以及具体的使用。[API文档](./FP-BeamAPI.md)

## 5.注意事项

请勿在打印运行中拔掉SD卡。
