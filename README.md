# 黑苹果折腾记录

## 配置

dell Inspiron 7559

cpu: i5 6300HQ

显卡：intel HD 530

内存：4+8

硬盘：500g西数蓝盘m2接口的sata+500g机械

## 参考文章

### 本机型安装的前期准备：

1. [参考文章1 ]([https://github.com/tanido/dell-inspiron-7559-i5-mojave](https://github.com/tanido/dell-inspiron-7559-i5-mojave)

2. [参考文章2](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/)

3. [参考文章3](https://www.cnblogs.com/katachi/p/12147613.html)

### 完善系统：
[https://github.com/fengwenhua/dell-7559-hackintosh](https://github.com/fengwenhua/dell-7559-hackintosh)

### uifi通用安装参考:
[https://www.bilibili.com/video/BV1fE411W7ei?from=search&seid=12025285269635744764](https://www.bilibili.com/video/BV1fE411W7ei?from=search&seid=12025285269635744764)

## 基础知识

    首先要知道黑苹果很难做到完美，费时费力，如果不是刚需而且是电脑小白建议别碰，很容易导致电脑开不了机进不了系统。黑苹果最难的地方在于驱动，驱动又基本保存在启动盘的efi分区的efi文件夹中，不同机型一般可以用一样的驱动，当然我指的是通用驱动，鼠标键盘背光啥的，网卡一般没有通用驱动。efi文件夹不仅有驱动，还有其他的系统配置，对于黑苹果来说比较重要的是clover文件夹下的config.plist文件和clover/ACPI/patched文件夹，直接关系到系统能不能正常运行。

    所以如果能找到相同机型的efi，至少主板cpu显卡需要一样，就成功了一半。当然没有也可以装，不过非常麻烦。本人的机型在github有其他大佬的efi，所以安装完系统引导开机什么的都不难，不过完善了很久才达到比较完美的状态。

    回到正题，首先需要确定你的电脑/笔记本能不能装黑苹果，只讲几个不能装的例子，amd的cpu，可以装，但比intel难，n卡，可以装10.13以下的系统，这个不太确定具体是多少版本，至少10.14是一定装不了的，而且9系显卡之后也是没有驱动的，其他的自行百度。

    然后需要有个u盘,如果是legacy模式安装黑苹果，需要有mac系统，可以用虚拟机，如果是uifi模式安装则只需要u盘。然后安装过程可以直接参考上面的UIFI通用安装参考就可以了。

    由于本人的7559非常难用uifi进macos的安装页面，只能换legacy模式进行引导。

## 制作引导盘

    首先按照参考文章3，1，2的做法，先在windows下安装macos的虚拟机，记得最好装10.14，也就是Mojave以上的系统，进入macos系统，插上u盘并且确定u盘已经连上虚拟机了，然后根据苹果官网的步骤，下载最终我们想用的mac系统，[https://support.apple.com/zh-cn/HT201372](https://support.apple.com/zh-cn/HT201372)。

    然后用命令

```
diskutil list
```

    查看u盘所在分区，比如下图：

![main-01fe33a0-775a-11ea-84b9-99ce09a6fbba.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e81e03a90c1201385034820/main-01fe33a0-775a-11ea-84b9-99ce09a6fbba.png)

    我的u盘位置为/dev/disk5,**备份**内部文件，然后执行命令：

```
diskutil partitionDisk /dev/disk1 1 GPT HFS+J "install_osx" R
```

    把u盘转为gpt分区，之后使用命令

```
sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/install_osx /Applications/Install\ macOS\ Mojave.app --nointeraction
```

    把安装镜像放到u盘中，注意其中的`install_osx`指的是u盘的名字。

    最后到github下载clover[https://github.com/CloverHackyColor/CloverBootloader/releases](https://github.com/CloverHackyColor/CloverBootloader/releases)

    打开安装，安装位置选择u盘，配置如下

![main-1b8abaa0-775a-11ea-84b9-99ce09a6fbba.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e81e03a90c1201385034820/main-1b8abaa0-775a-11ea-84b9-99ce09a6fbba.png)

    红色部分需要照着勾，其他默认就好，如果需要安装到机械盘，需要把RC脚本的选项也勾上，详见参考文章1，2.

    安装完成后需要把事先找好的efi文件夹中的clover文件夹放进去替换，除了CLOVERX64.efi保留之外都替换掉。如果没有efi的话，应该可以找把其它机型的efi文件夹下的这几个文件夹替换过来

    这几个文件主要关系到能不能引导u盘并且让clover识别u盘内的，macos安装镜像等等，应该是和机型无关的。

## 安装

    制作好启动盘后就可以重启了，但是安装之前要确保你要安装的系统盘的efi分区有300m容量，如果不确定建议去windows下用diskgenius看一下，不够的话扩容一下，不会的百度一下，网上很多教程的。如果是想要双系统记得划一个分区给macos。

    确认efi分区大小之后，重启并设置成legacy引导，引导u盘启动不出意外就能看见clover的启动界面了，选择带有苹果图标的那个安装，然后把需要安装的分区抹掉换成apfs格式，然后安装，整个安装过程可以参考uifi通用安装参考那个视频里面的安装部分。

## 双系统设置

    由于我这个机型本身比较奇葩，windows可以而且默认用uifi引导，而我之前把macos安装到机械硬盘，所以有两个efi分区，可以正常引导，但是我想把这两个系统用一个efi分区引导，折腾了好久，如果你本身windows是legacy引导的就直接把clover文件夹复制到windows原来的efi分区的efi文件夹中就好了，我自己的流程如下：

    在macos系统下，用clover configurator挂载efi分区，如图

![main-72143a10-7762-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e81e03a90c1201385034820/main-72143a10-7762-11ea-b9f2-69c92269ffb0.png)

	，把原来的的efi文件夹**备份**(非常重要)一下，删除efi分区的内容。然后跟虚拟机下用clover安装引导一样，去github下载clover，安装到系统盘，把备份的efi文件夹中的Microsoft复制到efi分区的efi文件夹下，如图

![main-87d190a0-7762-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e81e03a90c1201385034820/main-87d190a0-7762-11ea-b9f2-69c92269ffb0.png)

    重启用legacy模式选择对应的启动盘，应该就能看到clover启动界面了，其中应该包含macos，macos recovery和windows

## 驱动安装以及Bug修复

    到这一步消耗的时间其实远不如后面的多，找驱动问题的解决方法非常耗时，而且很危险，一定要确保u盘在你觉得系统已经完美了或者能凑合用了之前还能引导系统，否则后续操作一旦失败，系统就有可能进不去了。而且后面的问题我们这种小白只能去论坛爬贴，百度，去外网搜解决的方法，如果没找到，基本就没办法了，只能看大佬们什么时候能解决了。

    这里主要参考，完善系统，那里提及的文章[https://github.com/fengwenhua/dell-7559-hackintosh](https://github.com/fengwenhua/dell-7559-hackintosh)

    言归正传，首先一定是要确保鼠标键盘屏幕正常工作，直接用我的colver/kexts就好了，对应的鼠标键盘驱动为ApplePS2SmartTouchPad.kext，屏幕背光驱动WhateverGreen.kext+Lilu.kext,同时声卡驱动AppleALC.kext也依赖于Lilu,耳机驱动CodecCommander.kext，听说已经集成到AppleALC中了，但是我这里好像没效果，还是要单独挂出来。其他的驱动可以自行百度。后面制作dsdt之类步骤的跟上面的文章一样。

    碰到的烦人问题(蓝牙，耳机爆音等问题可以看上面那个文章)：

1. 触控板的问题，本人并不常用触控板，但是macos不能直接关触控板，需要勾选系统偏好设置/辅助功能里面的有鼠标或无线触控板....那个设置，但是，我这里并没有其作用，还是能使用触控板，导致我打字总是会误触，非常烦人。最后百度到VoodooI2C.kext似乎可以让那个功能起作用，然后把VoodooI2C.kext，VoodooI2CHID.kext放到colver/kexts里面就ok了。

2. 屏闪，这个问题比触控板问题还恶心，按照网上的教程装完系统后发现，只要我把屏幕观赏，再唤醒就会不定时屏闪，就是屏幕突然黑了然后又亮起来那种感觉，嗯...可以试试把灯关了然后马上打开，差不多就是那种感觉。直接说解决办法，用clover configurator加载clover下的config.plist，把显卡设置里的`ig-platform-id`改成[这篇文章](https://blog.daliansky.net/Intel-core-display-platformID-finishing.html)中你的独显对应的型号，一般有很多个，只要独显是对的，一个个试，有的会直接开不了机，用u盘引导接着试,如图
   
   ![main-9f4f0dc0-7762-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e81e03a90c1201385034820/main-9f4f0dc0-7762-11ea-b9f2-69c92269ffb0.png)

    我的原来是0x191B0000改成图中这个就没有屏闪了，改成0x193B0005也可以。

3. 鉴定权限，输入密码的时候非常慢，等很久才能确认。解决方法：下载NoTouchID.kext，[https://github.com/al3xtjames/NoTouchID/releases](https://github.com/al3xtjames/NoTouchID/releases) ，然后放到clover/kexts/other文件夹下就好了，我这里使用最新版会导致耳机驱动不起作用..然后下了前一两个版本的用。

4. 监控不显示cpu温度，解决方法：下载最新的fake_smc或者virtual_SMC相关的驱动，clover/kexts/other文件夹下就好了

## 尚存问题

1. 耳机有时候会没有声音，需要重启一下。
2. 耳机休眠之后需要重新插拔，不然没声音。
3. 有时候启动到输入密码的界面会自动重启，睡眠起来输入密码的时候也可能会重启。
4. cpu的频率被限制在2.8GHz，但是CPU-S的测试中变频没问题，而且能到3.2GHz，这个问题在参考文章1也有提及，日常使用没什么感觉。
5. wifi问题，原装的无线网卡不能驱动，无解，只能换网卡或者搞个usb网卡，我选择了后者
6. 独显不能驱动，也无解，因为10.14之后的系统都不支持n卡了。

总结：上面的1234基本都不影响正常使用，耳机的问题可能是我自身配置的问题，也可能是驱动本身还没完善，不过我也懒得研究了。cpu虽然被限制在了2.8GHz，但是笔记本自身4核，也基本能当mbp来用了吧，大概。

## 展示

### 桌面

![main-936c2ff0-7763-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-936c2ff0-7763-11ea-b9f2-69c92269ffb0.png)

### 耳机麦克风

![main-da55dfb0-7763-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-da55dfb0-7763-11ea-b9f2-69c92269ffb0.png)

### USB

![main-fbfa1640-7763-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-fbfa1640-7763-11ea-b9f2-69c92269ffb0.png)

### 集显

![main-0e2f5780-7764-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-0e2f5780-7764-11ea-b9f2-69c92269ffb0.png)

### 音量调节

![main-1fede220-7764-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-1fede220-7764-11ea-b9f2-69c92269ffb0.png)

### 背光调节

![main-3acc1670-7764-11ea-b9f2-69c92269ffb0.png](https://cdn.jsdelivr.net/gh/velor2012/imageHosting/blog/5e84dda992fe9e2b6c699186/main-3acc1670-7764-11ea-b9f2-69c92269ffb0.png)

### 更新

升级15.4，参考大佬[https://github.com/fengwenhua/dell-7559-hackintosh](https://github.com/fengwenhua/dell-7559-hackintosh)的步骤即可。

我升级的时候安装卡特琳娜到最后提示**启动磁盘当中无法设置Windows，提示bless工具无法设定当前的启动**

重启选择之前的开机项还是能进mac系统完成后续步骤，但是安装卡特琳娜的那几个开机项还留在那里，看起来比较难受..

