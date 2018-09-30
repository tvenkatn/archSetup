### Live USB
To create a live arch USB, go to an ubuntu machine, do fdisk -l to get the usb name (say, /dev/sdb)
now do 
dd bs=4M if=/path/to/archlinux.iso of=/dev/sdb status=progress oflag=sync
 :) that's all it takes!! dd is awesome
 
#### EFI
 Follow this for EFI
    https://arcolinuxd.com/6-the-actual-installation-of-arch-linux-phase-2



> Scrolling in arch terminal could be done using Shift+PageUp 

* https://linoxide.com/distros/beginners-arch-linux-installation-guide/
a. Verify boot mode: MBR/EFI.
    ls /sys/firmware/efi/efivars
        No result implies MBR mode. Else, EFI mode.
b. Verify network connection
    ping www.google.com
c. Configure system time
    timedatectl status
d. Prepare disk:
    a. fdisk -l gives (not 1, L) partitions table
    b. cfdisk /dev/sda. Select Dos and create boot, swap and main partitions
    c. More on partitions: https://www.ostechnix.com/install-arch-linux-latest-version/
    d. partitions can be checked using lsblk

Alternatively
        fdisk /dev/sda
            n for new partitions
            p for primary, e for extended
            o for erase all existing
            w for save and exit
            +nG for n GB of partition size
            
    
e. Format partitions:
    For boot, swap, home partitions (/dev/sda1, /dev/sda2, /dev/sda3, /dev/sda5)
    Format using
        mkfs.ext4 /dev/sda1
        mkfs.ext4 /dev/sda5
f. mkswap /dev/sda2
    swapon /dev/sda2
g. Install Arch base system
   mount /dev/sda1 /mnt
   mkdir /mnt/home
   mount /dev/sda5 /mnt/home
   pacstrap /mnt base base-devel
h. Generate and check fstab
    genfstab /mnt >> /mnt/etc/fstab
    cat /mnt/etc/fstab
    Note: The fstab file is used to control what file systems are mounted automatically when the system boots so that the system will be able to boot into Arch Linux
i. Set timezone
    ls /usr/share/zoneinfo : shows available
    ln -sf /usr/share/zoneinfo/US/Pacific /etc/localtime : sets 
    hwclock --systohc --utc
    
    ^^ above command sets the hardware clock to the same timezone
j. Configure system language using locale 
    vi /etc/locale.gen
    then uncomment  en_US.UTF-8 UTF-8
    save and exit
    then use command locale-gen
    set LANG variable in /etc/locale.conf 
        echo LANG=en_US.UTF-8 > /etc/locale.conf
k. Configure hostname. It is in /etc/hostname
    echo sriArch1Desk > /etc/hostname
    also add this to vi /etc/hosts
        (just add a line at the bottom and type in the above username)
