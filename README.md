# Arch Linux River Wayland Setup Guide

This guide documents the process of setting up and customizing a minimal, performant River compositor environment on Arch Linux, with a focus on creating a development environment for C/C++ and graphics programming.

## Setup Order

The optimal order for setting up components:

1. Core Wayland/River installation (prerequisite)
2. Status bar (Waybar)
3. Application launcher (Fuzzel)
4. Audio management (PipeWire)
5. Bluetooth management
6. Wallpaper management
7. Development environment

## 1. Status Bar (Waybar)

Waybar provides system information and application indicators.

### Installation

```bash
sudo pacman -S waybar
```

### Configuration

Create directories:
```bash
mkdir -p ~/.config/waybar
```

Create `~/.config/waybar/config`:
```json
{
    "layer": "top",
    "position": "top",
    "height": 30,
    "spacing": 8,
    
    "modules-left": ["river/tags"],
    "modules-center": ["river/window"],
    "modules-right": ["pulseaudio", "bluetooth", "network", "cpu", "memory", "battery", "clock", "tray"],
    
    "river/tags": {
        "num-tags": 9
    },
    
    "river/window": {
        "format": "{}"
    },
    
    "clock": {
        "format": "{:%H:%M %d/%m/%Y}",
        "tooltip-format": "<big>{:%Y %B}</big>\n<tt><small>{calendar}</small></tt>"
    },
    
    "cpu": {
        "format": "CPU: {usage}% ",
        "interval": 5
    },
    
    "memory": {
        "format": "MEM: {}% "
    },
    
    "battery": {
        "states": {
            "good": 95,
            "warning": 30,
            "critical": 15
        },
        "format": "BAT: {capacity}% {icon}",
        "format-charging": "BAT: {capacity}% ",
        "format-plugged": "BAT: {capacity}% ",
        "format-icons": ["", "", "", "", ""]
    },
    
    "network": {
        "format-wifi": "WIFI: {essid} ({signalStrength}%) ",
        "format-ethernet": "ETH: {ipaddr} ",
        "format-disconnected": "NET: Disconnected ",
        "tooltip-format": "{ifname}: {ipaddr}/{cidr}"
    },
    
    "pulseaudio": {
        "format": "VOL: {volume}% {icon}",
        "format-bluetooth": "BT: {volume}% {icon}",
        "format-muted": "MUTE ",
        "format-icons": {
            "headphone": "",
            "hands-free": "",
            "headset": "",
            "phone": "",
            "portable": "",
            "car": "",
            "default": ["", "", ""]
        },
        "on-click": "pavucontrol"
    },
    
    "bluetooth": {
        "format": "BT: {status}",
        "format-connected": "BT: {device_alias}",
        "format-connected-battery": "BT: {device_alias} {device_battery_percentage}%",
        "tooltip-format": "{controller_alias}\t{controller_address}\n\n{num_connections} connected",
        "on-click": "blueman-manager"
    },
    
    "tray": {
        "icon-size": 21,
        "spacing": 10
    }
}
```

