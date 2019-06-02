## Arch Linux Installation

1. #### Disk Partition

   ```
   lsblk //list available disks and device's name such as sda or sdb 
   fdisk /dev/sda //o for fat dos format g for gpt, always can use m for help menu
   n //for new partition
   p //for primary
   +300M //for sda1 as boot partition where grub and boot info are located
   +4G //for sda2 as swap partition
   rest for sda3 //main partition where everything else will be located
   mkfs.ext4 /dev/sda1
   set 1 boot on //not sure if this is necessary
   mkswap /dev/sda2
   swapon /dev/sda2
   mkfs.ext4 /dev/sda3
   ```

2. #### Mount Partition

   (If there is not ethernet available, read wifi connection instruction )

   ```
   mount /dev/sda3 /mnt
   mkdir /mnt/boot
   mount /dev/sda1 /mnt/boot
   ```

3. #### WiFi Enable

   ```
   rfkill
   rfkill unblock all
   iplink set wls1 up
   nano /etc/wpa_supplicant/wifi.conf 
   	//network={ssid="Atsw"
   			psk="Ac8964For"}
   	//
   wpa_supplicant -BDwext -i wls1 -c etc/wpa_supplicant/wifi.conf
   dhclient wls1
   ```

   

4. #### Install Arch Linux

   ```
   pacstrap /mnt base base-devel vim intel-ucode
   genfstab -U /mnt >> /mnt/etc/fstab //use cat /mnt/etc/fstab to ensure fstab existence
   ```

5. #### Basic Setting For Arch Linux

   ```
   arch-chroot /mnt
   vim /etc/locale.gen //uncomment en_US.UTF8
   locale-gen
   echo LANG=en_US.UTF8 > /etc/locale.conf //assure locale.conf existence
   export LANG=en_US.UTF8 
   ln -sf /usr/share/zoneinfo/America/Toronto /etc/localtime
   hwclock --systohc --utc
   echo OW > /etc/hostname
   vim /etc/pacamn.conf 
   	//uncomment
   		[multilib]
   		Include = /etc/pacman.d/mirrorlist
   	//create at the bottom
   		[archliuxfr]
   		SigLevel = Never
   		Server = http://repo.archlinux.fr/$arch//
   pacman -Syy //update source
   passwd //creates password for root
   useradd -m -g users -G wheel,storage,power -s /bin/bash q //add user q 
   passwd q //create password for q
   EDITOR=vim visudo
   	//add
   		//under #user privilege
   		q ALL=(ALL) ALL//
   	//uncomment 
   		%wheel ALL=(ALL) ALL
   		Defaults rootpw //add this line
   		%wheel no password when doing the same//
   pacman -S bash-completion
   ```

6. #### Install Bootloader

   ```
   pacman -S grub
   grub-install --target=i386-pc /dev/sda
   grub-mkconfig -o /boot/grub/grub.cfg
   vim /boot/grub/grub.cfg
   	//change timeout = 5 to timeout =2
   ```

7. #### Unmount & Reboot

   ```
   exit
   umount -R /mnt
   reboot
   ```

8. #### Internet Connection with cable

   ```
   sudo ip link set enp0s25 up
   sudo dhcpcd@enp0s25
   ```

9. #### Install Driver and Dependencies 

   ```
   pacman -S xorg-server xorg-xinit xorg-apps xorg-twm xorg-xclock xterm xf86-video-intel feh compton udisks2 udiskie fcitx(or ibus) fcitx-googlepinyin(or ibus-googlepinyin) i3-gaps rxvt-unicode firefox networkmanager network-manager-applet gnome-keyring variety git nautilus tlp tlp-rdw tp_smapi acpi acpi_call i3blocks wqy-microhei rofi pulseaudio pulseaudio-alsa alsa-utils pulseaudio-bluetooth bluez bluez-libs bluez-utils vlc
   
   git clone https://aur.archlinux.org/yay.git
   cd yay
   makepkg -si
   yay -Syu
   
   yay -S dropbox google-chrome typora wd719x-firmware aic94xx-firmware
   reboot
   ```

   ```
   startx
   nautilus
   ```

10. #### Copy Configuration Files

  From usb Drive to ~/

  ```
  1. .vim 
  2. .config
  4. .bashrc
  5. .bash_profile
  6. .Xdefaults
  7. .xinitrc
  8. .vimrc
  ```

11. #### WiFi Enable

    ```
    sudo systemctl disable dhcpcd@enp0s25.service
    sudo systemctl disable netctl-auto@wls1.service
    sudo systemctl enable NetworkManager.service
    reboot
    ```

    #### 