l. Create a new user and change root password
    useradd -m -G wheel,users -s /bin/bash srinivas
    passwd (<- this helps changing root password)
    passwd srinivas (<- this changes user's password)
    chage -d 0 srinivas ( <- prompts the user to change password at first log in)
     
m. ADD the new used to sudoers list. Use visudo command.
    visudo requires sudo and vim. so, first, 
    pacman -S vim sudo
    and then
        visudo
    then
        root ALL=(ALL) ALL
        srinivas ALL=(ALL) ALL
n. Install bootloader and os-prober
    grub is boot manager
    os-prober detects if there are other operating systems installed on the system
    pacman -S grub os-prober
    
    Now install grub on the disk
        grub-install /dev/sda
        grub-mkconfig -o /boot/grub/grub.cfg
    
o. exit
    unmount -R /mnt
    reboot
        
p. Configure internet
    https://wiki.archlinux.org/index.php/Network_configuration
    lspci -v shows all adapters
    ip address show
        and look for ethernet adapter (en*) or wireless (wl) name
    sudo ip link set adapterNameHere up
    sudo dhcpcd adapterNameHere
    
    now ping 8.8.8.8 will give results!
    
q. **Window Management**
    sudo pacman -Syu
    sudo pacman -S xorg xorg-server
    sudo pacman -S xorg-xinit
    sudo pacman -S git vim
    sudo pacman -S i3-wm
    sudo pacman -S rxvt-unicode
        ^^ lightweight terminal for i3
        type in urxvt
    Now, once you have .i3 installed, just go to your ~/.xinitrc and add:
        exec i3
        
    Get back to terminal and type 
        startx
        
        i3
        * $mod + return: opens a new terminal
        * $mod + d: dmenu, minimal launcher, defaults with i3.
        * $mod + f: toggle fullscreen
        * $mod + shift + q: kills app
        * $mod + number: changes virtual desktop
    
    Issues: https://unix.stackexchange.com/questions/251456/cant-exit-i3-because-no-sensible-terminal-emulator-is-installed
    
____

sudo pacman -S wget curl htop
File browser!
sudo pacman -S ranger
    ^^ this installs latests python3
    (https://ranger.github.io/documentation.html)

**i3 status**
for i3, status: sudo pacman -S i3status
    sudo pacman -S dmenu 

sudo pacman -S qutebrowser

sudo pacman -S i3lock
    Gives a white screen. Just start typing password to log back in. 
    
### R
    sudo pacman -S gcc-fortran
    sudo pacman -S r
    sudo pacman -S tk
        ^^ without this above, packages can not be installed. It's a dependency for R

### PACKAGE MANAGER:
yay
https://www.ostechnix.com/yay-found-yet-another-reliable-aur-helper/
    sudo pacman -S git
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -si
**feh**
    Image manager/viewer. Desktop background
    sudo pacman -S feh
    
Guide for arch i3 and bars
    https://www.devpy.me/your-guide-to-a-practical-linux-desktop-with-i3wm/

FIRST in i3, go to i3 config, (~/.i3/config)
find what Mod+return is using. If it is i3-sensible-terminal, then change it to 
your favorite terminal emulator (termite)! Else, i3 will look UGLY!

in ~/.config create a termite directory and in that create a file called  config
edit and add all config lines to it!

____

sudo pacman -S code (visual studio code)

____

**Display scaling**

https://www.reddit.com/r/i3wm/comments/5atii8/arch_i3gaps_1440p_monitor_small_font/

xrandr --current
xrandr --output HDMI-2 --mode 1920x1080

__

How to calculate screen dpi!
https://winaero.com/blog/find-change-screen-dpi-linux/

__

**SSH**

sudo pacman -S openssh
ssh-keygen
(enter, enter, enter)
__
sudo pacman -S scala maven
    scala would need Java as well!
    
__

**Rofi**
https://github.com/DaveDavenport/rofi/issues/758

in i3, replace exec dmenu with 
    rofi -show run -config ~/.config/rofi/config
    
^^ to create a sample config, do rofi -dump-config > ~/.config/rofi/config
To select rofi theme, type in terminal: rofi-theme-selector
    and follow further instructions on the screen!
__


### Multiple GITHUB accounts on same PC

create first SSH key and add to your github as usual
Now for the second SSH key, give it a different name and generate it by using 
    ssh-keygen -t rsa -C "email@domain.com"
Add this to the second github accoutn settings
Now add this using ssh-add ~/.ssh/newssh (not the .pub here)
    If this throws error, do eval `ssh-agent -s`
        (the above starts ssh agent)



##### SSH
sudo pacman -S openssh

##### display scaling
xrandr --current
	shows the displays and what resolutions they support
xrandr --output HDMI-2 --mode 1920x1080
	assigns/changes the displays

### Volume
By default, sound may be muted!
alsamxzer controls
sudo pacman -S alsa-utils
alsamizer

navigate through right and left arrows. MM indicates mute. Press m to unmute. 
Use up and down arrows to increase or decrease the volume

*BONUS* alsamixer has a nice UI which is colorful to rice screenshot :)

___

### Qutebrowser
To set up text search engines, do this in config.py file
c.url.searchengines = {
"DEFAULT":
"yt":

}

___

### Compton
Gives the nice shadow effect!

___

###betterlockscreen
Install betterlockscreen from yay and then do this on command. 
betterlockscreen -u ~/Downloads/edin-durmisevic-dark-forest.jpg -r 1920x1080 -b 0.5 -l dim
then store this in i3 config!
exec --no-startup-id betterlockscreen -w dim

___

 