Create `~/.config/waybar/style.css`:
```css
* {
    border: none;
    border-radius: 0;
    font-family: "JetBrains Mono", "Font Awesome 6 Free", sans-serif;
    font-size: 14px;
    min-height: 0;
}

window#waybar {
    background-color: rgba(43, 48, 59, 0.9);
    color: #ffffff;
    transition-property: background-color;
    transition-duration: .5s;
}

window#waybar.hidden {
    opacity: 0.2;
}

#tags button {
    padding: 0 5px;
    background-color: transparent;
    color: #ffffff;
    border-bottom: 3px solid transparent;
}

#tags button.focused {
    background-color: #64727D;
    box-shadow: inset 0 -3px #ffffff;
}

#clock,
#battery,
#cpu,
#memory,
#disk,
#temperature,
#backlight,
#network,
#pulseaudio,
#bluetooth,
#tray,
#mode {
    padding: 0 10px;
    color: #ffffff;
    background-color: #404552;
    margin: 2px 4px;
    border-radius: 3px;
}

#window,
#workspaces {
    margin: 0 4px;
}

/* Module-specific styling */
#clock {
    background-color: #64727D;
}

#battery {
    background-color: #90b1b1;
}

#battery.charging, #battery.plugged {
    color: #ffffff;
    background-color: #26A65B;
}

#battery.critical:not(.charging) {
    background-color: #f53c3c;
    color: #ffffff;
    animation-name: blink;
    animation-duration: 0.5s;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
    animation-direction: alternate;
}

@keyframes blink {
    to {
        background-color: #ffffff;
        color: #000000;
    }
}

#cpu {
    background-color: #2ecc71;
    color: #000000;
}

#memory {
    background-color: #9b59b6;
}

#network {
    background-color: #2980b9;
}

#network.disconnected {
    background-color: #f53c3c;
}

#pulseaudio {
    background-color: #f1c40f;
    color: #000000;
}

#pulseaudio.muted {
    background-color: #90b1b1;
    color: #2a5c45;
}

#bluetooth {
    background-color: #3b5998;
}

#bluetooth.disconnected {
    background-color: #8c9bbd;
}

#tray {
    background-color: #404552;
}

#tray > .passive {
    -gtk-icon-effect: dim;
}

#tray > .needs-attention {
    -gtk-icon-effect: highlight;
    background-color: #eb4d4b;
}
```

Install required font:
```bash
sudo pacman -S ttf-font-awesome
```

### Add to River Init

Add to your `~/.config/river/init`:
```bash
# Kill any existing waybar instances to avoid duplicates
pkill waybar || true
waybar &
```

### Customizing Waybar

- Edit `~/.config/waybar/config` to change modules or their order
- Edit `~/.config/waybar/style.css` to change colors, spacing, and other visual elements
- Restart Waybar to apply changes: `pkill waybar && waybar &`

## 2. Application Launcher (Fuzzel)

Fuzzel is a fast, lightweight application launcher for Wayland.

### Installation

```bash
sudo pacman -S fuzzel
```

### Configuration

Create directory:
```bash
mkdir -p ~/.config/fuzzel
```

Create `~/.config/fuzzel/fuzzel.ini`:
```ini
[main]
# Interface options
font=monospace:size=12
icon-theme=hicolor
terminal=foot
prompt=" > "
layer=overlay
width=35
horizontal-pad=20
vertical-pad=16
inner-pad=10

# Selection options
lines=15
tabs=4
show-actions=yes
print-command=true
fuzzy=yes

[colors]
# Base16 style colors
background=282c34fa
text=abb2bfff
match=61afefff
selection=3e4451ff
selection-text=abb2bfff
selection-match=61afefff
border=61afefff

[border]
width=2
radius=10

[dmenu]
exit-immediately-if-empty=yes
```

### Add to River Init

Add to your `~/.config/river/init`:
```bash
# Application launcher
riverctl map normal Super d spawn fuzzel
```

### Usage

- Press `Super+d` to open the launcher
- Type to search for applications
- Use arrow keys to navigate results
- Press Enter to launch the selected application

### Customization

- Edit colors in the `[colors]` section
- Adjust border radius in the `[border]` section
- Change font or font size in the `font` option

## 3. Audio Management (PipeWire)

PipeWire is a modern audio and video server with excellent Bluetooth support.

### Installation

```bash
sudo pacman -S pipewire pipewire-pulse pipewire-alsa pipewire-jack wireplumber
sudo pacman -S pavucontrol pamixer
```

### Enable Services

```bash
systemctl --user enable pipewire.service
systemctl --user enable pipewire-pulse.service
systemctl --user enable wireplumber.service
systemctl --user start pipewire.service
systemctl --user start pipewire-pulse.service
systemctl --user start wireplumber.service
```

### Add to River Init

Add to your `~/.config/river/init`:
```bash
# Volume controls
riverctl map normal None XF86AudioRaiseVolume spawn 'pamixer -i 5'
riverctl map normal None XF86AudioLowerVolume spawn 'pamixer -d 5'
riverctl map normal None XF86AudioMute spawn 'pamixer -t'

# Alternative volume controls (if media keys aren't available)
riverctl map normal Super bracketright spawn 'pamixer -i 5'
riverctl map normal Super bracketleft spawn 'pamixer -d 5'
riverctl map normal Super backslash spawn 'pamixer -t'

# Launch audio mixer
riverctl map normal Super a spawn 'pavucontrol'
```

