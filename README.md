# The Hisillicon TX9 Pro Max Project

## **Disclaimer**
* **I am not responsible for any of the damages that may be inflicted to your device.** 
* **The incorrect installation of an operating system (OS) on an embedded computer system may render it permanently unuseable, often referred to as *"bricking."***
* **Never violate the End User License Agreement of a manufacturer's device, *violations include reverse engineering the source code of properietary software and uploading it to the internet or using it for commercial purposes.***
* **If users intend to install their own system software on such devices, they must analyze the device's hardware specifications and think critically before attempting to do so.**

## Preface
The Hisillicon TX9 Pro Max Project's GitHub repository is dedicated to the development of a custom build of the Armbian Trixie GNU/Linux distribution (minimal or server variant), referred onwards as Armbian GNU/Linux, for the use of a cheap, common TV box that goes by the name of 'TX9 Pro Max.' Since the device is an open-source, white-label product; many different third-party Chinese companies resell it, so I did not specify its brand (the company that sells it). The TV box is found in television (TV) box and video accessories stores across Bangladesh. Although the boxes claim highly performative device hardware specifications (e.g., 256 Gigabytes of on-device non-volatile memory, 16 Gigabytes of Random Access Memory, Allwinner H313 System on a Chip (SoC)) the TV box comprises of significantly less performative device hardware specifications, as given below:

<img width="589" height="601" alt="Screenshot from 2026-06-24 13-32-07" src="https://github.com/user-attachments/assets/946d4ac7-df1b-49bc-a556-c56b798f5e4e" />

I initially began developing this project to learn more about embedded Linux systems, as I aspire to work professionally with embedded and IoT systems. The project includes the important documentation of the Linux kernel, running on the Allwinner H616 SoC. Allwinner H616 (sun50iw9p1) is a SoC that features a Quad-Core Cortex-A53 ARM CPU, and a Mali-G31 MP2 GPU from ARM. It is targeted for low-end TV boxes that require hardware accelerated transcoding capability for media streaming, usually of popular media streaming platforms like YouTube (e.g., Tanix TX9, Tanix TX9 Pro (original), TX9 Pro Max (non-specific brand; counterfeit model), X96, X96Q, etcetera). [Source](linux-sunxi.org/H616)

This repository contains the full procedure of research, development and installation of the custom build of Armbian GNU/Linux, on the H616-powered machine. It even includes the mistakes that were made during the entire process. The whole project, including all the files are protected as *open-source* and is provided with *absolutely* no warranty, to the extent the law provides. Therefore, you do not require crediting the owner.

## Research

