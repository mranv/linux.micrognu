# Simple GUI-Based Linux Distro Creation Guide

This guide will walk you through creating a simple graphical Linux distribution based on BusyBox and Nano-X, adapted to run on QEMU.

## Prerequisites

Ensure you have a Debian-based system and install the necessary dependencies:

```sh
sudo apt update && sudo apt install wget bzip2 libncurses-dev flex bison bc libelf-dev libssl-dev xz-utils autoconf gcc make libtool git vim libpng-dev libfreetype-dev g++ extlinux nano
```

## Step-by-Step Instructions

### 1. Download and Prepare the Linux Kernel

1. **Download the Linux Kernel:**

   ```sh
   wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.9.4.tar.xz
   ```

2. **Extract the Kernel:**

   ```sh
   tar xf linux-6.9.4.tar.xz
   cd linux-6.9.4
   ```

3. **Configure the Kernel:**

   ```sh
   make menuconfig
   ```

4. **Compile the Kernel:**

   ```sh
   make -j 4
   ```

5. **Create a Directory for the Distro:**
   ```sh
   mkdir /distro
   cp arch/x86/boot/bzImage /distro/
   ```

### 2. Set Up BusyBox

1. **Download BusyBox:**

   ```sh
   cd ~
   wget https://busybox.net/downloads/busybox-1.36.1.tar.bz2
   ```

2. **Extract BusyBox:**

   ```sh
   tar xf busybox-1.36.1.tar.bz2
   cd busybox-1.36.1
   ```

3. **Configure BusyBox:**

   ```sh
   make menuconfig
   ```

4. **Compile and Install BusyBox:**
   ```sh
   make -j 4
   make CONFIG_PREFIX=/distro install
   ```

### 3. Set Up MicroWindows

1. **Clone MicroWindows:**

   ```sh
   git clone https://github.com/ghaerr/microwindows
   cd microwindows/src/
   ```

2. **Configure MicroWindows:**

   ```sh
   cp Configs/config.linux-fb config
   nano config
   ```

3. **Compile MicroWindows:**

   ```sh
   make -j 4
   make install
   make x11-demo
   ```

4. **Set Up a Sample GUI Application:**

   ```sh
   mkdir x11-demo
   cd x11-demo/
   nano gui.c
   ```

5. **Compile the GUI Application:**
   ```sh
   cd ..
   mv x11-demo /distro/
   cd /distro/x11-demo/
   gcc gui.c -lNX11 -lnano-X -I /microwindows/src/nx11/X11-local/
   mv a.out /distro/nirs-test-app
   ```

### 4. Prepare the Distro Environment

1. **Create Necessary Directories:**

   ```sh
   mkdir -p /distro/lib/x86_64-linux-gnu/
   mkdir /distro/lib64
   ```

2. **Copy Required Libraries:**

   ```sh
   cp /lib/x86_64-linux-gnu/libpng16.so.16 /distro/lib/x86_64-linux-gnu/libpng16.so.16
   cp /lib/x86_64-linux-gnu/libz.so.1 /distro/lib/x86_64-linux-gnu/libz.so.1
   cp /lib/x86_64-linux-gnu/libfreetype.so.6 /distro/lib/x86_64-linux-gnu/libfreetype.so.6
   cp /lib/x86_64-linux-gnu/libc.so.6 /distro/lib/x86_64-linux-gnu/libc.so.6
   cp /lib/x86_64-linux-gnu/libm.so.6 /distro/lib/x86_64-linux-gnu/libm.so.6
   cp /lib/x86_64-linux-gnu/libbrotlidec.so.1 /distro/lib/x86_64-linux-gnu/libbrotlidec.so.1
   cp /lib64/ld-linux-x86-64.so.2 /distro/lib64/ld-linux-x86-64.so.2
   cp /lib/x86_64-linux-gnu/libbrotlicommon.so.1 /distro/lib/x86_64-linux-gnu/libbrotlicommon.so.1
   ```

3. **Copy Nano-X Binaries:**
   ```sh
   cp -r /microwindows/src/bin /distro/nanox
   cp /microwindows/src/runapp /distro/nanox/
   ```

### 5. Create the Bootable Image

1. **Create and Format the Image:**

   ```sh
   cd /distro/
   truncate -s 200MB boot.img
   mkfs boot.img
   mkdir mnt
   mount boot.img mnt
   ```

2. **Install Extlinux:**

   ```sh
   extlinux -i mnt/
   ```

3. **Copy Files to the Image:**

   ```sh
   mv bin bzImage lib lib64 linuxrc nanox nirs-test-app sbin usr mnt
   ```

4. **Create Additional Directories:**

   ```sh
   cd mnt/
   mkdir var etc root dev tmp proc
   ```

5. **Unmount the Image:**
   ```sh
   cd ..
   umount mnt
   ```

### Links

- [Linux Kernel](https://www.kernel.org/)
- [BusyBox](https://busybox.net/)
- [MicroWindows](https://github.com/ghaerr/microwindows)
- [X11 Hello](https://gist.github.com/nir9/098d83c7...)

With these steps, you will have created a simple graphical Linux distribution ready to run on QEMU.

<!-- https://www.youtube.com/watch?v=guSDz5Iwgw0&t=3s -->
