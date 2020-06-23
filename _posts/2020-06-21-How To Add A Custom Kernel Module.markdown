---
layout: post
title:  "How To Add A Custom Kernel Module In Nvidia Jetson Nano?"
date:   5/06/2020
categories: jekyll update
author: Gilson Varghese
background: '/img/kernel.jpg'
---
<p>Adding a Linux kernel module in nvidia jetson could be a little tricky. This article will cover the entire process of adding a custom kernel module and flashing jetson nano with the rootfs image. I will also describe the possible issues that might occur and how to solve them. The instructions are applicable for Jetson Nano module with jetpack 4.3 on Linux host.</p>

<h5>SDK Installation:</h5>
<p style="margin:0">The latest SDK manager can be downloaded from https://developer.nvidia.com/nvidia-sdk-manager.</p>
<p style="margin:0">I use SDK version 1.1.0. The following steps will install and run SDK manager in Ubuntu.</p>
<p style="margin:0">1) Open a Terminal. Keyboard shortcut is Ctrl+Alt+T</p>
<p style="margin:0">2) Change Directory to the folder where sdk is downloaded.</p>
<p style="margin:0">3) Run sudo apt install ./sdkmanager-[version].deb</p>
<p style="margin:0">4) Once installed, open SDK manager by running sdkmanager</p>

<p><h5>Creating Image and Obtaining Sources:</h5></p>
<p style="margin:0">Now the GUI will be up. Login with your developer account and SDK manager will provide with a form, which consists of 4 steps.</p>
<p style="margin:0">1) Development environment- Provide the nVidia board, Jetpack version etc.</p>
<p style="margin:0">2) Details and Licenses- Select the components you want to install and choose a destination folder. Jetson OS Image should be selected. Remember the target hardware image folder.</p>
<p style="margin:0">3) Setup Process- In this step all components will be downloaded and installed. You may skip flashing step if you don’e like to flash the board.</p>
<p style="margin:0">4) Final Summary- Summarises the components that were installed</p>

<center><img class="img-fluid" src="https://miro.medium.com/max/700/1*_S6ruGWcyxY6pmLXRiO9_g.jpeg" height="800" width="800" alt="Demo Image" align="middle"></center>
<span class="caption text-muted">Jetson Nano Dev Kit (Courtesy: NVidia).</span>

<p><h5>Customising Image:</h5></p>
<p style="margin:0">To customise kernel we need to download kernel source code.</p>
<p style="margin:0">1) Go to the HW image folder. And navigate to Linux_for_Tegra folder, which contails flash script, rootfs and kernel and bootloader images.</p>
<p style="margin:0">2) Open a terminal in the folder and run: ./source_sync.sh< Use tags provided in release notes. I used tag tegra-l4t-r32.3.1</p>
<p style="margin:0">3) Now you will get sources folder which contains source codes for kernel and boot-loader.</p>
<p style="margin:0">4) cd to kernel source folder, which is kernel4.9 in my case.</p>
<p style="margin:0">5) Add your custom module’s source code in the respective directory along with KConfig and MakeFile changes.</p>
<p style="margin:0">6) Add the module to tegra_defconfig which is present in arch/arm64/configs as a static or loadable module. You may do makeconfig also.</p>
<p style="margin:0">7) To compile kernel we need to setup environment variables: TEGRA_KERNEL_OUT=<Where should the output go>
<p style="margin:0">8) Install cross compiler. I used linaro. To install you just need to untar source into a directory, which we will use in the next step.</p>
<p style="margin:0">9) Run export CROSS_COMPILE=<cross_prefix>. If cross compiler is linaro, path will be <linaro_install_path>/bin/aarch64-linux-gnu- 
<p style="margin:0">10) Run export LOCALVERSION=-tegra</p>
<p style="margin:0">11) To configure linux with tegradef_config, run make ARCH=arm64 O=$TEGRA_KERNEL_OUT tegra_defconfig</p>
<p style="margin:0">12) Build the kernel using: make ARCH=arm64 O=$TEGRA_KERNEL_OUT -j3</p>
<p style="margin:0">13) Replace Linux_for_Tegra/kernel/Image with $TEGRA_KERNEL_OUT/arch/arm64/boot/Image</p>
<p style="margin:0">14) If you have any dts change, replace the contents of Linux_for_Tegra/kernel/dtb with $TEGRA_KERNEL_OUT/arch/arm64/boot/dts/</p>
<p style="margin:0">15) If you have built a dynamic module you shall add it to rootfs using: sudo make ARCH=arm64 O=$TEGRA_KERNEL_OUT modules_install INSTALL_MOD_PATH=<top>/Linux_for_Tegra/rootfs/

<p><h5>Flashing Image:</h5></p>
<p style="margin:0">1) To flash the image to jetson nano devkit run: sudo ./flash.sh jetson-nano-qspi-sd mmcblk0p1 .It will take around 15 minutes to flash the board.</p>
<p style="margin:0">2) Once flashed board will bootup and ask for user configuration like timezone, keyboard layout, username and password.</p>
<p style="margin:0">3) Provide the details and the configuration daemon will apply the configurations and reboot you to Ubuntu home.</p>
<center><img class="img-fluid" src="img-fluid" src="https://miro.medium.com/max/700/1*GXsM5VHFj4DpDszFqbN8Tw.png" height="800" width="800" alt="Demo Image" align="middle"></center>

<span class="caption text-muted">NVidia Ubuntu Home Screen</span>
Check if installed module is present using lsmod and dmesg.	
