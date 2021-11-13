
# Issues with rotation
With KMS introduced as the default graphics mode, and there are many 
arguments for it, and then moving to bullseye on top introduced some new
issues with the official 7" DSI connected touch screen. One of these issues
is that rotation of the screen and touch somewhat differs from the official
 documentation found at the time of release of bullseye in November 2021.

## Rotate the GUI 180 degrees
This is what I did to rotate screen and touch 180 degrees. This will not 
do anything for the output in console mode, splash or login screen though.
It also only works after login so it's far from perfect.

There is a command called `xrandr` that does what you seed to rotate the 
output and this is actually what the "Screen Configuration" application does
and it's quite simple to use.
The command `xrandr --output DSI-1 --rotate inverted` rotates the output of
display _DSI-1_ to show an _inverted_ output which corresponds to 180 degrees
of rotation.

Rotating the screen output doesn't currently rotate the touch input though.
For that another command, `xinput`, is needed that also is quite easy to use
for this. To match input to output one simply tells what input to match to
what output by `xinput --map-to-output "generic ft5x06 (79)" DSI-1`.
The `generic ft5x06 (79)` is the touch device built into the screen as 
bullseye currently sees it.


## Automate to rotate on login.

First create a script that does what is needed.

__/usr/local/bin/rotate-180__
```shell
#!/bin/bash
xrandr --output DSI-1 --rotate inverted
# sleep a while so that the rotation can take affect
sleep 1
# map input to output rotation.
xinput --map-to-output "generic ft5x06 (79)" DSI-1

```

Make that script executable by `sudo chmod +x /usr/local/bin/rotate-180`.

To then automate this to be executed on login I chose to use the 
[autostart feature of xdg](https://wiki.archlinux.org/title/XDG_Autostart).

Create a .desktop file that executes our script upon login.
__/etc/xdg/autostart/rotate-screen.desktop__
```
[Desktop Entry]
Version=1.0
Encoding=UTF-8
Type=Application
Name=DSI-1 Rotation
Icon=preferences-desktop-screensaver
Exec=/usr/local/bin/rotate-180
Terminal=false
OnlyShowIn=LXDE;OPENBOX;GNOME;KDE;
Categories=System;Security;Utility;
StartupNotify=false
```

Reboot and when logged back in you should have both display and input rotated.
