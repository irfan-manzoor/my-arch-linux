### Install i3 Window Manager
```sh
sudo pacman -S i3-wm i3status i3blocks rofi alacritty
```

### Default Keybindings

| Keybiding                | Description                                       |
| ------------------------ | ------------------------------------------------- |
| `Mod + Enter`            | Open terminal.                                    |
| `Mod + ←`                | Focus left.                                       |
| `Mod + →`                | Focus right.                                      |
| `Mod + ↑`                | Focus up.                                         |
| `Mod + ↓`                | Focus down.                                       |
| `Mod + Shift + ←`        | Move the window to the left side.                 |
| `Mod + Shift + →`        | Move the window to the right side.                |
| `Mod + Shift + ↑`        | Move the window up.                               |
| `Mod + Shift + ↓`        | Move the window down.                             |
| `Mod + f`                | Toggle the focused window to full-screen.         |
| `Mod + v`                | The next window will be placed vertically.        |
| `Mod + h`                | The next window will be placed horizontally.      |
| `Mod + s`                | Enables the stacked window layout.                |
| `Mod + w`                | Enables the tabbed window layout.                 |
| `Mod + Shift + Space`    | Enables the floating window (for focused window). |
| `Mod + Left-mouse-click` | Drag the entire window using the mouse.           |
| `Mod + 0-9`              | Switch to another workspace.                      |
| `Mod + Shift + 0-9`      | Move the window to another workspace.             |
| `Mod + d`                | Open the application launcher (D menu).           |
| `Mod + Shift + q`        | Kills the focused window.                         |
| `Mod + Shift + c`        | Reloads the I3 config file.                       |
| `Mod + Shift + r`        | Restart the I3 WM.                                |
| `Mod + Shift + e`        | Exit I3 WM.                                       |

