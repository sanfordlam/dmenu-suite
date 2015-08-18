Menu Suite
==========

This is a collection of shell scripts that interface with either [dmenu](http://tools.suckless.org/dmenu/) or [fzf](https://github.com/junegunn/fzf).

Everyone's *nix setups are different. As such, these scripts fit my personal use case; it's not guaranteed that it will fit yours.

Included Scripts
================
| Script         | Description
| -------------- | -----------
| brightnessmenu | Set laptop monitor brightness.
| cpumenu        | Query and set system's enabled cpu profiles.
| dvdmenu        | Navigate dvd with mpv.
| infomenu       | Display some system information with acpi.
| killmenu       | Kill processes.
| menumenu       | Menu to select menus.
| monitormenu    | Setup (preconfigured, hardcoded) monitor layouts with xrandr.
| mpcmenu        | Interface for [mpd](http://www.musicpd.org/) (using [mpc](http://linux.die.net/man/1/mpc)).
| netmenu        | Wifi profile select using netctl.
| runmenu        | List and run programs in user's $PATH.
| shutdownmenu   | Shutdown, reboot, and sleep.
| wallpapermenu  | Select a wallpaper from a hardcoded directory. Need to implement selecting specific wallpapers for specific monitors.
| wmmenu         | Unfinished script to interact with wmctrl.

Possible Usage
==============

All scripts take a single optional argument to decide which backend to use. If this argument is excluded, fzf is used by default. Backends supported: fzf, dmenu, and rofi.

Run mpcmenu (interface for mpd client) with dmenu:

    ~/bin/menu/scripts/mpcmenu dmenu

Run mpcmenu in a new terminal emulator (urxvt) window with fzf:

    urxvt -name "fzf-menu" -geometry 80x24 -e ~/bin/menu/scripts/mpcmenu fzf

We set an interface name for our urxvt window so we can allow a window manager to specifically manage these menus. For example, [bspwm](https://github.com/baskerville/bspwm) allows us to set rules for window interfaces:

    bspc rule -a fzf-menu floating=on,center=on,monitor=LVDS1,follow=on

With this, my fzf-enabled menus will be floating and centered on my laptop monitor. It will also focus itself if I run it from any other monitor.

A useful concept relevant to these scripts are [semaphores](https://en.wikipedia.org/wiki/Semaphore_(programming)) (in particular, lockfiles). What lockfiles will allow use to do is to ensure that there is only one running menu at a time. There are many tools to do this: [lockfile](http://linux.die.net/man/1/lockfile), [flock](http://linux.die.net/man/1/flock), and some [homegrown](http://stackoverflow.com/questions/185451/quick-and-dirty-way-to-ensure-only-one-instance-of-a-shell-script-is-running-at) (albeit flawed) solutions. `lockwrap` is an extremely simple script that will pass your command to flock under a predefined lock name:

Inside lockwrap:

    #!/bin/sh
    flock -n /tmp/menusuite.lock -c $@

Usage:

    $ ./lockwrap ./mpcmenu
    $ ./lockwrap alsamixer

If these commands are immediately run one after another, alsamixer will not be opened as mpcmenu would be using the lockfile. I've accidentally held down one of my hotkeys that opens these menus causing a dozen terminals/menus to open. The addition of lockfiles will take care of mishaps like that.
