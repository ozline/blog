---
title: Home Assistant实现Mijia与Homekit互联 --使用RaspberryPi
date: 2021-12-21 03:09:00
tags: 智能家居
categories:
	- RaspBerryPi
---
# Home Assistant 实现Mijia与Homekit互联 --使用RaspberryPi

![image-20211221025005286](https://files.ozline.icu/images/blog/HA/HA_05.png)

## 前言

作为一名学生，购买原生HomeKit家庭组件经费实在是不足，且自己原本就已经断断续续购买了一定的米家硬件

在iOS生态下，Apple原生的`家庭`APP的控制能力及操作便携性远大于`米家`APP，米家iOS版操作体验低于Android版

再加上部分Apple组件（如HomePod mini）无法通过米家操作

这时候，打通米家和HomeKit的想法就提上日程了

## 方案优点

- 将几乎所有米家设备加入`家庭`APP，使得一个APP可以控制几乎全部设备
- 在有家庭中枢的情况下，可以实现远程操作设备（如忘记关灯可以实现远程关灯）
- 拥有一个控制面板，可以直观显示设备当前情况

## 无外接显示器SSH登录RasPi

打开terminal，进入烧录好的SD卡，在boot文件夹中输入

```bash
ozline@OZLIINEX ~ : touch ssh  # 创建ssh空文件，打开RasPi的SSH功能
ozline@OZLIINEX ~ : touch wpa_supplicant.conf # 创建wifi连接文件
```

编辑wpa_supplicant.conf，把内容改为

```yaml
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
	ssid="你的WIFI名称"
	psk="你的WIFI密码"
	priority=1
}
```

后把SD卡插入RasPi，等绿灯不再亮而红灯常亮（RasPi 4B正常运行指示灯，其他可能有所不同），打开路由器后台，查看设备名为` raspberrypi`的ip，或者：

给RasPi通电前，在terminal输入

```bash
ozline@OZLIINEX ~ : arp-a # 查看lan设备的ip地址
```

通电并等待指示灯正常后，再输一遍，找出这两遍中多出来的ip，那么这个ip就是树莓派的局域网地址

通过terminal直接连接树莓派

```bash
 ozline@OZLIINEX ~ : ssh pi@RasPiHost # 通过ssh连接RasPi 这里RasPiHost替换为你的树莓派本地ip地址
```

## 安装Docker及Docker Compose

```bash
pi@raspberrypi:~ $  sudo curl -fsSL https://get.docker.com | sh # 使用官方脚本安装Docker
pi@raspberrypi:~ $  docker version # 查看docker是否安装成功
pi@raspberrypi:~ $  systemctl start docker # 启动docker
pi@raspberrypi:~ $  systemctl status docker # 查看docker状态
pi@raspberrypi:~ $  systemctl enable docker.service # 设置开机自启动
pi@raspberrypi:~ $  systemctl is-enabled docker.service # 查看是否设置成功
pi@raspberrypi:~ $  sudo usermod -aG docker $USER # 将当前用户加入docker组
pi@raspberrypi:~ $  exit # 退出RasPi
```

退出后重新登录，这样操作docker不再需要加sudo

接下来是安装Docker Compose，对于最新的Raspberry Pi OS，已经默认安装了python3和pip3，不需要重复安装，如果不是最新版的系统，可能需要手动安装python3和pip3

安装`libffi-dev`

```bash
pi@raspberrypi:~ $  sudo apt-get update # 更新
pi@raspberrypi:~ $  sudo apt-get upgrade # 更新
pi@raspberrypi:~ $  sudo apt-get install -y libffi-dev
```

使用中科大软件源安装`docker-compose`

```bash
pi@raspberrypi:~ $  sudo pip3 install docker-compose -i https://pypi.mirrors.ustc.edu.cn/simple/  --trusted-host  pypi.mirrors.ustc.edu.cn
pi@raspberrypi:~ $  docker-compose --version # 检查是否安装成功
```

## 通过Docker安装Home Assistant

先创建一些所需文件

```bash
pi@raspberrypi:~ $  mkdir home-assistant # 创建home-assistant文件夹
pi@raspberrypi:~ $  cd home-assistant
pi@raspberrypi:~/home-assistant $  touch docker-compose.yaml # 创建安装文件
```

编辑这个`docker-compose.yaml`

```yaml
version: '3'
services:
  homeassistant:
    container_name: homeassistant
    image: homeassistant/home-assistant
    volumes:
      - ./:/config
      - /etc/localtime:/etc/localtime:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
```

开始安装`Home Assistant`

```bash
pi@raspberrypi:~/home-assistant $  docker-compose up -d	# 安装Home Assistant
```

接下来打开浏览器，输入`http://RasPiHost:8123`进入Home Assistant后台界面，创建账号后就可以打开后台了，如图

![image-20211221015025616](https://files.ozline.icu/images/blog/HA/HA_01.png)

## 安装hass-xiaomi-miot 插件

```bash
pi@raspberrypi:~/home-assistant $  docker exec -it homeassistant bash # 进入容器
bash-5.1# wget -q -O - https://cdn.jsdelivr.net/gh/al-one/hass-xiaomi-miot/install.sh | HUB_DOMAIN=hub.fastgit.org bash - # 安装插件
bash-5.1# exit # 退出容器
pi@raspberrypi:~/home-assistant $ docker-compose restart # 重启容器
```



## 对Home Assistant后台进行配置

### 添加HomeKit集成

在后台，进入`配置->设备和服务->集成`，点击右下角的`添加集成`

![image-20211221015855227](https://files.ozline.icu/images/blog/HA/HA_02.png)

勾选全部域，提交，然后等待通知里提供二维码，在iPhone上的`家庭`APP里扫描即可添加桥接设备，如图

![IMG_2014](https://files.ozline.icu/images/blog/HA/HA_03.png)

### 添加Xiaomi Miot Auto集成

按照刚刚添加HomeKit的方法就可以添加了

![image-20211221021213360](https://files.ozline.icu/images/blog/HA/HA_04.png)

之后选择`账号集成模式`，然后输入自己的小米账号和密码，设备连接模式选择`云端模式`，之后一直点下一步就可以了，

## 成果

打开iPhone`家庭`APP，就会有如下显示：

![IMG_2015](https://files.ozline.icu/images/blog/HA/HA_06.png)

## 方案缺陷

- 尽管已经最大的简化，但是HA的上手难度依然存在一定的门槛
- 实际效果仍然与原生HomeKit有一定差距
- 在没有HomePod/iPad当家庭中枢的情况下，无法在非同网下操作硬件
- Xiaomi Miot Auto中无法集成部分硬件（如米家电热水壶Pro）到Home Assistant

## TODO

- 映射面板到公网/校园网
- 探索组件自动化