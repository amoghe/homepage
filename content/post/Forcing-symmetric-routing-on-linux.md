+++
date = "2016-08-21T23:01:25-07:00"
draft = true
title = "Forcing symmetric routing on (ubuntu) linux"

+++

Lets say you have a Linux host that has multiple interfaces. Now you'd like to put both interfaces on the same
network say `192.168.168/24`. Furthermore, you'd like to ensure that traffic entering on one interface `192.168.168.101`
returns via that interface, and traffic entering on the other interface `192.168.168.102` leaves only through that
interface.

Now, you might think this is how linux networking would behave without any special configuration. But you'd be wrong.

First, lets prove to ourselves that this is not how things work if left to the defaults. As we go along, I'll show you
what a freshly installed system running Ubuntu 12.04 looks like (and how it behaves). The same applies to Ubuntu 14.04.
I haven't tested this on newer releases yet.

This system has 3 interfaces. Two of them are on the `192.168.168.0/24` network. A third one is on the `10.0.0.0/16`
network. The `10.0.0.0/16` network provides internet connectivity.

The IP addresses of the interfaces on the box are:

- `192.168.168.102`
- `192.168.168.103`
- `10.0.4.15`
