# raspberrypi-kernel-compilation
## 下载内和源码
git下载(unstable): git clone --depth=1 https://github.com/raspberrypi/linux.git  
stable版本: https://github.com/raspberrypi/linux/tags  
## 下载aarch64-linux-gcc
https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-a/downloads  
x86_64 Linux hosted cross compilers -> AArch64 GNU/Linux target (aarch64-none-linux-gnu)

## 配置
```
touch .scmversion
make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- bcm2711_defconfig
```

## 编译
```
make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- Image modules dtbs -j8
```

## 常见错误:
### openssl
```
scripts/extract-cert.c:21:10: fatal error: openssl/bio.h: No such file or directory
   21 | #include <openssl/bio.h>
      |          ^~~~~~~~~~~~~~~
compilation terminated.
make[1]: *** [scripts/Makefile.host:95: scripts/extract-cert] Error 1
make[1]: *** Waiting for unfinished jobs....
```
```
sudo dnf install openssl-devel
```

## 复制内核到SD卡
插入SD卡，将SD卡的boot分区挂载到/mnt/boot，rootfs挂载到/mnt/rootfs
```
$ sudo cp arch/arm64/boot/Image /mnt/boot/kernel8.img
```

将arch/arm64/boot/dts/broadcom/下的dtb复制到SD卡
```
# Pi 4
$ sudo cp arch/arm64/boot/dts/broadcom/bcm2711-rpi-*.dtb /mnt/boot/
# 全部复制
$ sudo cp arch/arm64/boot/dts/broadcom/*.dtb /mnt/boot/
```

## 修改/boot/config.txt加上（如果没有的话）
```
kernel=kernel8.img
```

## 下面的命令在必须以root运行，不能是sudo!
```
# make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- INSTALL_MOD_PATH=/mnt/rootfs modules_install
# make ARCH=arm64 CROSS_COMPILE=aarch64-none-linux-gnu- INSTALL_MOD_PATH=/run/media/nereus/rootfs modules_install
```

## umount SD卡，结束
