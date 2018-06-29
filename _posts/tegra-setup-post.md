---
title: 'Setting up Tegra TX2 with Jetson 120'
date: 2018-04-10
permalink: /posts/2018/04/tegra-setup-post
tags:
  - Embedded Systems
  - Operating Systems
  - Tegra - Jetson
---

This post is about setting up the Tegra TX2 from [NVIDIA](https://devblogs.nvidia.com/jetson-tx2-delivers-twice-intelligence-edge/) with carrier board [Jetson 120](https://auvidea.com/product/70714/) equipped with an IMU.

Setup step by step :
---------------------------------------

1. Plug the Tegra to a monitor and to a Linux host machine, then do the following from the host machine

2. Go on : https://auvidea.com/firmware/

3. Download the latest firmware

4. Go on : https://developer.nvidia.com/embedded/jetpack

5. Download JetPack (be careful to download the version supported by the auvidea firmware)

6. Create a directory an move there the JetPack-L4T-3.1-linux-x64.run script

7. Run the JetPack-L4T-3.1-linux-x64.run script

8. Select the right model of the Tegra used (tx2 here) and download everything you need

9. Put the Tegra into recovery mode (press force recovery, click on reset and then release force recovery)

10. Press enter in the opened terminal to flash the Tegra (no need to worry about the IP address, just set the connexion as an ethernet one when prompted to do so). The Tegra should boot but you won't be able to use it as USB is not activated yet

11. Now you should have a lot of files and directories into the current directory and the most important one is the 64_TX2 which contains everything that is installed on the Tegra...

12. Now go into the auvidea firmware directory (ChangesTX2...) and copy the files into the BCT directory and paste them into 64_TX2/Linux_for_Tegra_tx2/bootloader/t186ref/BCT/

14. Put the Tegra back into recovery mode and go in 64_TX2/Linux_for_Tegra_tx2 from a terminal and run the command : 
```bash
sudo ./flash.sh jetson-tx2 mmcblk0p1
```

15. The Tegra should boot again and now USB should be enabled

16. Go back into the auvidea firmware directory (ChangesTX2...) and copy the .dtb files into the dtb directory and paste them into 64_TX2/Linux_for_Tegra_tx2/kernel/dtb/

17. Once again put the Tegra into recovery mode and from a terminal run :
```bash
sudo ./flash.sh -r -k kernel-dtb jetson-tx2 mmcblk0p1
# this will not reflash the tegra but it will update the kernel
```

18. Now copy the content of the kernel directory (Image, zImage, lib) into the auvidea firmware directory onto a USB drive

19. On the Tegra log on the nvidia account (password : nvidia) and plug the USB drive

20. Still on the Tegra, go into the directory /lib and paste the directory /modules from the USB drive

21. Still on the Tegra, go into the directory /boot and paste the Image and zImage files from the USB drive

22. Reboot the Tegra : 
```bash
sudo reboot
```

23. The SPI should now be seen : 
```bash
ll /dev/spi*
```

  1. To access the IMU of the jetson 120 without being root you need to autorize accessing the right /dev/spidevX.X :
```bash
sudo chown nvidia /dev/spidevX.Y
# (X=1 & Y=0 on the TX2 and X=3 & Y=0 on the TX1)
# This will only be effective on this session but not after reboot
```

  2. To have permanent non-root access to IMU :

  * Create a file : 
  ```bash
  sudo gedit /etc/udev/rules.d/50-spi.rules
  ```
  * Write in the file : 
  ```bash
  SUBSYTEM=="spidev", GROUP="spiuser", MODE="0660"
  ```
  * Reboot



Bonus using a NVME SSD with the Tegra :
---------------------------------------

0. In a terminal : sudo apt-get install gparted

1. In a terminal : sudo gparted

2. Select the NVME disk (right-top corner)

3. Create a ext4 partition

4. Once done you should be able to access the SSD

5. If you want the SSD to be mounted automatically with a specific name : sudo gnome-disks

6. Right click on the nvme and select : mount-options to set everything
```bash
# This allows you to use the SSD as a storage drive but the Tegra will still boot on eMMC
```
