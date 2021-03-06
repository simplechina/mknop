# 自动构建斐讯N1 OpenWrt固件脚本

# Automatic Build OpenWrt Firmware for PHICOMM N1

**制作脚本已部署到Github Action，真正实现一栈式全自动构建，每天早上六点准时为你构建，无须自行制作，下载即可用**

[![N1-OpenWrt-CI](https://github.com/tuanqing/mknop/workflows/N1-OpenWrt-CI/badge.svg?branch=master&event=repository_dispatch)](https://github.com/tuanqing/mknop/actions)  
 👆👆👆&nbsp;&nbsp;戳上面查看构建状态

**~~找几个人帮忙测试。有网友反馈制作成功之后无法启动，由于目前没有设备，测试不了，另外测试后期一键安装到emmc脚本。有兴趣的加群：[Phicomm N1固件测试交流群](https://shang.qq.com/wpa/qunwpa?idkey=f9af48e72576fd9cdb69690a96a89a3a1a1dfbedc3ae1b9f3174c00886b96477)~~**

## 使用方法

1. Linux环境，推荐使用Ubuntu 18.04 LTS
2. 编译好待构建的OpenWrt固件，不会的自行科普 [Lean's OpenWrt source](https://github.com/coolsnowwolf/lede "Lean's OpenWrt source")  

编译N1固件的配置如下：
``` 
Target System (QEMU ARM Virtual Machine)  --->
Subtarget (ARMv8 multiplatform)  --->
Target Profile (Default)  --->
```

3. 克隆仓库到本地  
`git clone https://github.com/tuanqing/mknop.git` 
4. 将你编译好的固件拷贝到OpenWrt目录（可以复制多个固件到此）
5. 使用sudo执行脚本  
`sudo ./make` 
6. 按照提示操作，如：选择你要制作的固件、设置ROOTFS分区大小等
7. 等待构建完成，默认输出文件夹为out
8. 写盘启动，写盘工具推荐 [Etcher](https://github.com/balena-io/etcher/releases/download/v1.5.80/balenaEtcher-Portable-1.5.80.exe)

**注意**：  
1、待构建的固件格式只支持rootfs.tar.gz、 ext4-factory.img[.gz]、root.ext4[.gz] 5种  
2、默认不会清理out目录，有需要的手动 `rm` ，或者使用 `sudo ./make -c` 清理  
3、一键安装到emmc命令为：  
`cd /root && ./install.sh`

## 特别说明

* 目录说明
   * `armbian` ，armbian相关文件
      * `phicomm-n1` ，设备文件夹
         * `root` ，自定义文件夹
         * `boot.tar.gz` ，启动分区相关文件
         * `firmware.tar.gz` ，设备驱动文件
         * `modules.tar.gz` ，内核模块文件
   * `openwrt` ，用于存放待构建的OpenWrt固件
   * `out` ，输出文件夹，用于存放构建好的OpenWrt固件
   * `tmp` ，临时文件夹，用于脚本转储

* 使用参数
   * `-c, --clean` ，清理临时文件和输出目录
   * `-d, --default` ，使用默认配置来构建固件，这会将以 "选择openwrt目录的第一个固件、ROOTFS分区大小默认设置为512m" 的方式构建固件
   * `-l, --link` ，如果你替换了内核，请使用此参数来帮助你链接内核模块
   * `-s, --size=SIZE` ，设置ROOTFS分区大小，不要小于256m
   * `-h, --help` ，显示帮助信息
   * example：  
`sudo ./make -c` ，清理文件  
`sudo ./make -d` ，使用默认配置  
`sudo ./make -s 256` ，将ROOTFS分区大小设置为256m  
`sudo ./make -d -s 256` ，使用默认，并将分区大小设置为256m  

* 自定义
   * 使用自定义内核  
     你可以在Armnbian镜像中提取，并按照目录结构，将文件打包为boot.tar.gz、modules.tar.gz，最后替换armbian/phicomm-n1目录下的相关文件即可

     **注意**：  
     1、如果Armnbian镜像的BOOT分区下的 uEnv.txt中的FDT参数不是phicomm-n1，则需要你手动更改，不更改则导致无法启动  
     2、替换内核后需要使用 `-l` 参数来链接内核模块文件，如果你在提取时已经做了这一步，请忽略

   * 添加自定义文件  
      向armbian/phicomm-n1/root目录添加你想要的文件

      **注意**：添加的文件应保持与ROOTFS分区目录结构一致
