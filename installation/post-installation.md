# Post Arch Installation

#### Pacman
```sh
# Pacman.conf
# uncomment 'Color'
# Add 'ILoveCandy'
# Increase Parallel Downloads count
: sudo vim /etc/pacman.conf
```

#### Upgrade system
`: sudo pacman -Syu`

#### Reflector
```sh
: pacman -Syu
: pacman -S reflector
: reflector --latest 9  --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
: systemctl enable reflector.timer
: systemctl start reflector.timer
```

#### System cleaner
```sh
: sudo pacman -S pacman-contrib
: 
```

#### Install Microcode
```sh
: pacman -S amd-ucode # AMD CPU
or
: pacman -S intel-ucode # Intel CPU
```

#### Update grub config file again
`: grub-mkconfig -o /boot/grub/grub.cfg`

#### Display drivers
```sh
: pacman -S xorg xorg-xinit xdg-user-dirs

# Copy .xinitrc to home folder
: sudo cp /etc/X11/xinit/xinitrc ~/.xinitrc
: startx
```

#### User DIrectories
```sh
: pacman -S xdg-user-dirs
: xdg-user-dirs-update
```

#### Enable AUR helper
```sh
: git clone https://aur.archlinux.org/yay-git.git && cd yay
: makepkg -si
```

#### Sound
```sh
: sudo pacman -S alsa-utils alsa-plugins pulse
- pulseaudio-alsa # to manage ALSA
- pulseaudio-bluetooth # for bluetooth
- pulseaudio-equalizer # for equalizer sink
- pulseaudio-jack # for JACK
- pulseaudio-lirc # for infrared volume control with LIRC
- pulseaudio-zeroconf # for Zeroconf (Avahi/DNS-SD) support
```

#### Printing
```sh
: sudo pacman -S cups cups-pdf
: systemctl enable cups.service
```