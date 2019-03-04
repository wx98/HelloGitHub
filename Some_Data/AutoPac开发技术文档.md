
版本 | 修改人 | 备注
---|---|---
0.9 | 雍高超 | 初稿 
1.0 | 雍高超 | 完善内容

## 1 前言
AutoPac是专门用于本公司手游联运业务的游戏渠道分包工具。

AutoPac一般应用步骤如下：

1. cp方接入我们sdk，打出母包。此母包渠道号为CP00，为鸿游渠道的包。
2. 通过AutoPac向母包中动态注入第三方渠道sdk的资源和代码。
3. 修改包名、替换带角标icon等其他工作...
4. 分出渠道包。

本文档适用读者为网游联运业务开发技术人员以及渠道sdk分包使用人员。

## 2 运行环境搭建

操作系统为Windows，暂不支持Linux等其他系统。

1. 安装python 2.7。
2. 安装xlwt-1.0.0
3. 安装xlrd-0.9.4
4. 确保本机已经安装Android Sdk。
5. 从SVN检出本项目后，修改`Util/Constant.py`中的常量`BUILDTOOL_PATH`，参照示例修改为Android Sdk路径。推荐这里修改为23版本。
```
BUILDTOOL_PATH = r'D:\ASS\build-tools\23.0.3'
```

## 3 运行AutoPac

> win10用户请先关闭Windows Defender，否则会明显降低打包速度，甚至偷偷删除某些文件，造成打包失败。

### 3.1 基本操作

运行Launcher.bat，出现可视化界面和日志cmd窗口。左上多渠道打包表示单个游戏母包出多个渠道分包；而单渠道打包表示多个游戏分出某个渠道的分包。在可视化界面选择想要分渠道包的母包apk。在界面右侧是AutoPac支持的所有渠道，多渠道模式可以多选想要打出的渠道。

点击Decode，解包apk，日志出现`Decode task finished!`可进行到下一步。

点击Merge，合并资源，日志出现`All merge tasks finished!`可进行到下一步。

最后，点击Encode，打包并签名渠道apk，日志出现`All Encode tasks finished!`说明打包完成。apk最终输出在final目录下。

> 如果以上Decode过程出现1min以上的卡死时间，可能是`Decompile`目录下文件过多导致清空该目录时间过长，请耐心等待。

### 3.2 icon替换
某些渠道对icon有角标要求，可以将带角标的icon复制到`GameInfo/（appid）/（渠道号）/icon.png`。注意，文件名必须为icon.png。打包阶段会自动检查渠道对应的目录下是否存在该文件，若存在，则替换掉所有游戏工程中的icon。

### 3.3 包名后缀
`Util/PlatformStore.py`中配置了所有渠道基本信息，其中`packageNameSuffix`指定了包名后缀：


```
# 键为渠道号
{
    'ZY-YW-10195': {
        'platformCode': 36,     # 该渠道数字渠道号
        'realizeClassName': 'com.android.sdk.ext.ThirdPayRealize_YW',
        'packageNameSuffix': '.yxa'     # 修改这里
    }
}
```

更多渠道配置请参考`7.1 渠道配置`。

## 4 项目概述

主要目录结构如下：
- **BaseApk** 存储母包。
- **Decompile** apk解包后，解包工程存放的目录。
- **final** 渠道包最终输出目录。
- **GameInfo** 游戏信息文件夹，存放了该游戏所接的所有渠道、对应渠道的icon等文件。
- **Templates** 渠道模板，打包时会将模板文件注入到游戏包中。
- **Tools** 打包所需aapt等工具。
- **Launcher.bat** 打包工具启动器。

运行项目请启动`Launcher.bat`。工具分出渠道包一般分为3个步骤：解包游戏apk；将渠道sdk注入游戏；打包游戏apk并签名。其中，解包与打包只是apktool的调用，而注入过程比较复杂，因此本文档着重介绍注入过程。

## 5 注入（Merge过程）原理

非技术人员请跳过本节。

