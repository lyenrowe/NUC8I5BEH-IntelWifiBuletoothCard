## Intel NUC8I5BEH EFI for Monterey 12.* (Intel WIFI + Bluetooth)

### 适用范围

1. 适用于 NUC8I5BEH，原装Intel蓝牙和wifi

2. macOS系统 Monterey

### opencore 0.9.0

### BIOS 设置

参考： https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html#intel-bios-settings

我的BIOS版本是0092，文中提到设置项有如下关联项，可以BIOS设置界面顶部搜索框中搜索下面的选项关键字。

#### Disable

- Fast Boot  --默认未勾选
- Secure Boot  --去掉勾选
- Interl VT for ...(VT-d)  --去掉勾选
- Intel Platform Trust Technology  --去掉勾选

#### Enable

- Hyper-Threading Technology  --默认已勾选
- Chipset SATA Mode: AHCI  --默认已勾选

### 安装说明

1. 默认是Macos+Windows的双系统引导，如只引导Macos单系统，请将config-macos单系统.plist重命名为config.plist（原config.plist删除）

2. 系统安装完成后自行修改序列号等相关配置

   + https://checkcoverage.apple.com/ 在此站点查询Serial，确保是无效的（没有被使用）。

   + 新装黑苹果生成序列号等SMBIOS信息可使用工具 https://github.com/corpnewt/GenSMBIOS 。详情查看 https://dortania.github.io/OpenCore-Install-Guide/AMD/fx.html#platforminfo

     ```sh
       #######################################################
      #               MacPro7,1 SMBIOS Info                 #
     #######################################################
     
     Type:         MacPro7,1
     Serial:       F5KZV0JVP7QM
     Board Serial: F5K9518024NK3F7JC
     SmUUID:       535B897C-55F7-4D65-A8F4-40F4B96ED394
     Apple ROM:    001D4F0D5E22
     ```

     对应关系：

     `Type` <--->  SystemProductName

     `Serial` <---> SystemSerialNumber

     `Board Serial` <---> MLB

     `SmUUID` <---> SystemUUID

     `Apple ROM`<---> ROM

   + 编辑信息可使用 https://github.com/corpnewt/ProperTree ，当然也可以使用文本方式修改，但要注意其中的`Apple ROM`（以太网mac地址）是二进制数据。

   + 老的黑苹果应将之前的SMBIOS信息复制过去，而不是重新生成。所以老的EFI替换之前要做好备份

### 问题

- WiFi蓝牙非白果卡，Airdrop不能使用。

### 替换EFI

挂载EFI分区

```shell
diskutil list
sudo diskutil mount /dev/diskXs1
#其中 X 是磁盘编号。比如我们要挂载disk0的EFI Partition，它的标识符是disk0s1，命令如下。
sudo diskutil mount /dev/disk0s1
```

### 问题日志

- 2023-04-01 发现不能正常睡眠，刚进入睡眠约2秒，就又被唤醒了。后来发现是接在SATA接口的机械硬盘（这上面安装了window10）引起。

  通过系统设置解决，进入 偏好设置 -> 节能

  ​	`disable`启用电能小憩 --未插入机械硬盘时启用也不影响睡眠，有机械硬盘后必须得关闭

  ​	`enable` 如果可能，使硬盘进入睡眠 --未插入机械硬盘时没看到这个选项
  
  当然这期间BIOS中所有power相关的设置（从休眠中唤醒）我都设置成off或disable。如：`Wake on LAN from S4/S5`: stay off，其他值不记得了。我觉得和这应该没有关系。暂未调回来做验证