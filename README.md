# Assignment3-a - Real-Time (RT) Programming with Linux (Kernel Build)
## By Vivek Tiwari

Use Raspberry PI Imager to install 64 - bit OS in SD Card. 
**Note: This will remove all the existing data, please take backup.**

Once Raspberry Pi Boots up, run the following commands:

```bash
sudo apt-get update
sudo apt-get upgrade
uname -a
```

You should see something like this:
```bash
Linux raspberrypi 6.1.0-rpi8-rpi-v8 #1 SMP PREEMPT Debian 1:6.1.73-1+rpt1 (2024- 01 - 25)
aarch64 GNU/Linux
```
Next, install the necessary packages and clone the Linux repository:
```bash
sudo apt install git bc bison flex libssl-dev make libncurses5-dev
mkdir kernel
cd kernel/
git clone --depth=1 --branch rpi-6.1.y https://github.com/raspberrypi/linux
```
Download the patch:
```bash
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.77-rt24.patch.gz
```
**Note: Please check for any error in patch (if you follow same steps you should not get any issue and patch should apply easily.**
check for any error 
```bash
zcat ../patch-6.1.77-rt24.patch.gz | patch -p1 --dry-run
```
Apply the patch
```bash
cd linux/
zcat ../patch-6.1.77-rt24.patch.gz | patch -p
```
Apply the default kernel configuration and Customize for PREEMPT_RT
```bash
KERNEL=kernel
make bcm2711_defconfig
```
Customized 
Navigate to General -> Preemption Model, Select Fully Preemptible Kernel (Real-Time)

```bash
make menuconfig
```
Save the .config

Build the kernel
**Note: Compilation will take approximately 1 hour.**
```bash
make -j4 Image.gz modules dtbs
```
**Install the kernel, modules, and Device Tree blobs**
```bash
sudo make modules_install
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/
sudo cp arch/arm64/boot/dts/overlays/.dtb /boot/overlays/
sudo cp arch/arm64/boot/dts/overlays/README /boot/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/kernel8.img
```
Also, copy the kernel to the Raspberry PI SD Card:
```bash
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/
sudo cp arch/arm64/boot/dts/overlays/.dtb /boot/firmware/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/firmware/kernel8.img
```
Finally, reboot the system:
```bash
sudo reboot
```
The final output must look like this: PREEMPT_RT
```bash
uname -a
```
Linux raspberrypi 6.1.77-rt24-v8+ #1 SMP PREEMPT_RT Sat Feb 10 19:25:18 PST 2024 aarch64 GNU/Linux





