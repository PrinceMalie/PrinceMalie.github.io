# Arch Linux Installation 

[Arch Wiki](https://wiki.archlinux.org/title/installation_guide) was the resource used to ensure the completion of this project. 

## Arch Linux Iso

### Download Arch Linux Iso

- Before completeing any other steps, one must download the Arch Linux iso. 

- I highly suggest downloading the iso from [MIT](http://mirrors.mit.edu/archlinux/iso/2022.11.01/). If you would like to explore other download options, the following link can be used to access the download page: https://archlinux.org/download/

- Be sure to download the iso that matches the hardware settings of your device. More than likely, you will need to download the x86_64.iso. 

### Verify Arch Linux Iso
- After downloading the iso, you must verify whether or not you downloaded malicious software. 

* The SHA256 will be used the verfiy the validity of the iso.
    * On the [download page](https://archlinux.org/download/), find the area labeled Checksums. 

    * Under the bullet point list, take note of the SHA256 number. This number will be used as a reference to confirm the validation of the iso. 

    * The SHA256 of the downloaded iso must be compared to the recorded SHA256. 

        * I used a program called 7-Zip to receive the SHA256. Please follow the steps listed below to get the SHA256:

            1. Download [7-Zip](https://www.7-zip.org/).

            2. Locate the iso on your file system and right click on the iso. 

            3. Click CRC SHA and then clcik SHA-256. 

            4. After a very small wait, 7-Zip will reutrn the iso's SHA256.

            5. Verify that the two SHA256 numbers match. 

## VM Setup

### VM Configuration
- Create new VM in [Workstation (Windows)](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html) or [Fusion (macOS)](https://www.vmware.com/products/fusion/fusion-evaluation.html).

- Select the iso.

- Give the VM 2 GB of Ram (the default 768 MB was problematic).

- Give the hard disk 20 GB of space to be safe (thin provisioned by default).

- Assign the guest operating system to be Linux. 

- Assign the version to be Other Linux 5.x kernel 64-bit. 

- Assign the VM two processors (Number of processors = 1 & Number of cores per processor = 2)

- Assign the network adpater to NAT. 

### UEFI Mode

- After configuring the VM, you must change the VM to UEFI mode. To change the VM to UEFI mode, following the steps listed below: 

    1. Right click on Open VM directory 

    2. Search for the named Arch Linux.vmx

    3. Open the file with Notepad or Visual Studio Code

    4. Insert the following as line 2: firmware=“efi”

## Boot Mode

1. Run the VM and when the screen loads, select Arch Linux install medium.

2. Once you are logged in the virtual console as the root user, run the following command to verify that the boot mode is UEFI:

<center>

 ``` 
ls /sys/firmware/efi/efivars
 ```
</center>

If the commmand shows the directory without error, then the system is booted in UEFI mode. If the directory does not exist, the system may be booted in BIOS mode. 


## Keyboard Configuration

The default console keymap is US. However, you might have another keyboard layout. To change the default keymap, complete the following steps: 

1. Run the following command to see the list of available layouts:

<center>

```
ls /usr/share/kbd/keymaps/**/*.map.gz
```
</center>

2. After determining the keyboard layout you need, run the command below:

<center>

``` 
loadkeys de-latin1
```
</center>

Note: de-latin1 is the German keyboard layout. You can use de-latin1 as an example of what the command is for your desired keyboard layout.

## Internet Connection  

The next step involves ensuring that the virtual environment has access to the internet. To complete this task, please follow the steps listed below: 

1. Run the command below to ensure your network interface is listed and enabled:

<center>

```
ip link 
```
</center>

2. Verify the connection by executing the following command: 

<center>

```
ping archlinux.org
```
</center>
If you receive a response, that means that it is working. 

## System Clock Update

Use the command below to ensure the system clock is accurate:

<center>

```
timedatectl status
```
</center>

## Disk Partition

To partition the disk, please follow the set of instructions listed below:

1. Use the command to see a list of all the available block devices that the disk(s) are assigned to: 

<center>

```
fdisk -l 
```

</center>

You will see a disk that has 20 GB assigned to it as well as a disk that has around 687 MB. The 20 GB disk represents the virtual enviroment's hard drive; that is the disk you want to partition. The other disk is referencing the downloaded iso. 

2. The command below must be used to partition the virtual environment's hard drive:

<center>

```
fdisk /dev/the_disk_to_be_partitioned
```
</center>

Remember, the disk you want to partition is the one that has 20 GB assigned to it. More than likely, it will be named /dev/sda. 

3. Once you select /dev/sda, you will be able to partition the disk. Before going any further, type m, so you can see the relevant commands. Some of the most important commands include: n (create new partition), p (verify that the desired partition is listed under the partition table), and w (write the partition table to disk and exit). DO NOT type command q becuase your work will not be saved.

4. Two partitions must be created. The first partition will be assigned 500 MB, while the second will be asssigned the remainder of the hard drive. To create the two partitions, use the command n. 

    * For the first partition, press enter to keep the default settings for the partition until you reach the line labeled last sector. On that line, type +500M. After doing so, the first partition will be created. 

    * For the second partition, press enter to keep the default settings for every single line. After the completion of that step, the second partition will be created.

Note: I accidentally assigned one partition to 100% of hard drive space on my first try. If this happens to you, use the command d to delete a partition, so that you can repartition your disk(s). 

5. Verify that your partitions are correct by using the command p. After verification, use the command w to write the partition table to disk and exit. 

## Format Partition
After creating the partitions, we now must format them. The steps to format the newly created partitions are as follows: 

1. Type the following command to see the list of partitions on your device (You should have two partitions.):

<center>

```
fdisk -l
```
</center>

2. For the partition with the larger storage size, run the following command: 

<center>

```
mkfs.ext4 /dev/root_partition
```
</center>

The root partition is the bigger partition. In my case, it was dev/sda2, but it could be dev/sda1. Again, it just depends on which one takes up more storage. 

3. For the remaining partition, run the command listed below:
<center>

```
mkfs.fat -F 32 /dev/efi_system_partition
```
</center>

## Mount the File Systems
To mount the file systems, please do the following:

1. Run this command to mount the root partition:

<center>

```
mount /dev/root_partition /mnt
```
</center>


2. Run this command to mount the EFI system partition:

<center>

```
mount --mkdir /dev/efi_system_partition /mnt/boot
```
</center>

## Package Installation

Run this command and let the magic happen:
<center>

```
pacstrap -K /mnt base linux linux-firmware
```
</center>

## System Configuration

1. Generate an fstab file using the command below: 

<center>

```
genfstab -U /mnt >> /mnt/etc/fstab
```
</center>

2. Change root into the new system by executing the code below: 

<center>

```
arch-chroot /mnt
```
</center>

3. Install other essential packages. The base package does not includes all the packages and tools you need. Please install the following pacakges using the command listed below:
    * Packages to install:
        1. nano
        2. man-db
        3. man-pages
        4. texinfo
        5. vim
        6. dhcpcd
        7. sof-firmware
        8. lvm2
        9. ntfs-3g


<center>

```
pacman -S [name of package]
```
</center>

## Time Zone

1. Set the time zone using the command below: 

<center>

```
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
</center>

Be sure to replace the region and city with the appropiate value representing where you currently reside. 

2. Run hwclock to generate /etc/adjtime:

<center>

```
hwclock --systohc
```
</center>

## Localization
1. Edit /etc/locale.gen by running the command below and uncommenting en_US.UTF-8 UTF-8 in:

<center>

```
nano /etc/locale.gen
```
</center>

2. Create the locale.conf file, and set the LANG variable by taking the steps down below: 

<center>

```
echo LANG=en_US.UTF-8 > /etc/locale.conf
```
</center>

3. If you set the console keyboard layout, make the changes persistent in vconsole.conf:

<center>

```
echo KEYMAP=de-latin1 > /etc/vconsole.conf
```
</center>

Note: I did not run this command because I have the default US keyboard, but the command must be used if you have a different keyboard.

## Network Configuration

1. Create the hostname file and add myhostname as the hostname. 

<center>

```
echo "myhostname" > /etc/hostname
```

</center>

2. Edit /etc/hosts using nano by adding the following text:
exi
```
127.0.0.1 localhost
::1 localhost
127.0.1.1 [yourhostname].localdomain [yourhostname]
```

If the system has a permanent IP address, it should be used instead of 127.0.1.1.

## Initramfs

Run this line of code: 

<center>

```
mkinitcpio -P
```

</center>

## Root Password

Set the root password by executing this commannd:

<center>

```
passwd
```

</center>

## Boot Loader

1. Install the packages grub and efibootmgr

<center>

```
pacman -S grub
pacman -S efibootmgr
```

</center>


2. Mount the EFI system parititon and in the remainder of this section, substititue esp with it mount point. 

<center>

```
mount --mkdir /dev/efi_system_partition /boot
```
</center>

3. Install Grub using the command below: 

<center>

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
</center>

4. After installing Grub, microcodes must be enabled. To acquire updated microcodes, depending on the processor, install one of the following packages:

<center>

```
pacman -S amd-ucode (for AMD processors)

OR

pacman -S intel-ucode (for Intel processors)
```

</center>

After installing the software for updating microcodes, run the command below:

<center>

```
grub-mkconfig -o /boot/grub/grub.cfg
```
</center>

5. Exit chroot and reboot the Arch Linux VM. When asked to login, type root for the login username and type the password you set earlier for the password entry. 

## Desktop Environment Installation

1. Install LXDE by running the command below: 

<center>

```
pacman -S lxde
```
</center>


## Create User Accounts with Sudo Permissions

1. Type dhcpcd into the command line.

2. Install sudo and vi editor by running the following commands:

<center>

```
pacman --sync sudo
pacman --sync vi
```
</center>

3. Add a user also state to make its home directory by option --create-home

<center>

```
useradd --create-home [username]
```
</center>

4. Assign a password to the user:

<center>

```
passwd [username]
```
</center>

5. Wheel group is the sudo group in Arch Linux. So at this point add newly created user 'john' to the wheel group with the help of command usermod.

<center>

```
usermod -aG wheel [username]
```

</center>

6. Now edit the file sudoers so that the wheel group is activated. To do this, open the sudoer's file and uncomment the line  %wheel ALL=(ALL) ALL. This can be accomplished by running the code below: 

<center>

```
EDITOR=nano visudo
```
</center>

7. Verify that the user has sudo permissions by running the code below. The first line switches the user from root to the user you created. The second line will reutrn the user id, group id, and groups (make sure you see 998(wheel)). The third line should return root. If all of that happens, the user account has sudo permissions. 

<center>

```
su - [username]
id
sudo whoami
```
</center>

8. Create a user named "codi" with password GraceHopper1906. Repeat the process listed above. 

## Install Zsh

1. Install zsh shell by running the command below: 

<center>

```
pacman -S zsh
```
</center>

## Install SSH and SHH into Class Gateway

1. Install openssh with the command below:

<center>

```
pacman -S openssh
```
</center>

2. SSH into gateway using the following command:

<center>

```
ssh user@server-address
```
</center>

## Install Color Coding 

I used [this](https://computingforgeeks.com/how-to-install-and-configure-zsh-shell-on-linux/) guide as a reference for installing and configuring Zsh on Linux. 

1. Change to zsh shell by executing the command below:

<center>

```
zsh
```
</center>

2. Change zsh to be your default shell by using the code below:

<center>

```
chsh -s /bin/zsh
```
</center>

3. Before installing Oh My ZSH, tthe prerequisites must be installed (wget, curl, git):

<center>

```
pacman -S wget
pacman -S curl
pacman -S git
```
</center>

4. Install zsh

<center>

```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

</center>


5. Clone this repo

<center>

```
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```
</center>

6. Symlink spaceship.zsh-theme to your oh-my-zsh custom themes directory:

<center>

```
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```
</center>

7. Set ZSH_THEME = "spaceship" in your .zshrc. 

8.  The symlink was created with an absolute path. Once your username folder is changed, the symlink is broken. Re-doing the symlink again is enough to fix it.

<center>

```
ln -sf "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```
</center>

9. Set the ZSH_THEME to be spaceship and rouce the .zhhrc after.

<center>

```
vi, ~/.zshrc
ZSH_THEME = "spaceship"
source ~/.zshrc
```
</center>

10. Configure help command by using nano to edit the .zshrc. Adding the following to .zshrc, and after source the file: 

<center>

```
autoload -U run-help
autoload run-help-git
autoload run-help-svn
autoload run-help-svk
alias help=run-help
```
</center>

11. Clean code the plugins folder.

<center>

```
cd ~/.oh-my-zsh/plugins 
git clone https://github.com/zsh-userszsh-syntax-highlighting.git 
```
</center>

12. Edit the .zshrc file by adding the information listed below to the end of the file. After, source the .zshrc file. 

<center>

```
vim ~/.zshrc

source ~/.oh-my-zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

source ~/.zshrc

```

</center>

13. Edit the .zshrc file by adding the information listed below to the end of the file. After, source the .zshrc file. 

<center>

```
vim ~/.zshrc

zstyle ':completion:*' rehash true

source ~/.zshrc

```
</center>

14. Clone zsh completions using code below:

<center>

```
git clone https://github.com/zsh-users/zsh-completions ~/.oh-my-zsh/custom/plugins/zsh-completions
```

</center>

15. Enable zsh completions by editing the .zshrc file by addding the following to the file:

<center>

```
vim ~/.zshrc
plugins+=(zsh-completions)
autoload -U compinit && compinit
```
</center>

16. Install zsh tab completion system using the code below:

<center>

```
pacman -S zsh-completions
```

</center>

## Boot into GUI

1. Install xorg-xinit:

<center>

```
pacman -S xorg-xinit
```

</center>

2. Run the following code

<center>

```
systemctl enable lxdm
```
</center>

3. Reboot and you will see the lxde gui.
</center>

## Aliases

Click [this](https://www.cyberciti.biz/tips/bash-aliases-mac-centos-linux-unix.html) link and choose a few aliases to create. The aliases I chose are as follows: 

1. Show hidden files

<center>

```
alias l.='ls -d .* --color=auto'
```

</center>

2. Generate sha1 digest

<center>

```
alias shal='oppenssl shal'
```

</center>

3. Create parent directories on demand.

<center>

```
alias mkdir='mkdir -pv'
```

</center>

4. To make all the aliases permanent, add them to the .zshrc file, and reboot the system. 


