---
title: Home Assistant后续 - 接入米家蓝牙设备
date: 2022-01-01 21:00:00
tags: 智能家居
categories:
	- RaspBerryPi
---

# Home Assistant 更新

月底的时候发现HA有新版本，直接进入容器，执行：

```bash
pip install --upgrade pip 
pip install --upgrade homeassistant -i https://pypi.tuna.tsinghua.edu.cn/simple #使用清华源
```

有个鸡贼的事情，pip最新版本是`21.多`，但是Home Assistant支持的pip版本是小于21的，然而，如果我不执行第一条，会一直报错更新失败，而更新到21版本后，又看着HA把pip退回到20，但是这时候更新好了

整个就是一个$$ ? $$ 

# Home Assistant接入Mijia蓝牙设备

这里以我刚买的米家蓝牙电子温湿度计为例

首先需要通过米家APP将这个电子温湿度计接入小米账号

接入后在HA后台的`hass-xiaomi-miot`集成里更新设备，更新后会在设备里多一个`Xiaomi Digital Temperature and Humidity Monitor`

## 获取蓝牙设备一些信息

### 获取MAC地址

打开米家APP，进入蓝牙设备管理页面后点右上角三个小圆点进入设置，在`关于`里就有蓝牙设备的MAC地址，不需要像网上说的那么复杂

### 获取V5 ENCRYPTION KEY

小米的蓝牙设备数据是加密过的，我们需要给后面的`BLE Monitor`插件提供解密密钥来解密数据

github链接：https://github.com/PiotrMachowski/Xiaomi-cloud-tokens-extractor

这个python脚本用来获取账号绑定的米家设备的一些信息，其中就包括蓝牙设备的加密秘钥，方法在README里已经说很清楚了，执行以下命令：

```bash
wget https://github.com/PiotrMachowski/Xiaomi-cloud-tokens-extractor/releases/latest/download/token_extractor.zip
unzip token_extractor.zip
cd token_extractor
pip3 install -r requirements.txt # 安装依赖项
python3 token_extractor.py # 执行脚本
```

这个脚本会要求你输入小米账号及密码，输入后会让你选择所属区域，对于国人来说就是cn了，里面涉及的英文不难

我们重点要的是这个32位Encryption Key：

![image-20220101211055549](https://files.ozline.icu/images/blog/HA/HA-07.png)

## 安装BLE Monitor插件

我给树莓派已经安装了Samba，可以从Finder直接SMB访问树莓派

对于HA，统一在`/home-assistant/custom_components/`安装外部插件，上一篇里我安装过了`hass-xiaomi-miot`插件，这个文件夹就已经存在了，如果没有安装过插件，可以手动创建新文件夹

github链接：https://github.com/custom-components/ble_monitor

把整个source下载下来（zip downlolad），复制`custom_components`文件夹里面的`ble_monitor`文件夹，直接粘贴到刚刚`/home-assistant/custom_components/`里

进入HA后台，在`配置->设置->服务管理`里重新启动Home Assistant服务

重新启动后在`配置->设备和服务->集成->添加集成`里添加`Passive BLE monitor `，跳出的配置页不用管，直接点下一步就可以

## 配置BLE Monitor以解密设备数据

编辑`/home-assistant/comfiguration.yaml`，在行尾添加下面代码：

```yaml
# ble monitor
ble_monitor:
  hci_interface: 0
  batt_entities: True
  discovery: False
  devices:
    - mac: '前面获取的设备mac地址'
      encryption_key: '前面获取的32位密钥'

```

然后进入HA后台，在` 配置->设置->配置检查`里检查配置文件是否正确，如果无误的话就再重启HA设备

# 收工

由于蓝牙设备广播（以我这个设备）会有时间间隔（通常为1分钟\5分钟\10分钟），因此需要过一会儿才能在`Bluetooth Low Energy Monitor`这个集成里找到设备（找到这个设备可能不用这么久）并且获取当前数据（温度、湿度等等）

最终成果展示

![image-20220101212049895](https://files.ozline.icu/images/blog/HA/HA-08.png)