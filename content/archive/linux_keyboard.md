---
title: "Linux Keyboard Configure"
description: "Linux键盘配置"
tags:
- linux
- keyboard
---


> 之前一直在使用LControl和CapsLock交换的Keyboard Layout，但还没好好清晰地理一遍Linux相关的Keyboaed设置，此文章使用Linux发行版为Kali，以Debian为基础

## 相关包

### `systemd`

在大多数的发行版下，在使用`systemd`作为`init system`，可以使用其中工具`localectl`设置`CLI`及`GUI`的键盘布局

> 在Debian下，安装`console-data`，键盘布包会使用`kmap`为文件后缀，但`localectl`使用`map`为后缀，所以Debian建议使用`dpkg-reconfigure console-data`及`dpkg-reconfigure keyboard-configuration`进行配置，相关ISSUS https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=790955

```bash
$ localectl --help
localectl [OPTIONS...] COMMAND ...

Query or change system locale and keyboard settings.

Commands:
  status                   Show current locale settings
  set-locale LOCALE...     Set system locale
  list-locales             Show known locales
  set-keymap MAP [MAP]     Set console and X11 keyboard mappings
  list-keymaps             Show known virtual console keyboard mappings
  set-x11-keymap LAYOUT [MODEL [VARIANT [OPTIONS]]]
                           Set X11 and console keyboard mappings
  list-x11-keymap-models   Show known X11 keyboard mapping models
  list-x11-keymap-layouts  Show known X11 keyboard mapping layouts
  list-x11-keymap-variants [LAYOUT]
                           Show known X11 keyboard mapping variants
  list-x11-keymap-options  Show known X11 keyboard mapping options

Options:
  -h --help                Show this help
     --version             Show package version
     --no-pager            Do not pipe output into a pager
     --no-ask-password     Do not prompt for password
  -H --host=[USER@]HOST    Operate on remote host
  -M --machine=CONTAINER   Operate on local container
     --no-convert          Don\'t convert keyboard mappings

See the localectl(1) man page for details.
$ strace localectl list-keymaps 2>&1 | grep lstat
lstat("/usr/share/keymaps",...
lstat("/usr/share/kbd/keymaps",...
lstat("/usr/lib/kbd/keymaps",...
lstat("/lib/kbd/keymaps",...
```

### `kbd`

提供在`TTY`/`Console`提供底层键盘工具

```bash
$ apt-file list kbd | grep bin
kbd: /bin/chvt
kbd: /bin/dumpkeys
kbd: /bin/fgconsole
kbd: /bin/kbd_mode
kbd: /bin/loadkeys
kbd: /bin/openvt
kbd: /bin/setfont
kbd: /bin/unicode_start
kbd: /sbin/kbdrate
kbd: /usr/bin/codepage
kbd: /usr/bin/deallocvt
kbd: /usr/bin/getkeycodes
kbd: /usr/bin/kbdinfo
kbd: /usr/bin/loadunimap
kbd: /usr/bin/mapscrn
kbd: /usr/bin/mk_modmap
kbd: /usr/bin/psfaddtable
kbd: /usr/bin/psfgettable
kbd: /usr/bin/psfstriptable
kbd: /usr/bin/psfxtable
kbd: /usr/bin/resizecons
kbd: /usr/bin/screendump
kbd: /usr/bin/setkeycodes
kbd: /usr/bin/setleds
kbd: /usr/bin/setlogcons
kbd: /usr/bin/setmetamode
kbd: /usr/bin/setvtrgb
kbd: /usr/bin/showconsolefont
kbd: /usr/bin/showkey
kbd: /usr/bin/splitfont
kbd: /usr/bin/unicode_stop
kbd: /usr/sbin/setvesablank
kbd: /usr/sbin/vcstime
```

配置文件`/etc/vconsole.conf`


### `x11-xkb-utils`

提供在`X11`下的底层键盘工具（`X11`为Linux主流的`GUI`协议标准）

```bash
$ apt-file list x11-xkb-utils | grep bin
x11-xkb-utils: /usr/bin/setxkbmap
x11-xkb-utils: /usr/bin/xkbbell
x11-xkb-utils: /usr/bin/xkbcomp
x11-xkb-utils: /usr/bin/xkbevd
x11-xkb-utils: /usr/bin/xkbprint
x11-xkb-utils: /usr/bin/xkbvleds
x11-xkb-utils: /usr/bin/xkbwatch
```

配置文件`/etc/X11/xorg.conf.d/*`

## 常用设置

### CapsLock及LControl互换

#### GUI
```bash
$ grep -E "(ctrl|caps):" /usr/share/X11/xkb/rules/base.lst
  ctrl:nocaps          Caps Lock as Ctrl
  ctrl:lctrl_meta      Left Ctrl as Meta
  ctrl:swapcaps        Swap Ctrl and Caps Lock
  ctrl:swapcaps_hyper  Caps Lock as Control, Control as Hyper
  ctrl:ac_ctrl         At left of 'A'
  ctrl:aa_ctrl         At bottom left
  ctrl:rctrl_ralt      Right Ctrl as Right Alt
  ctrl:menu_rctrl      Menu as Right Ctrl
  ctrl:swap_lalt_lctl  Swap Left Alt with Left Ctrl
  ctrl:swap_lwin_lctl  Swap Left Win with Left Ctrl
  ctrl:swap_rwin_rctl  Swap Right Win with Right Ctrl
  ctrl:swap_lalt_lctl_lwin Left Alt as Ctrl, Left Ctrl as Win, Left Win as Left Alt
  caps:internal        Caps Lock uses internal capitalization; Shift "pauses" Caps Lock
  caps:internal_nocancel Caps Lock uses internal capitalization; Shift does not affect Caps Lock
  caps:shift           Caps Lock acts as Shift with locking; Shift "pauses" Caps Lock
  caps:shift_nocancel  Caps Lock acts as Shift with locking; Shift does not affect Caps Lock
  caps:capslock        Caps Lock toggles normal capitalization of alphabetic characters
  caps:shiftlock       Caps Lock toggles ShiftLock (affects all keys)
  caps:swapescape      Swap ESC and Caps Lock
  caps:escape          Make Caps Lock an additional Esc
  caps:escape_shifted_capslock Make unmodified Caps Lock an additional Esc, but Shift + Caps Lock behaves like regular Caps Lock
  caps:backspace       Make Caps Lock an additional Backspace
  caps:super           Make Caps Lock an additional Super
  caps:hyper           Make Caps Lock an additional Hyper
  caps:menu            Make Caps Lock an additional Menu key
  caps:numlock         Make Caps Lock an additional Num Lock
  caps:ctrl_modifier   Caps Lock is also a Ctrl
  caps:none            Caps Lock is disabled
$ setxkbmap -option ctrl:swapcpas  # enbale swap caps and ctrl, multiple option sep with `,`
$ setxkbmap -option  # disable xkb options
```

### CLI

TODO...

```bash
$ cat personal.map
...
keycode 29 = Caps_Lock
keycode 58 = Control
...
$ loadkeys personal.map
```

### 参考资料

- [keyboard configure](https://wiki.archlinux.org/title/Category:Keyboard_configuration)
- [linux console/keyboard](https://wiki.archlinux.org/title/Linux_console/Keyboard_configuration)
- [xorg/keyboard](https://wiki.archlinux.org/title/Xorg/Keyboard_configuration)