合并sdk到游戏包的过程可参考`Util/Merge.py`，大致步骤如下：
```
配置文件更新
  读取hyinfo.store中的appId，拷贝最新配置文件到assets下
拷贝资源文件
  拷贝drawable、layout等
  合并color、style等values下的资源
合并AndroidManifest.xml
运行aapt，将aapt生成的R.java转换为smali替换原R文件
拷贝assets
拷贝lib
拷贝smali以及双dex的兼容处理
拷贝平台相关补丁文件，替换icon
```

### 5.1 配置文件更新
游戏包事先集成了鸿游sdk，游戏包中`assets/hyinfo.store`的`mergeAppId`标识了游戏。打包工具会在这个阶段搜索`GameInfo`目录，根据appId找到最新的游戏配置文件，将其拷贝复制到游戏的`assets`目录下覆盖同名`hyinfo.store`。

### 5.2 合并资源文件
渠道sdk的资源分为两类：能直接拷贝过来的文件，例如drawable、layout；能直接拷贝的文件，例如values下的资源，需要逐项比对，与游戏中的资源合并（同名覆盖）。最后，由于新增了资源文件，而这些资源文件在及R文件中没有记录，因此需要运行aapt，重新生成R文件。生成的R文件是java代码，需要经过一系列转换成为smali代码，拷贝到游戏相应的包名下。

### 5.3 合并AndroidManifest.xml
读取hyinfo.store，如果有需要写入AndroidManifest.xml的游戏参数，则写入。读取原文件AndroidManifest.xml，将activity、service等对比原文件去重拷贝。修改包名、启动activity（如果有的话）。

### 5.4 合并assets
将sdk所需的assets、lib文件拷贝游戏目录。需要注意lib下各指令集目录中的.so文件个数保持一致，文件一一对应。一般保留`armeabi`目录即可。如果游戏只有`armeabi-v7a`，则只保留这个目录。

### 5.5 合并smali
将sdk代码拷贝游戏目录。`Templates/（渠道号）/MergeContext.txt`配置了模板需要拷贝到游戏的smali目录。

这里需要考虑方法数超65535导致回编译失败的情况，一般分为：
- 游戏本身已经是双dex。
- 游戏本身有两个以上dex。
- 游戏是单dex，但是拷贝渠道sdk代码后，方法数超过65535。
 
其中本工具解决了1，因为2、3几乎无法遇到。

### 5.6 其他操作
替换icon、启动activity等。

## 6 添加游戏

### 6.1 游戏配置

游戏配置文件管理游戏在各渠道的参数，一般使用人员无需手动修改配置文件，而是去管理后台添加渠道参数。管理后台自动生成配置文件，并提交SVN，打包工具端只需拉取即可。

游戏配置文件位于`GameInfo`目录，管理后台配置渠道参数后，会将最新配置文件、icon等传到SVN，而打包工具只需要拉取即可获得最新的游戏配置。

一个典型的渠道配置文件用json描述，其大体结构如下：

```
{
 "meta-data": [
 
 ], 
 "platformCode": 1, 
 "realizeClassName": "com.android.sdk.ext.ThirdPayRealize_AB", 
 "version": "1.1.4", 
 "placeHolder": [
 
 ], 
 "versionCode": 14, 
 "applicationRealize": "", 
 "mergeAppId": "666666",
 "screenOrientation": "landscape"
}
```

其中各属性有效阶段一列分为：
- **运行时（R）**  运行过程中全程有效的属性。
- **初始化前运行时（IR）** sdk初始化前需要的属性。
- **打包时（P）**  运行打包工具需要读取该属性。
- **其他(O)**  一般是后台管理系统读取的标识。

越靠上的属性有效期越短，例如打包时(P)的属性即使在运行时仍然可以有效，而运行时(R)的属性在打包阶段可能还未赋值。


属性 | 类型 | 描述 | 有效阶段
---|---|---|---
platformCode | int | 渠道号 | IR
mergeAppId | int | appId | P
version | string | sdk版本 | R
versionCode | int | sdk版本的整数标识 | R
realizeClassName | string | 渠道sdk封装类 | IR
applicationRealize | string | 默认application | IR
meta-data | string | 各渠道参数 | R
placeHolder | string | AndroidManifest.xml中的字符串动态替换配置 | P
screenOrientation | string | 屏幕方向，取值landscape/portrait | IR

