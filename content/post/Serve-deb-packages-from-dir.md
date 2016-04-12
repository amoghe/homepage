+++
date  = "2016-04-12T00:13:27-07:00"
draft = false
title = "Serve debian packages from a directory"
+++

So you have some debian packages in a directory and you'd like to consume them as you would from an apt repo server?

Well, its quite simple because apt uses plain 'ole HTTP for everything it does. So we can serve the packages (and some
"special" files) over HTTP and pretend to be an apt server!

First, you'll need to install `dpkg-dev`. This package provides the tool to take a directory full of packages and
create a 'Packages' file. Think of a Packages file as an 'index' for the repo.

On my Ubuntu 14.04 system, I run

```bash
sudo apt-get install dpkg-dev
```

Next, I `cd` into the directory containing the packages, and run

```bash
dpkg-scanpkgs > Packages
```

This creates the 'index' for the packages in the dir. If you have a lot of packages in the dir, you might be better
off creating a compressed version of the file so that clients transfer fewer bytes on the wire.

```bash
dpkg-scanpkgs | gzip -9 > Packages.gz
```

Of course, this assumes you have the `gzip` utility available on your system.

__NOTE__: you only need to run one of the above commands.

Okay, so we have a `Packages` (or `Packages.gz`) file in the directory. Thats great, because `apt-get` expects to be
able to fetch that file in order to determine what packages the repo server can provide.

In order to actually pretend to be an apt server, this directory needs to be "served" by an HTTP server. For this, we
stand up a simple HTTP server (I'm using python, but you could use any of these [methods][1]).

```bash
python -m SimpleHTTPServer 8000
```

Great, so now we have an HTTP server running on port 8000.

Now head over to the "client" side where we'll twiddle apt settings so that it connects to the HTTP server we started.

In `/etc/apt/sources.list`, add

```bash
deb http://localhost:8000/ /
```

I'm using `localhost` in this example, but I often use this to serve packages inside docker containers, in which case
I point to the appropriate docker IP of the container serving the repo.

Now run

```bash
apt-get update
```

You'll see some messages indicating that certain files were not found (such as `Releases` and `Release.gpg`). Thats
okay since those aren't mandatory files. Whats important is that it finds the `Packages` (or its `.gz` variant).

Now try and install a package served by the server...

```bash
apt-get install <something>
```

Et Voila!

[1]: https://gist.github.com/willurd/5720255
