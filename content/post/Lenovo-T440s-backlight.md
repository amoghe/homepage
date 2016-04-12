+++
date  = "2015-04-11T22:59:57-07:00"
draft = false
title = "Fix screen brightness control keys on a (2014) Lenovo T440s"
+++

The hardware keys for brightness control on my Lenovo T440s (2014) worked at a very coarse granularity. Hitting
the brightness keys would cause it to switch between 4 levels of brightness. I found a solution [here][1] which
worked for me.

Simply add this to the GRUB_CMDLINE_LINUX_DEFAULT line in `/etc/default/grub` :

```bash
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor quiet splash"
```

and run

```bash
sudo update-grub
```

Reboot and you will be able to change the backlight brightness in finer steps.

__Update__: On a recent installation of 14.04.3 on a 2015 T440s, I didn't have to do anything to get this to
work. Seems like it was fixed upstream.

[1]: http://blog.karssen.org/2014/05/10/fixing-backlight-control-with-ubuntu-on-my-thinkpad-t440s/