## Resolution of i3 WM
`xrandr --output [Display_name] --mode [resolution]

Find name of connected display:
```sh
xrandr | grep -w 'connected'
```

Add to i3 config
```c
# Display Resolution
exec_always xrandr --output [Display_name] --mode [Resolution]
```

Restart i3-wm `Mod + Shift + r`
## Wallpaper

Install `feh`
```sh
sudo pacman -S feh
```

Add to i3 config
```c
# Display Wallpaper
exec_always feh --bg-fill /path/to/wallpaper
```

Restart i3-wm `Mod + Shift + r`.

## Lock screen 

Install the i3lock-color from AUR:
```sh
yay i3lock-color
```

Make new directory and script file to store the i3lock configuration:
```sh
mkdir ~/.config/scripts && vim ~/.config/scripts/lock
```

[[lock|Lock screen styling script]]

Make this file executable:
```sh
sudo chmod +x .config/scripts/lock
```

Add to config file:
```c
# Shortcut for Lockscreen
bindsym $mod+x exec /home/$USER/.config/scripts/lock
```

## Theme and icons

Install `materia-gtk-theme` and `papirus` icons:
```sh
sudo pacman -S materia-gtk-theme papirus-icon-theme
```

Install lxappearance:
```c
sudo pacman -S lxappearance
```

## Icons for workspaces

Install new fonts named Awesome:
```sh
sudo pacman -S ttf-font-awesome
```

Add to i3 config
```c
# Define names for default workspaces for which we configure key bindings later on.
# We use variables to avoid repeating the names in multiple places.
set $ws1 "1: Terminal"
set $ws2 "2: VSCode"
set $ws3 "3: Browser"
set $ws4 "4"
set $ws5 "5"
set $ws6 "6"
set $ws7 "7"
set $ws8 "8"
set $ws9 "9"
set $ws10 "10"
```

[Cheatsheet Awesome Font](https://fontawesome.com/v5/cheatsheet?ref=itsfoss.com) to find the appropriate icon.

Copy and paste the icons in front of the name: 
```c
# Define names for default workspaces for which we configure key bindings later>
# We use variables to avoid repeating the names in multiple places.
set $ws1 "1: Terminal"
set $ws2 "2: VSCode"
set $ws3 "3: Browser"
set $ws4 "4"
set $ws5 "5"
set $ws6 "6"
set $ws7 "7"
set $ws8 "8"
set $ws9 "9"
set $ws10 "10"
```

## Font of the title window and bar

Install fonts:
```sh
sudo pacman -S ttf-ubuntu-font-family
```

Add to i3 config
```c
font pango:Ubuntu Regular 14
```

Restart i3

## Allocate applications to workspaces

Start the application and terminal side by side

Execute:
```sh
xprop
```

Hover the cursor on the application and click anywhere inside the application window

Note the class name:
```sh
WM_CLASS(STRING) = "Navigator", "firefox"
```

Repeat the process for all the applications want to allocate to workspaces.

Add to i3 config for all applications to allocate:
```c
# Allocate applications to workspaces
for_window [class="class_name"] move to workspace $[workspace_variable]
```

## Transparent terminal

Install picom:
```sh
sudo pacman -S picom
```

Add to i3 config
```c
# Transparency with picom compositor
exec_always picom -f
```

`-f` flag is used to enable the fading effect

Restart i3

## Customize the status bar

Create a new directory inside the .config:
```sh
mkdir .config/i3status
```

Copy i3status.conf to home directory
```sh
sudo cp /etc/i3status.conf ~/.config/i3status/i3status.conf
```

Change the ownership:
```sh
sudo chown $USER:$USER ~/.config/i3status/i3status.conf
```

Add to i3 config

Llook for the `status_command i3status` line:
```c
bar {
        status_command i3status -c /home/$USER/.config/i3status/i3status.conf
}
```

Customize `i3status.conf`
```c
vim .config/i3status/i3status.conf
```

```c
#order += "ipv6"
#order += "wireless _first_"
order += "ethernet _first_"
#order += "battery all"
order += "disk /"
#order += "load"
order += "memory"
order += "tztime local"
```

## Color scheme

Declare variables for each color. Add to i3 config:
```c
# Color shemes for windows 
set $bgcolor    #523d64
set $in-bgcolor #363636
set $text       #ffffff
set $u-bgcolor  #ff0000
set $indicator  #a8a3c1
set $in-text    #969696
#                       border          background      text            indicator (a line which shows where the next window will be placed)     
client.focused          $bgcolor        $bgcolor        $text           $indicator
client.unfocused        $in-bgcolor     $in-bgcolor     $in-text        $in-bgcolor
client.focused_inactive $in-bgcolor     $in-bgcolor     $in-text        $in-bgcolor
client.urgent           $u-bgcolor      $u-bgcolor      $text           $u-bgcolor 
```

- `bgcolor` indicates the background color.
- `in-bgcolor` indicates background color for inactive windows.
- `text` is for the text color.
- `u-bgcolor` indicates the background for urgent action.
- `indicator` is color for the line, which indicates where the next window will be placed.
- `in-text` text color when inactive.

Basic classes:

- `client.focused` defines colors for the focused windows.
- `client.unfocused` decides how to decorate windows when not focused.
- `client.focused_inactive` shows colors when one of the containers is focused but does not have the focus at the moment.
- `client.urgent` defines colors when urgent action is needed.

Restart i3
### Color scheme

In the i3 config in the `bar {...}` section.
```c
bar {
        status_command i3status -c /home/$USER/.config/i3status/i3status.conf
        colors {
                background $bgcolor
                separator #191919
                #                       border          background      text
                focused_workspace       $bgcolor        $bgcolor        $text
                inactive_workspace      $in-bgcolor     $in-bgcolor     $text
                urgent_workspace        $u-bgcolor      $u-bgcolor      $text   

        }
}
```

Restart i3

## Transparent bar

Install droid fonts:
```sh
sudo pacman -S ttf-droid
```

Add to i3 config
```c
bar{
	...
	font pango: Droid Sans Mono 11
	...
}	
```

Restart i3

To control transparency, [Codes ranging from 0 to 100% transparency](https://gist.github.com/lopspower/03fb1cc0ac9f32ef38f4?ref=itsfoss.com).

Add to i3 config
```c
set $bgcolor    #523d6499
set $in-bgcolor #3636364D
```

Added new variables with different transparency:
```c
set $focused-ws #523d6480
set $bar-color  #523d640D
```

Change the bar section to apply transparency and colors:
```c
bar {
	status_command i3status -c /home/$USER/.config/i3status/i3status.conf
	i3bar_command i3bar --transparency
	tray_output none
	font pango: Droid Sans Mono 11
    colors {
            background $bar-color
            separator #191919
            #                       border          background      text
            focused_workspace       $focused-ws     $focused-ws     $text
            inactive_workspace      $in-bgcolor     $in-bgcolor     $text
            urgent_workspace        $u-bgcolor      $u-bgcolor      $text
}
```

`tray_output none` hide any icons in the tray

Restart i3
## i3 Blocks

Create a new directory inside the .config:
```sh
mkdir ~/.config/i3blocks
```

Copy i3block.conf to home directory
```sh
sudo cp /etc/i3blocks.conf ~/.config/i3blocks/
```

Change the ownership:
```sh
sudo chown $USER:$USER ~/.config/i3blocks/i3blocks.conf
```

Changes to the i3 config file: 
```c
bar {
	status_command i3blocks -c /home/$USER/.config/i3blocks/i3blocks.conf
	i3bar_command i3bar --transparency
	....
}
```

Restart i3

### Adding blocks

[[disk|Disk block]]
```sh
vim ~/.config/scripts/disk
sudo chmod +x ~/.config/scripts/disk
```

[[memory|Memory block]]
```sh
vim ~/.config/scripts/memory
sudo chmod +x ~/.config/scripts/memory
```

[[arch-update|Arch Update block]]
```sh
sudo pacman -S pacman-contrib
vim ~/.config/scripts/arch-update
sudo chmod +x ~/.config/scripts/arch-update
```

[[volume|Volume Indicator block]]

Install dependencies:
```sh
# Install dependencies
sudo pacman -S pulseaudio-alsa pulseaudio-bluetooth pulseaudio-equalizer pulseaudio-jack alsa-utils playerctl

# Create new file
vim ~/.config/scripts/volume
sudo chmod +x ~/.config/scripts/volume
```
*If  audio/video not working, use this command: `systemctl --user disable --now pipewire.{socket,service} && systemctl --user mask pipewire.socket`*

Add to i3blocks.conf
```c
[disk]
command=/home/$USER/.config/scripts/disk
LABEL=
#DIR=$HOME
#ALERT_LOW=10
interval=30

[memory]
command=/home/$USER/.config/scripts/memory
label=
interval=30

[arch-update]
command=/home/$USER/.config/scripts/arch-update
interval=3600
markup=pango
LABEL=

[volume]
command=/home/$USER/.config/scripts/volume
LABEL=♪
#LABEL=VOL 
interval=1
signal=10
#STEP=5%
MIXER=default
#SCONTROL=[determined automatically]
#NATURAL_MAPPING=0
```
*If audio/video not working, use: `systemctl --user disable --now pipewire.{socket,service} && systemctl --user mask pipewire.socket`*

Reload i3
## i3 gaps

Add to i3 .config:
```c
# default gaps
gaps inner 15
gaps outer 5

# gaps
set $mode_gaps Gaps: (o)uter, (i)nner, (h)orizontal, (v)ertical, (t)op, (r)ight, (b)ottom, (l)eft
set $mode_gaps_outer Outer Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_inner Inner Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_horiz Horizontal Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_verti Vertical Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_top Top Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_right Right Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_bottom Bottom Gaps: +|-|0 (local), Shift + +|-|0 (global)
set $mode_gaps_left Left Gaps: +|-|0 (local), Shift + +|-|0 (global)
bindsym $mod+Shift+g mode "$mode_gaps"

mode "$mode_gaps" {
        bindsym o      mode "$mode_gaps_outer"
        bindsym i      mode "$mode_gaps_inner"
        bindsym h      mode "$mode_gaps_horiz"
        bindsym v      mode "$mode_gaps_verti"
        bindsym t      mode "$mode_gaps_top"
        bindsym r      mode "$mode_gaps_right"
        bindsym b      mode "$mode_gaps_bottom"
        bindsym l      mode "$mode_gaps_left"
        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}

mode "$mode_gaps_outer" {
        bindsym plus  gaps outer current plus 5
        bindsym minus gaps outer current minus 5
        bindsym 0     gaps outer current set 0

        bindsym Shift+plus  gaps outer all plus 5
        bindsym Shift+minus gaps outer all minus 5
        bindsym Shift+0     gaps outer all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_inner" {
        bindsym plus  gaps inner current plus 5
        bindsym minus gaps inner current minus 5
        bindsym 0     gaps inner current set 0

        bindsym Shift+plus  gaps inner all plus 5
        bindsym Shift+minus gaps inner all minus 5
        bindsym Shift+0     gaps inner all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_horiz" {
        bindsym plus  gaps horizontal current plus 5
        bindsym minus gaps horizontal current minus 5
        bindsym 0     gaps horizontal current set 0

        bindsym Shift+plus  gaps horizontal all plus 5
        bindsym Shift+minus gaps horizontal all minus 5
        bindsym Shift+0     gaps horizontal all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_verti" {
        bindsym plus  gaps vertical current plus 5
        bindsym minus gaps vertical current minus 5
        bindsym 0     gaps vertical current set 0

        bindsym Shift+plus  gaps vertical all plus 5
        bindsym Shift+minus gaps vertical all minus 5
        bindsym Shift+0     gaps vertical all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_top" {
        bindsym plus  gaps top current plus 5
        bindsym minus gaps top current minus 5
        bindsym 0     gaps top current set 0

        bindsym Shift+plus  gaps top all plus 5
        bindsym Shift+minus gaps top all minus 5
        bindsym Shift+0     gaps top all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_right" {
        bindsym plus  gaps right current plus 5
        bindsym minus gaps right current minus 5
        bindsym 0     gaps right current set 0

        bindsym Shift+plus  gaps right all plus 5
        bindsym Shift+minus gaps right all minus 5
        bindsym Shift+0     gaps right all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_bottom" {
        bindsym plus  gaps bottom current plus 5
        bindsym minus gaps bottom current minus 5
        bindsym 0     gaps bottom current set 0

        bindsym Shift+plus  gaps bottom all plus 5
        bindsym Shift+minus gaps bottom all minus 5
        bindsym Shift+0     gaps bottom all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
mode "$mode_gaps_left" {
        bindsym plus  gaps left current plus 5
        bindsym minus gaps left current minus 5
        bindsym 0     gaps left current set 0

        bindsym Shift+plus  gaps left all plus 5
        bindsym Shift+minus gaps left all minus 5
        bindsym Shift+0     gaps left all set 0

        bindsym Return mode "$mode_gaps"
        bindsym Escape mode "default"
}
```

Reload i3

To resize the gaps:

1. `Mod + Shift + g` to enter the gaps mode
2. Choose from given options
3. Use `+` or `-` to increase/decrease gaps 
4. Once done, press the `Esc` key to exit the gaps mode