### Usage

- Use volume keys or configured shortcuts to control volume
- Use `Super+a` to open the PulseAudio Volume Control
- The Waybar pulseaudio module will show volume levels

## 4. Bluetooth Management

For connecting Bluetooth devices like mice, keyboards, and headphones.

### Installation

```bash
sudo pacman -S bluez bluez-utils blueman
```

### Enable Service

```bash
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
```

### Add to River Init

Add to your `~/.config/river/init`:
```bash
# Bluetooth management
blueman-applet &
```

### Configure Permissions (if needed)

```bash
sudo usermod -a -G lp $USER
```
(Log out and back in for this to take effect)

### Usage

- Click the Bluetooth icon in Waybar to open the manager
- Use blueman-manager to pair and connect to devices
- For command-line control, use `bluetoothctl`

### Automatic Connections

Edit `/etc/bluetooth/main.conf`:
```
sudo nano /etc/bluetooth/main.conf
```

Find and uncomment the line with `AutoEnable` and set it to true:
```
AutoEnable=true
```

## 5. Wallpaper Management

### Installation

```bash
sudo pacman -S swaybg
```

### Basic Usage

Add to your `~/.config/river/init`:
```bash
# Set wallpaper
swaybg -m fill -i /path/to/your/wallpaper.jpg &
```

### Random Wallpaper (Optional)

Create directory:
```bash
mkdir -p ~/Pictures/Wallpapers
mkdir -p ~/.local/bin
```

Create `~/.local/bin/random-wallpaper.sh`:
```bash
#!/bin/bash
WALLPAPER_DIR="$HOME/Pictures/Wallpapers"
WALLPAPER=$(find "$WALLPAPER_DIR" -type f | shuf -n 1)
pkill swaybg
swaybg -m fill -i "$WALLPAPER" &
```

Make it executable:
```bash
chmod +x ~/.local/bin/random-wallpaper.sh
```

Update your River init file:
```bash
# Set random wallpaper
~/.local/bin/random-wallpaper.sh
```

### Wallpaper Display Modes

- `-m fill`: Scales the image to fill the screen (may crop)
- `-m fit`: Scales the image to fit the screen (may have black bars)
- `-m stretch`: Stretches the image to fill the screen (may distort)
- `-m center`: Centers the image without scaling
- `-m tile`: Tiles the image to fill the screen

## 6. File Explorer (PCManFM)

### Installation

```bash
sudo pacman -S pcmanfm-gtk3 gvfs
```

### Configuration

Add to your `~/.config/river/init`:
```bash
# File manager shortcut
riverctl map normal Super e spawn pcmanfm
```

### Theming (Optional)

Install themes:
```bash
sudo pacman -S arc-gtk-theme papirus-icon-theme
```

Create `~/.config/gtk-3.0/settings.ini`:
```ini
[Settings]
gtk-theme-name=Arc-Dark
gtk-icon-theme-name=Papirus-Dark
gtk-font-name=Sans 11
gtk-cursor-theme-name=Adwaita
gtk-cursor-theme-size=24
```

### Usage

- Press `Super+e` to open PCManFM
- Use for file management, folder browsing, and accessing external devices
- Right-click for context menu options

## 7. Media Viewers

### Installation

```bash
sudo pacman -S mpv imv
```

### File Associations

Create or edit `~/.config/mimeapps.list`:
```ini
[Default Applications]
image/jpeg=imv.desktop
image/png=imv.desktop
image/gif=imv.desktop
video/mp4=mpv.desktop
video/mkv=mpv.desktop
audio/mp3=mpv.desktop
audio/flac=mpv.desktop
```

### Add to River Init (Optional)

```bash
# Media player shortcut
riverctl map normal Super v spawn mpv
```

### Usage

- **imv**: Lightweight image viewer
  - Navigate with arrow keys
  - Zoom with +/- or mouse wheel
  - Press 'f' for fullscreen

- **mpv**: Versatile media player
  - Space to pause/play
  - Left/right arrows to seek
  - Up/down arrows for volume
  - 'f' for fullscreen

## 8. Development Environment for C/C++ and Graphics

