This kernel build was:

OS: Debian 11

Recomended OS build: Debian/Ubuntu or derivates like Parrot OS

Commands:
```shell
# Download build dependences
apt install -y git subversion bc bison flex libssl-dev make libc6-dev libncurses5-dev crossbuild-essential-arm64 # <https://www.raspberrypi.com/documentation/computers/linux_kernel.html>

# Download kernel
git clone -b rpi-5.10.y https://github.com/raspberrypi/linux.git

# Subversion boot
svn export https://github.com/raspberrypi/firmware/trunk/boot # boot firmware
rm boot/kernel*
rm boot/*.dtb
rm boot/overlays/*.dtbo

# Build kernel
cd linux
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcmrpi3_defconfig
ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- make -j4 \
    CXXFLAGS="-march=armv8-a+crypto+crc -mtune=cortex-a53" \
    CFLAGS="-march=armv8-a+crypto+crc -mtune=cortex-a53" \
    bindeb-pkg
cp arch/arm64/boot/Image ../boot/kernel8.img
cp arch/arm64/boot/dts/overlays/*.dtbo ../boot/overlays/
cp arch/arm64/boot/dts/broadcom/*.dtb ../boot/
cd ..

# Out kernel
mkdir out
cp -r linux-* out
cp -r boot out

# Delete folders (optional)
rm -rf linux
rm -rf boot

# Kernel installation (optional this is only builder)
mkdir /tmp/kernel
cd /tmp/kernel
# Copy output folder here
sudo cp -r output/* . # copy all files from output folder to /tmp/kernel
apt purge linux-headers-* linux-image-* # remove others kernels
sudo rm -rf /boot/* # remove others boot config
#install kernel
sudo dpkg -i linux-headers-*.deb linux-image-*.deb linux-libc-*.deb
cp -r boot/* /boot
cp configurations/* /boot/*
reboot # (optional)
```