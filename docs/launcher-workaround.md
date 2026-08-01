## Requirements

- GNOME Desktop Environment
- Two configured input sources (e.g. us and your native layout)
  - **Keyboard input sources must be set to "Use different input sources for each window".**
- Krita AppImage (or adjust the launcher for your installation) on your desktop environment

## This is an example for Krita. Adjust the Name to your drawing program

## 1) Create a bin directory in your home directory and create the file start-krita.sh
- I would recommend to use nano as an editor but you can use any editor you whant

```bash
mkdir -p ~/bin
nano ~/bin/start-krita.sh
chmod +x ~/bin/start-krita.sh  # Script ausführbar machen  # Make the launcher script executable.
```

## 2) After opening start-krita.sh in your Editor copy paste the following script into `start-krita.sh`
```bash
#!/bin/bash

# Prevent multiple instances of this launcher from running simultaneously.
# This avoids race conditions when the launcher is started multiple times.
exec 9>/tmp/start-krita.lock
flock -n 9 || exit 0

# SAFETY NET: This command will run AUTOMATICALLY whenever the script exits, 
# even if Krita crashes or the script is interrupted.
trap 'gsettings set org.gnome.desktop.input-sources sources "[('\''xkb'\'', '\''de'\''), ('\'xkb'\'', '\''us'\'')]"' EXIT

# Set the US layout to first place so Krita is launched with the US keyboard layout.
gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'us'), ('xkb', 'de')]"

(
    
# Delay before restoring DE layout.
# 9 seconds gives Krita enough startup time even under system load.
    sleep 9
# The original layout is set to first place again.
    gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'de'), ('xkb', 'us')]"
) &

# Launching the Krita AppImage and also allowing other arguments to be passed down.
$HOME/PATH-TO-YOUR-KRITA-APPIMAGE/krita-5.3.3-x86_64.AppImage "$@" 
```
## 3) Adjust the script
Replace de with your own primary keyboard layout.

Examples:
- German → de
- French → fr
- Spanish → es
- Italian → it
- Czech → cz

You can find out yours with:
```bash
gsettings get org.gnome.desktop.input-sources sources
```
Add the path to your Krita app image to the last line of the script 

## 4) Test the launcher

Run:

```bash
~/bin/start-krita.sh
```
- Are there no problems and now your keyboard layout is set to the US layout? If not, try to give Krita more time to launche by adjusting the 9 seconds to 15. Launching time differs from device to device.

## 5) Update your Krita launcher

Locate the `krita.desktop` launcher.

My file is at ~/.local/share/applications/krita.desktop

Open an editor and replace the `Exec=` line.
For nano: ```bash
nano ~/.local/share/applications/krita.desktop
```
Example:

```ini
Exec=/path/to/Krita.AppImage %F
```

Replace it with:
```ini
Exec=/home/USERNAME/bin/start-krita.sh %F
```

Replace `USERNAME` with your Linux username.

## Now try to open Krita. Does your layout change? Greate job!

## Limitations

This launcher is a convenience workaround and not a fix for the XP-Pen driver itself.
Because the launcher temporarily changes the primary keyboard layout to US, any other application started during this short time window may also inherit the US layout.
The delay is intentionally set to provide enough startup time for Krita on slower systems. Feel free to adjust it to suit your hardware.











