> 当前OpenCore版本 0.7.2， macOS 版本 Big Sur 11.5.2

- 2021年08月24日：升级OpenCore 0.7.2， 完成USB Mapping， 升级至macOS Big Sur 11.5.2
- 2021年02月27日：已直升macOS11.2.2

## 本机配置

| Type        | Detail                                         |
| ----------- | ---------------------------------------------- |
| CPU         | Intel i7 10700K                                |
| GPU         | Sapphire AMD RX 5700XT 8GB超白金               |
| MotherBoard | Asus ROG STRIX Z490-A Gaming 吹雪              |
| RAM         | 32G GSkill Trident Z Royal 3200MHz DDR4 16 * 2 |
| SSD         | Samsung NVMe 970 EVO Plus 500GB                |
| 无线网卡    | BCM94360CD                                     |



## 1. 启动盘制作

- Mac环境
- 16G优盘

在AppStore下载BigSur， 打开Terminal终端， 输入以下命令（命令中的USBName就是你插入优盘的优盘名）

 ```
sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
 ```

对于EFI分区的创建， 下面的EFI配置会说明



## 2. SMBIOS 序列号生成

打开Terminal终端， 输入以下命令， Do the following one line at a time in Terminal:

    git clone https://github.com/corpnewt/GenSMBIOS
    cd GenSMBIOS
    chmod +x GenSMBIOS.command

Then run with either `./GenSMBIOS.command` or by double-clicking *GenSMBIOS.command*

双击GenSMBIOS.command， 生成SMBIOS

将生成的uuid等信息复制到Config.plist - PlatformInfo对应字段

**请务必替换成自己的SMBIOS**

- MLB 主板序列号
- SystemProductName iMac20,1等
- SystemSerialNumber 序列号
- SystemUUID 



## 3. EFI分区

