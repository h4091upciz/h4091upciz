<h1 align="center">MicroPython WS2812 Led Clock</h1>

<p align="center"><img src="https://img.shields.io/badge/Licence-MIT-green.svg?style=for-the-badge" /></p>

### 项目介绍

基于`安信可ESP-C3-12F`模组，搭配`WS2812`矩阵灯珠，用于显示当前时间

![](./images/photo.png)

### 硬件介绍

硬件电路使用 [立创EDA](https://lceda.cn/) 设计，完全适合新手小白使用，PCB 板是在 [深圳嘉立创](https://www.jlc.com/) 下单打样的，本着薅羊毛的原则，板子尺寸限制在了`10cm * 10cm`以内，原理图文件可以在 [立创开源硬件平台](https://oshwhub.com/Walkline/kou-sou-dian-zhen-shi-zhong) 查看，这里不再赘述

> 主控模组选用了`安信可 ESP-C3-12F`，并非常用的`ESP32-WROOM-32D`

### 软件介绍

#### 工作流程

软件整体工作流程大致如下：

* 第一次启动或复位配网信息时，进入`配网模式`
* 如果输入信息有误，尝试联网`1分钟`后会自动重启并重复以上步骤
* 联网成功后保存`配网信息`（写入`sta_config.py`文件）并自动重启
* 重启后联网校时，并显示当前工作模式的对应内容
* 在此之后：
	* 每间隔`60秒`刷新一次时间
	* 每间隔`0.1秒`检测一次环境亮度，如果环境亮度发生变化则自动调整屏幕亮度
	* 每间隔`1小时`刷新一次台历
	* 每间隔`1小时`进行一次联网校时
* 如果重启后联网校时失败则会继续重启，保证时间可以正确显示

> 如果微信配网失败，可以尝试使用 app 进行配网，总有一个可以成功~

#### 工作模式

目前一共设计了三种工作模式，分别为：

* 时钟模式
* 台历模式1
* 台历模式2

> 如何阅读`时钟模式`和`台历模式1`显示的内容可以参考`Led 显示`部分的介绍

> `台历模式2`可以显示全部日期信息，要使用此模式功能需要更换前面板

#### 按键功能

板子上集成了`4个`功能按键和`1个`复位按键，目前按键功能如下：

* `SW1`：
	* 短按：
		* 在`时钟模式`和`台历模式1`显示时临时切换到另一种工作模式，时长`20秒`
		* 菜单模式下为退出菜单模式，退出时不保存修改内容
	* 长按：清除配网信息，并切换到另一种配网方式
* `SW2`：
	* 短按：
		* 屏幕显示开关
		* 菜单模式下循环切换菜单项
	* 长按：进入/退出工作模式选择菜单，退出时保存修改内容
* `SW3`：弃用 ~~循环切换屏幕显示亮度，但还会根据环境亮度自动调节，仅做调试用~~
* `SW4`：弃用 ~~屏幕显示开关~~
* `EN`：手动重启设备

### 设备配网

设备需要连接无线网络并且具备外网访问权限才能正常使用，目前支持使用`蓝牙`和`SmartConfig`方式进行配网，默认为`蓝牙`方式

长按`SW1`键可以在`蓝牙`和`SmartConfig`之间进行切换，以不同背光颜色区分：

* 蓝色：`蓝牙`方式
* 绿色：`SmartConfig`方式

#### 蓝牙配网

设备启动时默认进入`蓝牙`模式

使用微信扫描下方的`小程序码`，或者微信搜索`BLE Config`小程序即可进行配网操作

![](https://gitee.com/walkline/micropython_ble_config/raw/master/images/ble_config.jpg)

#### SmartConfig 配网

`SmartConfig`配网可以选择以下几种方法：

* 关注 安信可科技 微信公众号，点击 应用开发→微信配网
* 关注 乐鑫信息科技 微信公众号，点击 商铺→Airkiss 设备
* 安装 [EspTouch for Android](https://github.com/EspressifApp/EsptouchForAndroid/releases)，点击 EspTouch

输入 Wi-Fi密码 后点击 连接按钮，等待即可

> 如果长时间获取不到信息，则需要手动重启设备并重试

> 前往观看 [配网演示视频](https://www.bilibili.com/video/BV1N34y1971S/
)

> `ESP-C3-12F`模组只支持连接`2.4G`无线网络，所以配网时要求路由器或热点必须为`2.4G`模式，`2.4G`与`5G`混合模式也不能正常工作

### Led 显示

#### 时钟模式

![13点35分](./images/led_clock.png)

* 最大的区域显示当前时间的`小时`数字
* 最右侧区域显示当前时间分钟数的`十位数`
* 最下边区域显示当前时间分钟数的`个位数`

因此，上图显示的时间为`13点35分`

#### 台历模式1

![5月20日](./images/led_calendar1.png)

* 最大的区域显示当前`日期`
* 最右侧两列区域显示当前`月份`
* 最上边区域显示当前`星期`

因此，上图显示的日期为`5月20日星期四`（非要问是哪一年？那就是2004年）

#### 台历模式2

`台历模式2`显示`月份`、`星期`方式与`台历模式1`相同，不同之处是需要更换前面板以显示当前`日期`

![](./images/led_calendar2.png)

> 感谢扇贝物联的有心大叔提供的设计方案

### 如何烧录固件

#### 1. 在线烧录固件

推荐使用[在线烧录工具](https://walkline.wang/esp-web-installer/mini-calendar/)进行固件的烧录

在线烧录工具提供 3 种不同的固件

* 正式版：可以确保稳定运行，但是无法自动更新代码
* 测试版：每次设备重启后都会联网进行在线更新，但是无法确保稳定运行
* 开发版：不包含程序文件，仅用于项目代码调试

#### 2. 本地烧录固件

> 推荐使用 [AMPY Batch Tool](https://gitee.com/walkline/a-batch-tool)，以下说明使用`ab 工具`进行讲解

设备连接到电脑，打开`终端`，输入命令，根据提示信息进行操作即可：

```bash
$ cd path/to/repo
$ ab --flash # 选择 esp32c3 开头的固件文件
```

如果要设置屏幕亮度或其它设置，可以进行如下操作：

```bash
# 修改 config.py 文件相关内容并保存文件
$ ab abc_config
```

根据提示信息选择端口号就可以上传修改后的`config.py`文件，之后重启设备即可


前往观看 [设备使用演示](https://www.bilibili.com/video/BV1jQ4y1v7Wq/)

### 硬件测试

运行项目中的`hardware_test.py`即可同时测试`Led`、`按键`和`光敏电阻`

```bash
$ cd path/to/repo
$ ab --repl

# 使用快捷键 Ctrl+R 选择 hardware_test.py 并回车
```

### 计划增加的功能

* [x] 目前配网时没有任何提示信息，准备增加一个提示画面（或动画）
* [ ] ~~计划中还有一个模式切换功能，无非就是手电或者各种乱闪~~
* [x] 还可以增加一个整点提醒功能
* [x] 增加蓝牙配网功能
* [x] 增加台历功能

### 存在的问题

* 为了省事没有给每一颗 LED 搭配电容，当全部 LED 以白色最大亮度（255）点亮时，会因为供电不足导致无法继续工作，所以解决方案是降低最大亮度的上限值，目前仅使用`10%`亮度，不使用遮光板的前提下亮度已经足够

* `SmartConfig`偶尔出现卡死的情况，不使用串口调试无法发现，不过无线连接本来就是个概率事件，也能说得过去。。。。吧

* `SmartConfig`在我的`WIFI6`路由器下使用会出现无法发送配网完成通知的情况，无解

### 相关项目

* [MicroPython WS2812 Research](https://gitee.com/walkline/micropython-ws2812-research)
* [MicroPython SmartConfig CModule
](https://gitee.com/walkline/micropython-smartconfig-cmodule)
* [MicroPython BLE 配网](https://gitee.com/walkline/micropython_ble_config)

### 合作交流

* 联系邮箱：<walkline@163.com>
* QQ 交流群：
	* 走线物联：[163271910](https://jq.qq.com/?_wv=1027&k=xtPoHgwL)
	* 扇贝物联：[31324057](https://jq.qq.com/?_wv=1027&k=yp4FrpWh)

<p align="center"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_walkline.png" width="300px" alt="走线物联"><img src="https://gitee.com/walkline/WeatherStation/raw/docs/images/qrcode_bigiot.png" width="300px" alt="扇贝物联"></p>