### Install Core Development Tools

```bash
sudo pacman -S base-devel git cmake
sudo pacman -S clang lldb gdb valgrind
```

### Install Graphics Libraries

```bash
sudo pacman -S vulkan-devel glfw-wayland glew glm sdl2
sudo pacman -S vulkan-tools mesa-demos
```

### Documentation

```bash
sudo pacman -S man-db man-pages
```

### Code Editor (Choose One)

```bash
# For VS Code
sudo pacman -S visual-studio-code-bin  # From AUR

# For Neovim
sudo pacman -S neovim

# For CLion
# Download and install from JetBrains website
```

### Create Project Directory

```bash
mkdir -p ~/Projects/Graphics
```

### Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Test Graphics Setup

```bash
# Check Vulkan support
vulkaninfo | grep deviceName

# Check OpenGL version
glxinfo | grep "OpenGL version"
```

## Complete River Configuration Example

Here's a complete example for `~/.config/river/init` that includes all components:

```bash
#!/bin/sh

# Set keyboard repeat rate
riverctl set-repeat 50 300

# Set keyboard layout
riverctl keyboard-layout us

# Set cursor theme
riverctl xcursor-theme Adwaita 24

# Basic window management keybindings
riverctl map normal Super q close
riverctl map normal Super+Shift q exit
riverctl map normal Super f toggle-fullscreen
riverctl map normal Super+Shift Space toggle-float

# Focus and window movement
riverctl map normal Super j focus-view next
riverctl map normal Super k focus-view previous
riverctl map normal Super+Shift j swap next
riverctl map normal Super+Shift k swap previous

# Application launcher
riverctl map normal Super d spawn fuzzel

# Terminal (assuming foot is installed)
riverctl map normal Super Return spawn foot

# Volume controls
riverctl map normal None XF86AudioRaiseVolume spawn 'pamixer -i 5'
riverctl map normal None XF86AudioLowerVolume spawn 'pamixer -d 5'
riverctl map normal None XF86AudioMute spawn 'pamixer -t'

# Alternative volume controls
riverctl map normal Super bracketright spawn 'pamixer -i 5'
riverctl map normal Super bracketleft spawn 'pamixer -d 5'
riverctl map normal Super backslash spawn 'pamixer -t'

# Launch audio mixer
riverctl map normal Super a spawn 'pavucontrol'

# File manager
riverctl map normal Super e spawn pcmanfm

# Media player shortcut (optional)
riverctl map normal Super v spawn mpv

# Tags (workspaces)
for i in $(seq 1 9)
do
    # Tags can be bound to keys 1-9
    riverctl map normal Super $i set-focused-tags $((1 << ($i - 1)))
    riverctl map normal Super+Shift $i set-view-tags $((1 << ($i - 1)))
done

# Set the default layout
riverctl default-layout rivertile
rivertile -view-padding 4 -outer-padding 4 &

# Set wallpaper
~/.local/bin/random-wallpaper.sh

# Start services
# Kill any existing waybar instances to avoid duplicates
pkill waybar || true
waybar &

# Bluetooth
blueman-applet &
```

## Troubleshooting

### Waybar Issues
- **Multiple instances**: Make sure to use `pkill waybar || true` before starting Waybar
- **Missing icons**: Install `ttf-font-awesome`
- **Module not appearing**: Check dependencies for that module

### Audio Issues
- **No sound**: Verify correct output in pavucontrol
- **Volume keys not working**: Check keybindings in River init

### Bluetooth Issues
- **No devices found**: Run `rfkill list` and `rfkill unblock bluetooth`
- **Connection issues**: Restart the service `sudo systemctl restart bluetooth.service`

### Graphics Issues
- **No GPU acceleration**: Check driver installation and Vulkan/OpenGL support
- **Application rendering issues**: Ensure proper Wayland compatibility

## Customization Resources

- **River**: [River GitHub Wiki](https://github.com/riverwm/river/wiki)
- **Waybar**: [Waybar Wiki](https://github.com/Alexays/Waybar/wiki)
- **Fuzzel**: [Fuzzel Documentation](https://codeberg.org/dnkl/fuzzel)
- **dotfiles**: Check r/unixporn for inspiration