The primary resource for learning how to build the customized variant of Armbian GNU/Linux for this exact TV box is given below:
Note: This has been taken from the internet and abridged by using an LLM, so it may contain mistakes caused by hallucination. The link of the thread where it has been taken from: [en.eeworld.com.cn/bbs/thread-1245672-1-1.html](https://en.eeworld.com.cn/bbs/thread-1245672-1-1.html)

Markdown
# Linux Sunxi (Allwinner H616 / MQ-Quad) Build and Boot Guide

## 1. U-Boot Build and Boot from USB

### Install Dependencies and Pull Source
bash
# Install sunxi-fel tools and Arm Trusted Firmware (arm64)
# Pull down the latest U-Boot
Modify U-Boot Configuration
For the MQ-Quad, there is no Ethernet interface.
    1. Execute make menuconfig.
    2. Turn off network support on the homepage: [ ] Networking support.
    3. Use the AXP313 power management chip by modifying the driver file: drivers/power/axp305.c.
Compile and Boot U-Boot
Bash
# Compile U-Boot
# Boot from USB using sunxi-fel
../sunxi-tools/sunxi-fel uboot u-boot-sunxi-with-spl.bin
2. Linux Kernel Compilation
Download Mainline Kernel
Bash
git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
# If speed is slow, download the mainline kernel tarball directly from [https://kernel.org](https://kernel.org)
tar -xvzf linux-6.0-rc3.tar.gz
Note: Because the 6.0 kernel is too new and many drivers do not support it, you can alternatively use the stable version of the kernel for driver porting from the H616 mainline maintainer:
Bash
git clone -b h616-v13 [https://github.com/apritzel/linux](https://github.com/apritzel/linux)
Modify the Device Tree
File: arch/arm64/boot/dts/allwinner/sun50i-h616-orangepi-zero2.dts (or mq-quad.dts)
Because the 3.3v of MQ-Quad is not connected to PMIC for control, it is necessary to add a Fixed 3.3v node. Add reg_vcc3v3 under the reg_vcc5v node (otherwise the IO cannot be initialized, resulting in serial port failure).
DTS
reg_vcc3v3: vcc3v3 { 
    compatible = "regulator-fixed"; 
    regulator-name = "vcc-3v3"; 
    regulator-min-microvolt = <3300000>; 
    regulator-max-microvolt = <3300000>; 
    regulator-always-on; 
};
Modify reg_aldo1 in the pio node to reg_vcc3v3:
DTS
&pio { 
    vcc-pc-supply = <&reg_vcc3v3>; 
    vcc-pf-supply = <&reg_vcc3v3>; 
    vcc-pg-supply = <&reg_vcc3v3>; 
    vcc-ph-supply = <&reg_vcc3v3>; 
    vcc-pi-supply = <&reg_vcc3v3>; 
}; 
Modify mmc0's vmmc-supply (reg_dcdce) to reg_vcc3v3 (otherwise the sunxi-mmc driver cannot start):
DTS
&mmc0 { 
    vmmc-supply = <&reg_vcc3v3>; 
    cd-gpios = <&pio 5 6 GPIO_ACTIVE_LOW>; /* PF6 */ 
    bus-width = <4>; 
    status = "okay"; 
};
Compile the Kernel, DTB, and Modules
Bash
# Use the default configuration
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- defconfig 

# Compile the kernel image
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 Image

# Compile device tree
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 dtbs 

# Compile modules (optional, used after making the file system)
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 modules 
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=modules modules_install 

# Install header files (convenient for later cross-compilation)
make ARCH=arm64 INSTALL_HDR_PATH=headers_install 
3. Buildroot and WiFi Driver Porting
Port the RTL8723DS WiFi Driver
    1. Clone the driver to the wireless directory:
       Bash
       git clone [https://github.com/lwfinger/rtl8723ds](https://github.com/lwfinger/rtl8723ds) /drivers/net/wireless/realtek/rtl8723ds
    2. Modify the Kconfig of rtl8723ds (---help--- to help).
    3. Modify /drivers/net/wireless/realtek/Kconfig and Makefile to include rtl8723ds.
    4. Enable WiFi (RTL8723DS) support in kernel config.
WiFi Device Tree Configuration
Add WiFi power configuration after reg_vcc3v3:
DTS
reg_vcc33_wifi: vcc33-wifi { 
    /* Always on 3.3V regulator for WiFi and BT */ 
    compatible = "regulator-fixed"; 
    regulator-name = "vcc33-wifi"; 
    regulator-min-microvolt = <3300000>; 
    regulator-max-microvolt = <3300000>; 
    regulator-always-on; 
    vin-supply = <&reg_vcc5v>; 
}; 

reg_vcc_wifi_io: vcc-wifi-io { 
    /* Always on 1.8V/300mA regulator for WiFi and BT IO */ 
    compatible = "regulator-fixed"; 
    regulator-name = "vcc-wifi-io"; 
    regulator-min-microvolt = <1800000>; 
    regulator-max-microvolt = <1800000>; 
    regulator-always-on; 
    vin-supply = <&reg_vcc33_wifi>; 
}; 

wifi_pwrseq: wifi-pwrseq { 
    compatible = "mmc-pwrseq-simple"; 
    clocks = <&rtc 1>; 
    clock-names = "osc32k-out"; 
    reset-gpios = <&pio 6 18 GPIO_ACTIVE_LOW>; /* PG18 */ 
    post-power-on-delay-ms = <200>; 
}; 
Add WiFi mmc1 configuration:
DTS
&mmc1 { 
    vmmc-supply = <&reg_vcc33_wifi>; 
    vqmmc-supply = <&reg_vcc_wifi_io>; 
    mmc-pwrseq = <&wifi_pwrseq>; 
    bus-width = <4>; 
    non-removable; 
    mmc-ddr-1_8v; 
    status = "okay"; 
}; 
Compile the device tree: make dtbs
Buildroot Configuration
Use Buildroot buildroot-2022.02.5. Configure system settings:
    • System configuration > Init system (BusyBox): /dev management (Dynamic using devtmpfs + mdev)
    • Target packages > Networking applications:
        ◦ [x] iperf3 // Network speed test
        ◦ [x] lftp // Network file transfer
        ◦ [x] openssh (Enable SFTP protocol / Remote ssh)
        ◦ [x] wpa_supplicant (Enable wpa_cli binary)
4. System Configuration
WiFi Configuration
Create/modify /etc/wpa_supplicant.conf:
Code snippet
ctrl_interface=/var/run/wpa_supplicant 
ap_scan=1 
network={ 
    ssid="your_wifi_ssid" 
    psk="your_wifi_password" 
}
Manually connect to WiFi:
Bash
ifconfig wlan0 up 
wpa_supplicant -Dnl80211 -iwlan0 -c/etc/wpa_supplicant.conf -B
udhcpc -i wlan0
Auto WiFi Connection on Startup
Create /etc/init.d/S45wifi:
Bash
#!/bin/sh
# wlan0 Starts WiFi.

start() {
    printf "Starting WiFi: "
    ifconfig wlan0 up
    wpa_supplicant -Dnl80211 -iwlan0 -c/etc/wpa_supplicant.conf -B
    udhcpc -i wlan0
    echo "OK"
}

stop() {
    printf "Stopping WiFi: "
    killall udhcpc
    killall wpa_supplicant
    echo "OK"
}

restart() {
    stop
    start
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart|reload)
        restart
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
esac
exit $?
Make it executable: chmod +x /etc/init.d/S45wifi
Test Network Speed (iperf3)
Bash
iperf3 -c 192.168.10.164
Configure SSH and Terminal
Modify /etc/ssh/sshd_config:
Plaintext
PermitRootLogin yes
AllowUsers root
Restart SSH: /etc/init.d/S50sshd restart Set password: passwd root
Configure Terminal (/etc/profile.d/myprofile.sh):
Bash
#!/bin/sh
PS1='[\u@\h]:\w$ '
export PS1
Make executable and load:
Bash
chmod +x /etc/profile.d/myprofile.sh
source /etc/profile.d/myprofile.sh
5. Make Boot Disk (TF Card)
Setup Image and Loop Devices
Bash
mkdir images && cd images
mkdir file

# Collect files
cp ../u-boot/u-boot-sunxi-with-spl.bin ./file
cp ../linux-5.19.0/arch/arm64/boot/Image ./file
cp ../linux-5.19.0/arch/arm64/boot/dts/allwinner/mq-quad.dtb ./file 
cp ../buildroot-2022.02.5/output/images/rootfs.tar ./file

# Make an empty img file
sudo dd if=/dev/zero of=./sdcard.img bs=1M count=512  

# Partition the image (Partition 1: 64MB Boot, Partition 2: ~428MB RootFS)
sudo fdisk ./sdcard.img 
# n -> p -> 1 -> 40960 -> +131072 
# n -> p -> 2 -> 172033 -> 1048575 
# w  

# Setup Loop Devices
sudo losetup -f ./sdcard.img 
sudo losetup -l # Check which loop device is associated (e.g., /dev/loop11)

# Write U-Boot
sudo dd if=./file/u-boot-sunxi-with-spl.bin of=/dev/loop11 bs=8K seek=1  

# Map Partitions (offsets = starting sector * 512, sizelimit = sectors * 512)
sudo losetup -f -o 20971520 --sizelimit 67109376 ./sdcard.img      # Boot partition (loop30)
sudo losetup -f -o 88080896 --sizelimit 448790016 ./sdcard.img     # RootFS partition (loop26)

# Format Partitions
sudo mkfs.fat /dev/loop30 
sudo mkfs.ext4 /dev/loop26 

# Mount Partitions
sudo mkdir -p /mnt/boot /mnt/rootfs
sudo mount /dev/loop30 /mnt/boot/ 
sudo mount /dev/loop26 /mnt/rootfs/  
Make U-Boot Boot Script
Create boot.cmd:
Plaintext
setenv bootargs console=ttyS0,115200 root=/dev/mmcblk0p2 rootfstype=ext4 rootwait rw 
setenv bootcmd fatload mmc 0:1 0x4fc00000 boot.scr; fatload mmc 0:1 0x40200000 Image; fatload mmc 0:1 0x4fa00000 mq-quad.dtb; booti 0x40200000 - 0x4fa00000 
Generate boot.scr:
Bash
mkimage -C none -A arm64 -T script -d boot.cmd boot.scr 
Populate Boot and RootFS
Bash
# Copy Boot Files
sudo cp ./file/Image /mnt/boot/ 
sudo cp ./file/mq-quad.dtb /mnt/boot/ 
sudo cp ./file/boot.scr /mnt/boot/  

# Extract RootFS
sudo tar -vxf ./file/rootfs.tar -C /mnt/rootfs/ 

# Install Kernel Modules
cd ../linux-5.19.0/ 
sudo make INSTALL_MOD_PATH=/mnt/rootfs/ modules_install  
Unmount and Write to TF Card
Bash
sync 
sudo umount /mnt/rootfs 
sudo umount /mnt/boot  

# Detach Loop Devices
sudo losetup -d /dev/loop30 
sudo losetup -d /dev/loop26 
sudo losetup -d /dev/loop11  

# Write Image to TF Card (replace /dev/sdd with your actual SD card block device)
sudo dd if=./sdcard.img of=/dev/sdd status=progress
6. System Boot Logs
U-Boot Boot Log
Code snippet
U-Boot SPL 2022.10-rc3-00069-g67fe8cc001-dirty (Sep 03 2022 - 18:10:41 +0800)
pmic id is 0x4b
DRAM: 1024 MiB
Trying to boot from MMC1
NOTICE:BL31: v2.7(debug):v2.7.0-312-g9a5dec669
...
INFO: BL31: Preparing for EL3 exit to normal world
INFO: Entry point address = 0x4a000000
INFO: SPSR = 0x3c9
INFO: Changed devicetree.

U-Boot 2022.10-rc3-00069-g67fe8cc001-dirty (Sep 03 2022 - 18:10:41 +0800) Allwinner Technology

CPU: Allwinner H616 (SUN50I)
Model: OrangePi Zero2
DRAM: 1 GiB
...
Loading Environment from FAT... OK
In: serial@5000000
Out: serial@5000000
Err: serial@5000000
Hit any key to stop autoboot: 0
35908096 bytes read in 1489 ms (23 MiB/s)
13222 bytes read in 2 ms (6.3 MiB/s)

Flattened Device Tree blob at 4fa00000
Booting using the fdt blob at 0x4fa00000
Loading Device Tree to 0000000049ff9000, end 0000000049fff3a5 ... OK
Starting kernel ...
Kernel Boot Log Extract
Code snippet
[ 0.000000] Booting Linux on physical CPU 0x0000000000 [0x410fd034]
[ 0.000000] Linux version 5.19.0-rc1-609706-gc465e81f8859-dirty (evler@evler-Standard-PC-i440FX-PIIX-1996)
[ 0.000000] Machine model: MQ-Quad_H616
...
[ 0.006798] SMP: Total of 4 processors activated.
[ 0.535716] EXT4-fs (mmcblk0p2): mounted filesystem with ordered data mode. Quota mode: none.
[ 0.535815] VFS: Mounted root (ext4 filesystem) on device 179:2.
...
[ 0.642146] Run /sbin/init as init process
Initializing random number generator: OK
Starting system message bus: done
Starting network: OK
Starting WiFi: Successfully initialized wpa_supplicant
udhcpc: started, v1.35.0
udhcpc: lease of 192.168.10.196 obtained from 192.168.10.251, lease time 43200
OK
Starting sshd: OK

Welcome to Buildroot
buildroot login: root
