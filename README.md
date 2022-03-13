# Arch Linux Ultimate Install Guide
## Installation
First thing first set your keyboard layout by running `loadkeys <layout>` .  If you want to connect to wifi use `iwd` . After setting your keyboard layout, you should make sure the system clock is accurate by running `timedatectl set-ntp true` .

Configure your hard drive partitions using `cfdisk` . Create a bootable partition (sda1) and a swap partition (sda2). Complete disk partitioning by running:
```
mkfs.ext4 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
```

Mount the main partition by running `mount /dev/sda1 /mnt` . Download the linux kernel onto the mounted partition `pacstrap /mnt base linux linux-firware` . Don't forget to create the fstab by running `genfstab -U /mnt >> /mnt/etc/fstab` . After The installation change root to the arch linux filesystem by running `arch-chroot /mnt` .

 Now you need to setup the arch linux environment. Run these commands:
 ```bash
 ln -sf /usr/share/zoneinfo/Europe/Budapest /etc/localtime
 hwclock --systohc
 pacman -S sudo nano vim grub dhcpcd iwd
 ```
Now open `/etc/locale.gen` . Uncomment *en_US.UTF-8* . After that run `locale-gen` .

Edit these files:

| File | Edit |
|:---:|:---:|
| /etc/locale.conf | LANG=en_US.UTF-8 |
| /etc/vconsole.conf | KEYMAP=\<keymap\> |
| /etc/hostname | arch |

Run `mkinitcpio -P` . Set root password using `passwd` . Create a user profile by running `useradd -G wheel -m <username>` . Set the password by runnning `passwd <username>` . Now run `visudo` and add `<username> ALL=(ALL) ALL` . 

Install the bootloader by running these commands:
```bash
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

Enable dhcpcd and iwd by running: `systemctl enable dhcpcd iwd` . The enable connection to the internet.

Finally you need to install a window manager. Run this command:

```bash
pacman-key --init
pacman -S xorg lightdm-gtk-greeter i3-gaps dmenu i3status ttf-dejavu
systemctl enable lightdm
```

Install a terminal emulator.  I would recommend using `alacritty` or `konsole` .

If you are running in a Virtual Machine install `open-vm-tools` and `xf86-video-vmware` and run `systemctl enable vmtoolsd` .

Dont forget to switch the keyboard layout in `/etc/X11/xorg.conf.d/20-keyboard.conf` by adding these lines to it:
```txt
Section "InputClass"
	Identifier "keyboard"
	MatchIsKeyboard "yes"
	Option "XkbLayout" "<layout>"
EndSection
```

Now you are done! Run `reboot` .

## Software
### Essential
Install a web browser. I recommend installing `firefox` or `chromium` .
If you want to compile packages you should download the GNU Toolchain by installing `base-devel` , `git` and `cmake` .

You want to install an AUR helper too. Use yay!
```bash
git clone https://aur.archlinux.org/yay-git.git
cd yay-git
makepkg -si
```

Install most essential programs in a single run:

```bash
pacman -S vlc gimp eog ffmpeg mpv libreoffice htop
```

### Aesthetics
Install the `picom` compositor. Use `nitrogen` to set a wallpaper. Add these lines to `.config/i3/config` :
```txt
exec --no-startup-id picom
exec --no-startup-id nitrogen --restore
```

You can have gaps between your windows by adding these lines to the file above:
```txt
for_window [class="^.*"] border pixel 0
gaps inner 12
gaps outer 12
```

Set up picom by adding these lines to `.config/picom/picom.conf` :
```txt
shadow = true;
shadow-radius = 18;
shadow-opacity = 1;
shadow-offset-x = -12;
shadow-offset-y = -12;
shadow-color = "#000000";

corner-radius = 10;

fading = true;
fade-in-step = 0.1;
fade-out-step = 0.08;
fade-delta = 20;

inactive-opacity = 0.95;
active-opacity = 0.95;
```

I suggest installing a cool shell too. Use `fish` and `starship` ! 
Run these commands to configure fish:
```bash
set fish_greeting
echo "\nstarship init fish | source\n" >> ~/.config/fish/config.fish
```

Nerd Fonts are needed for some of the programs used. Install it by running:
```bash
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/JetBrainsMono.zip
```
Copy the file to `/usr/share/fonts/`. Make a `nerd-fonts` directory and unzip the file there by running `unzip JetBrainsMono.zip`.