meta-data示例：

```
{
   "platformCode": 12, 
   "injectToManifest": true, 
   "description": "玖毛支付密钥（固定值）", 
   "value": "CgsPEgsLEg8LCg==", 
   "name": "paysdk_signkey",
   "editable": false
}
```

meta-data中各属性说明如下：

属性 | 类型 | 描述 | 有效阶段
---|---|----|---|
platformCode | int | 指示当前参数所属渠道 | IR
name | string | 参数名 | IR
value | string | 参数值 | IR
description | string | 参数描述 | IR
injectToManifest | boolean | 指示是否将该参数写入到AndroidManifest.xml | IR
editable | boolean | 标识后台管理系统是否可以修改该属性 | O

placeHolder示例：

```
{
   "to": "${mergeContext.packageName}", 
   "platformCode": 32, 
   "from": "hyplaceholder_qb1", 
   "description": "趣吧支付配置游戏包名",
   "editable": false
}
```

在这个例子中，`AndroidManifest.xml`中所有字符串`hyplaceholder_qb1`会被替换为游戏的包名。`${mergeContext.packageName}`是一种类el表达式写法，表示游戏包名。

placeHolder中各属性说明如下：

属性 | 类型 | 描述 | 有效阶段
---|---|---|---|
platformCode | int | 指示当前参数所属渠道 | IR
from | string | 需要被替换的文本 | IR
to | string | 替换后的文本 | IR
description | string | 参数描述 | IR
editable | boolean | 标识后台管理系统是否可以修改该属性 | O

目前支持的类el表达式如下：

属性 | 描述
---|---
mergeContext.packageName | 游戏包名
mergeContext.screenOrientation | 游戏屏幕方向

## 7 添加渠道

如果想让Auto支持更多的渠道分包，请参考本节。

### 7.1 渠道配置

渠道配置文件是各渠道打包过程的定制化配置，例如包名后缀、闪屏、自定义application等，一个典型的渠道配置如下所示：

```
{
    # ...
    
    'ZY-ZYSC-10215': {
        'platformCode': 47,
        'name': '卓易',
        'realizeClassName': 'com.android.sdk.ext.ThirdPayRealize_ZY',
        'applicationRealize': 'com.android.sdk.ext.ZYApplication',
        'overrideLaunchActivity': True,
        'packageNameSuffix': '.zy'
    },
    
    # ...
}
```

渠道配置文件位于`Util/PlatformStore.py`。请参照相关注释修改。

属性 | 描述
---|---
platformCode | 渠道号
name | 渠道名
realizeClassName | 渠道sdk封装类
applicationRealize | 渠道application代理类
packageNameSuffix | 渠道包名后缀要求
launchMode | 渠道启动模式要求
fissureRToPackage | 在指定目录下生成R文件的一份拷贝
overrideLaunchActivity | 将游戏的主Activity的启动标记擦除
v2Splash | 在AndroidManifest.xml中添加闪屏配置，添加此项时，overrideLaunchActivity默认置为true。

### 7.2 制作渠道模板(Template)
Template指第三方渠道sdk的资源文件、代码等的增量包，打包工具会将其动态注入apk（apk需要首先集成鸿游sdk）。

制作基本步骤：

- svn上检出shell代码工程。
- 新建工程Dependent_XX（XX为渠道名），将渠道sdk的代码、资源等拷贝到工程中。
- 删除有明显可能与app冲突的资源，例如`<string name="app_name">HUOSDK</string>`。
- shell中Demo工程依赖Dependent_XX，并且Dependent_XX依赖SdkBase，跑出demo包。
- 将demo包解包，复制整个解包后的工程到`（AutoPac根目录）/Templates/(渠道号)`目录下。
- 删除assets下与sdk无关的assets文件（例如hyinfo.store）。
- 删除AndroidManifest.xml中与sdk无关的组件配置。
- `（AutoPac根目录）Templates/(渠道号)`目录下新建MergeConfig.txt，配置需要注入的代码。
- `Util/PlatformStore.py`中配置该渠道。
