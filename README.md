# sample-6.12.30-jh7110-generic-tftp-kernel
Basically a generic mainline kernel with ethernet and nftables, intended to be used on VisionFive 2 or Star64. Maybe it will work on Mars.

There are quite a few issues with this kernel, but it is a bunch of experiments on how I should get headless node usage over TFTP boot in which I cannot supply a /lib/modules. Many rebuilds were attempted.

Trisquel 22.04's stock cross compiler was used. It goes like this.
3x A76 cores @ 2.4GHz : Compiler
4x U74 cores @ 1.5GHz : Intended firmware receipent

$ /bin/bash
$ make ARCH=riscv CC="riscv64-linux-gnu-gcc -mcpu=sifive-u74 -mtune=sifive-7-series" LD=riscv64-linux-gnu-ld CROSS_COMPILE=riscv64-linux-gnu-
 (for defconfig, menuconfig, -j3, and dtbs_install install INSTALL_DTBS_PATH=benny INSTALL_PATH=benny)
 
I have explicitly specified the architecture for the compiler and for some reason, the LD as well despite not using a custom LD. And I have created a basic Alpine initramfs (uInitrd), vmlinuz for u-boot's booti, and dtb based on pine64_star64.dtb.

Here are some issues I have encountered.
1. I have known from my earliest vendor builds for other SBCs many years ago that if you have a custom defconfig with undocumented features by menuconfig, you cannot run menuconfig, or all those settings will be lost and your kernel comes out incomplete.
2. I used scripts/config quite often to enable settings, but on make, all those changes will be reset as... manually editing config or scripts/config -e doesn't track dependencies, and that leads to stuff you thought are being built-in to revert to modules if the master is a module.
3. Therefore I have to use menuconfig, which luckily works fine on mainline, unlike vendor kernels.
The kernel itself.
1. For some reason only one of the Ethernet ports work.
2. It's a barebones kernel, nftables and ethernet is built in, along with CPU support. No modules are used. A initramfs is still used, a small one. But most firewalls don't even work since the nftables is not loaded as a module or something? It just says protocol not supported. Therefore don't rely on it.
3. How do I get firewall support on a kernel without /lib/modules? A question indeed, since I built it into the kernel and it still doesn't seem to get recognized.

Trust me, I like those JH7110 boards of mine. They are so basic, and they are like pets. Always in need of maintainance, and yet so imperfect. But still a reliable computer, but as you can see, the state of the best mainlined RISC-V SoC (as in the most blocks are mainlined, not that it doesn't have many blocks to begin with) is still quite rough at best.
I know I am not very smart, for I purchased (preordered rather) a DeepComputing ROMA II Mainboard with a Eswin EIC7702X. Oh ho ho. Just imagine, that thing will probably be my laptop and yet live on the vendor's BSP for most of it's life. However, I guess I will release a OS image based on that BSP, but of my favorite stable distro --> Slackware.

A little update : It's possible that nftables just doesn't work to begin with on mainline 6.12. Or vendor 6.12.
