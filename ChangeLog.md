## ChangeLog

### 2023-09-30
1. Update macOS Ventura 13.6 via OTA succeeded!

### 2023-07-27
1. Update macOS Ventura 13.5 via OTA

### 2023-06-25
1. Update macOS Ventura 13.4.1 via OTA

### 2023-05-19
1. Update macOS Ventura 13.4 via OTA

### 2023-05-09
1. Update OpenCore 0.9.2

### 2023-04-16
1. Update OpenCore 0.9.1
2. Update macOS Ventura 13.3.1 via OTA

### 2023-03-29
Update macOS Ventura 13.3 via OTA succeeded!
TODO: update OpenCore

**调试及问题**

SetApfsTrimTimeout 设置为0后开机启动时间大幅缩短

这个版本Reset Nvram会消失， 可以在Tools里把CleanNvram.efi放到Drivers中， Config.plist 同样添加

⚠️ Reset Nvram后可能导致UEFI启动项消失， 可以[使用Easy UEFI修复](https://blog.csdn.net/weixin_45456085/article/details/127280792 "")

### 2022-03-10 

Update OpenCore 0.7.9, Update macOS Monterey 12.2.1

⚠️ FakePCI*.kext may cause reboot in macOS 12, just remove all the FakePCI*.kext, and add `dk.e1000=0` at the boot-args, thank you [@CharlesCCC](https://github.com/CharlesCCC) for the [issue](https://github.com/evenlinyf/hackintosh-EFI-Z490A-i710700k-5700xt/issues/1#issue-1035692471)

### 2023-02-14
Update macOS Ventura 13.2.1 via OTA succeeded!
Valentine's Day ??? 😂

### 2023-01-27
Update macOS Ventura 13.2 via OTA succeeded!

### 2022-12-27
Update OpenCore 0.8.7, macOS Ventura 13.1
- SetApfsTrimTimeout set to 0 (default -1) (fix samsung 970 evo plus apfs trim)
- Update Resources
- Update kexts
- To drive i225-v
  - add SSDT-I225V
  - add AppleIntel210Ethernet.kext (get from macOS Monterey)
  - add boot-arg `dk.e1000=0`  —> `e1000=0`

### 2021-08-24
Update OpenCore 0.7.2， Finish USB Mapping， Update macOS Big Sur 11.5.2
