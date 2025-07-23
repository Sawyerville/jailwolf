# Jailwolf

[BastilleBSD](https://bastillebsd.org) template to bootstrap Firefox.

## Description

When applied to a container, this template will install with dependencies
 
- icewolf 
- noto fonts ( All the glyphs for the modern web )
- xauth
- mesa-dri ( 3D Support if available )
- add user `icewolf` 
- gives access to the socket `/tmp/.X11-unix/:0=` from inside the jail.

This all adds up to a container size of: **~1.8 Gigabyte**

To run the jailed icewolf from the host system please visit 
[jailwolf-install](https://github.com/sawyerville/jailwolf-install) for further instructions.

## Bootstrap
```shell
bastille bootstrap https://github.com/ddowse/jailwolf
```

## Apply
```shell
bastille template TARGET sawyerville/jailwolf [ --arg WITH_USB=1 ] [ --arg WITH_3D=1  ]
```
The arguments `WITH_3D` and `WITH_USB` are stricly optional. But if you decide
to use them. You have add some rules to `/etc/devfs.rules`, some rules that i
have found working are listed in the `devfs.rules` file in this repo.   
Add the content of `devfs.rules` to your existing `/etc/devfs.rules`.

```sh
[bastille_xorg=99]
add include $devfsrules_hide_all
add include $devfsrules_unhide_basic
add include $devfsrules_unhide_login

# Remove comments in the next 2 lines to allow access to soundcard
#add path 'mixer*' unhide
#add path 'dsp*'   unhide

# Remove comment in the next line to allow access to all output/input sound devices
# This is mandatory to get microphone working
#add path 'sndstat' unhide

# Remove comments in the next 2 lines to allow access to Webcam
#add path 'cuse*'  unhide
#add path 'video'  unhide

# Remove comments in the next 5 lines to allow access to Videocard for 3D
#add path 'dri'    unhide
#add path 'dri/*'  unhide 
#add path 'drm'    unhide
#add path 'drm/*'  unhide
#add path 'pci'    unhide

# Remove comments in the next 2 lines to allow access to USB devices.
# Mandatory if Webcam and Headset is connected via USB 
#add path 'usb'    unhide
#add path 'usb/*'  unhide
```

```shell
bastille config TARGET set devfs_ruleset 99
service devfs restart
bastille restart TARGET
```

## 3D Acceleration 

Go to `Settings` scroll down then **Uncheck** "Use recommended performance settings".   

![Icewolf Settings](ff-performance.png)

Open `about:config` and set `gfx.webrender.all` to `true`

Restart Icewolf

## Hint

To avoid frustration, I recommend to ZFS snapshot the target jail before applying
the BastilleBSD template. This way it makes it very easy to start all over again by rolling back to the snapshot. 
You could also create a ZFS snapshot of the target jail, after the template is
applied, to always have a fresh webbrowser at your disposal.

And feedback on this template is welcome. 