为了创建EFI分区，需要使用 [MountEFI](https://github.com/corpnewt/MountEFI) ， 使用这个工具可以为一个磁盘创建一个EFI分区。（或者直接使用hackintool 磁盘那里创建）

安装系统前，需要为优盘创建EFI分区，最后将配置好的EFI文件夹复制到这个分区里； 安装系统后需要为Mac系统盘创建EFI分区， 并将优盘EFI分区里的EFI文件夹复制到Mac系统盘的EFI分区里， 这样就不用依赖优盘去引导macOS。注意⚠️：重启或者插拔优盘都会是EFI分区“消失”， 需要重新运行Mount.command创建（使其显示）EFI分区

打开Terminal终端， 输入以下命令 Do the following one line at a time in Terminal:

    git clone https://github.com/corpnewt/MountEFI
    cd MountEFI
    chmod +x MountEFI.command

Then run with either `./MountEFI.command` or by double-clicking *MountEFI.command*

双击MountEFI.command， 选择对应的磁盘创建EFI分区



## 4. EFI配置

按照[OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/prerequisites.html)配置EFI文件

**值得一提的是因为本机是华硕主板， 所以ACPI需要加入一个SSDT-RHUB.aml, 否则安装会失败**

- 一些ACPI说明
  - SSDT-PM.aml 可实现节能五项
  - SSDT-RHUB.aml 是为了解决Asus主板的一些问题
  - SSDT-RX 5700 XT-Version 1.0.aml 是优化5700xt的acpi

- Drivers
  - HfsPlus.efi 必须
  - OpenRuntime.efi 必须
  - AudioDxe.efi 开机钟声， 可不加
  - OpenCanopy.efi 启动界面美化， 可不加

⚠️ **增减ACPI、Drivers和Kexts的文件时， 需要在Config.plist相对应的位置做相应增减**

| EFI - OC | Config.plist - Root |
| -------- | ------------------- |
| ACPI     | ACPI - Add          |
| Drivers  | UEFI - Drivers      |
| Kexts    | Kernel - Add        |



## 5. BIOS启动项配置

禁用

- fastboot
  - 启动 - 启动设置- 快速启动 - Disable
- 操作系统类型改为UEFI
- 禁用安全启动
  - 清除密钥即可

其他的Z490A主板默认即可符合OpenCore官方要求



## 6. 启动界面美化

OpenCore自带的界面我是比较难以接受的， 所以按照OpenCore官方教程美化了一下界面， 只要两步：

1. 首先需要将[Resources文件夹](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt)放到OC根目录下， 这个目录文件都是美化界面所需的音频、字体、图像等资源。这里的Resource文件夹是OpenCore Desktop Guide中 macOS BigSur 风格的启动界面资源， 如果不行， 请下载最新版OpenCore Resource资源。

2. 在EFI/Drivers添加OpenCanopy.efi ， 同时在config.plist - UEFI - Drivers 中添加一个 item

这样界面基本就比较好看了， 但是因为本人比较强迫症， 除了Win和mac的启动项外， 其他的都想要隐藏， 比如Recovery， OpenShell, ResetNvram， 查了一些资料， 只需在Config.plist中按照以下配置即可

| 要隐藏的启动项       | Config.plist设置                                             |
| -------------------- | ------------------------------------------------------------ |
| Recovery             | Misc - Boot - HideAuxiliary 设置为 1                         |
| OpenShell.efi        | Misc - Tools 找到OpenShell.efi 这个item, 在item里将 Auxiliary 设置为1 |
| ResetNvram           | Misc - Security - AllowNvramReset 设置为 0                   |
| 进入默认磁盘等待时间 | Misc - Boot - Timeout 默认为5秒， 我这里改成了 3秒， 给我蓝牙键盘反应是够了吧😂 |



## 7. Trouble Shooting 问题解决

#### 1. 4K 60Hz

连接网络后无法4K 60hz显示

显示器： Dell 2718Q  线材 DP to miniDP

显示器设置里按住Option + 点击缩放， 就会出现刷新率选择



#### 2. 有线网络

Asus ROG STRIX Z490-A Gaming 吹雪主板自带的有线网卡是**Intel-I225-V**

按照OpenCore官方在Config.plist - DeviceProperties 中添加device-id <F2150000>并没有作用
在此基础上添加了两个Kext才驱动了有线网卡,  config.plist要对应在Kernel里Add相应的Kext

* FakePCIID.kext
* FakePCIID_Intel_I225-V.kext



#### 3. Asus主板卡F1问题

在Config.plist 里搜索 DisableRtcChecksum 设置为1

如果还不行建议参照 [RTC综述 - Xjn’s Blog](https://blog.xjn819.com/post/rtc-issues-related-to-oc.html)



#### 4. 节能五项

添加了SSDT-PM.aml 并在Config.plist - ACPI中Add item



#### 5. 声卡问题

Asus ROG STRIX Z490-A Gaming 吹雪使用的是 **ROG SupremeFX 8** 声卡芯片， 好像是**Realtek ALCS1220A**的马甲

使用[Hackintool](https://github.com/headkaze/Hackintool)注入正确的ALC LayoutID即可



#### 6. 更改默认启动磁盘

- 设置EFI文件夹 - OC - Config.plist   UEFI - Quirks - RequestBootVarRouting - 1 or YES

- 系统偏好设置 - 启动磁盘 - 选择mac磁盘

其实只需要在启动选择页面选中磁盘， 按 ctrl + enter 即可😂



#### 7. USB Map

已完成

Hackintool需要将SSDT-RHub.aml删除才能显示USB， map完成再放进去即可
或者使用iMac20,x_USBInjectAll_v0.7.5_z490.kext也行

删除了USB-C， 背板只有网口上一排两个USB接口支持USB3.0



#### 8. macOS Windows时间不同步问题

搜索cmd， 找到命令提示符， **以管理员身份运行**， 输入以下代码：

```
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
```



## 8. 参考链接

装黑苹果的过程中， 一下链接给了很大帮助， 感谢！ Thanksssss 

[SMBIOS](https://github.com/corpnewt/GenSMBIOS)

[MountEFI](https://github.com/corpnewt/MountEFI)

[OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/prerequisites.html)

[OpenCore Post-Install](https://dortania.github.io/OpenCore-Post-Install/)

[Hackintool](https://github.com/headkaze/Hackintool)

[Xjn’s Blog](https://blog.xjn819.com/)

ROG Z490 黑苹果交流 QQ群



### 9. 截图 Screenshoot

![AboutHackintosh](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/AboutHackintosh.png?raw=true)

![CPUScore](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/HackintoshCPUScore.png?raw=true)

![](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/HackintoshOpenCLScore.png?raw=true)

![Hackintosh Metal Score](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/Hackintosh Metal Score.png?raw=true)

![CINEBENCH-CPU-SingleCore](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/CINEBENCH-CPU-SingleCore.png?raw=true)

![CINEBENCH-CPU-MultiCore](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/CINEBENCH-CPU-MultiCore.png?raw=true)

![970EVOPlus](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/blob/main/Assets/970EVOPlus.png?raw=true)

