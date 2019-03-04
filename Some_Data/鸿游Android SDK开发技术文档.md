
版本 | 修改人 | 备注
|---|---|---
0.9 | 雍高超 | 初稿 



## 前言
鸿游sdk及其衍生工具用于网游联运业务。一方面，游戏提供方（CP）接入sdk，并委托本公司联运；另一方面，游戏发行渠道挑选在本公司联运的游戏，并纳入其渠道下发布。由于大型游戏发行渠道一般都有自行研发的sdk，CP需要接入sdk后出包方能在该渠道发布，本业务在技术上需要解耦CP方与渠道方，保证CP方接入一次本公司sdk，在任何本公司的合作渠道都可以发行，这就是鸿游sdk的研发目的与业务背景。

CP接入sdk后所出的游戏包称为母包。母包可以在本公司自有渠道——鸿游发布。母包可以通过打包工具分出渠道包，该包可以在相应渠道发布。

本文档是鸿游sdk的技术文档，对应1.1.0以上版本sdk。若想了解打包工具，请参考```AutoPac文档```。

## 功能
本sdk实现了自有渠道登录、支付（自有渠道）、角色信息上报等功能，接入渠道后，可实现渠道分发。

## 架构与原理

### 整体设计
由于本sdk由旧版鸿延sdk改造而成，因此核心代码遵循鸿延sdk的设计，分为两部分：plugin代码和shell代码。

plugin代码主要包含与服务器交互的重要接口，对应工程`onetgame_SDK_s_plugin_base`。shell代码是sdk核心代码及其封装，封装了登录、支付等功能，对应工程`renetgame_SDK_1_s_shell_with_login`。

### 接口设计

通过大量收集整理渠道sdk的接口，设计出如图所示的鸿游sdk接口。
![鸿游sdk原理](http://i4.piimg.com/1949/7cdbb3a71ff0a63c.png)

鸿游sdk取各家渠道sdk接口的并集，适当精简后得出如图所示的接口，包括用户相关接口、支付相关接口以及Activity生命周期回调。打包阶段，渠道sdk所需的代码、资源文件、AndroidManifest声明等会通过打包工具打入母包，制作出渠道包。当渠道包开始运行，由于CP会首先调用初始化接口，鸿游sdk会在初始化阶段得知该渠道sdk所有接口的封装类名。封装类在打包阶段打入，用于封装渠道sdk的接口实现，并被鸿游sdk在相应时机反射调用。例如，当CP方调用鸿游sdk的支付，渠道1的封装类的支付方法就会被反射调用。

## 工程项目简介

sdk工程大致分为登录项目、支付与渠道分发项目（分为plugin、shell两部分），以及各渠道接入工程（命名形如Dependent_xx的工程）。

工程名 | 描述
---|---
Demo | demo工程
LoginDependProject | 登录项目依赖的资源
LoginSDK | 登录项目主代码
LoginSDKDemo | 登录项目测试demo
onetgame_SDK_s_plugin_base | plugin代码
renetgame_SDK_1_s_shell_with_login | shell代码
Dependent_xx | 渠道xx的sdk集成工程
SdkBase | 是所有Dependent_xx的依赖工程
SdkWrapper | 自有渠道的代码实现

### 项目依赖
鸿游sdk接入其他渠道时，会为这个渠道手动新建一个Dependent_XX工程。工程间遵循如下依赖关系：
```
demo ===> Dependent_xx（可以是任意渠道的接入工程） ===> SdkBase
```

每一个Dependent_xx工程都是一个渠道sdk的集成。特别地，Dependent_HY是鸿游自有渠道的实现工程。Demo工程需要依赖且只依赖一个Dependent_xx工程，而Dependent_xx依赖SdkBase工程，这样运行出来的app将会是某一个渠道接入后的demo。

> 渠道实现的demo有什么作用？请参考AutoPac文档。

其他工程，则是通过将整个工程打包成jar的形式，被上述工程依赖：

```
LoginSDK ---lsdk.jar---> renetgame_SDK_1_s_shell_with_login
onetgame_SDK_s_plugin_base ---nplugin.apk---> demo
renetgame_SDK_1_s_shell_with_login ---npaysdk_xxx.jar---> SdkBase
SdkWrapper ---npayext_xxx.jar---> Dependent_HY
```

LoginSDK代码修改后，需要运行该工程下的build_msg.xml进行ant构建，构建出的lsdk.jar请自行拷贝到shell工程的`libs`目录下。

plugin代码修改后，需要运行该工程下的build_msg.xml进行ant构建，构建出的nplugin.apk将会自动打入`Demo/assets`下。

shell代码修改后，需要运行该工程下的build_msg.xml进行ant构建，构建出的npaysdk_xxx.jar会自动打入`SdkBase/libs`下。

修改SdkWrapper代码后，需要运行该工程下的build_msg.xml进行ant构建，构建出的npayext_xxx.jar会自动打入`Dependent_HY/libs`。

## 注意事项

### 充值比例
本公司所有版本修改游戏目前充值比例都是1:200（即1元=200游戏内货币）。如果某游戏充值比例与1:200不符，需要修改以下渠道的smali代码：

> 果盘、仙侠

因为上述渠道将充值比例写死在代码中，而不是提取出一个游戏配置参数。

### gson包冲突
由于早期设计失误，鸿游SdkBase工程中jar包集成了gson包，而渠道sdk也会依赖gson包，造成版本冲突。需要注意，2.7、2.8版本gson是可以向下兼容的，而2.2、2.3版本gson无法兼容2.7、2.8，因此原则上**保留高版本gson**即可。






