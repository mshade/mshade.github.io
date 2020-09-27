+++
title = "Enabling Docker's Native Overlay Diff on Linux"
date = 2020-09-27T00:59:11Z
author = "Mike Shade"
authorTwitter = "mike_shade" #do not include @
cover = ""
tags = ["docker", "tech"]
keywords = ["", ""]
description = "Docker, slow builds, and `Native Overlay Diff: false`"
showFullContent = false
+++

# The Problem

Watching `docker build` on my workstation, I noticed that often the process
would pause between layers, seemingly waiting on something -- even layers that
should be pretty quick to apply.

## Clues

After fiercely googling, I came across these earlier clues and info:

- [Jarek Przygódzki's blog post](https://dev.to/jarekprzygodzki/a-curious-case-of-slow-docker-image-builds-2o7k)
- [A StackOverflow question](https://stackoverflow.com/questions/46787983/what-native-overlay-diff-mean-in-overlay2-storage-driver)
- [Kernel commit messages](https://github.com/torvalds/linux/commit/d47748e5ae5af6572e520cc9767bbe70c22ea498#diff-315c61b86e39b9b47f4ab0cd9efc2467)

In a nutshell, recent default kernel options result in Docker falling back to
a naive and slow (but reliable) method.

## Still broken

The commonly posted resolution, adding `redirect_dir=off` to the `overlay` module's options, no longer works alone. Something is still broken.

`docker info` still showed:
```shell
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: false
```

And in `journalctl -u docker`:
```
"Not using native diff for overlay2, this may cause degraded performance for building images: kernel has CONFIG_OVERLAY_FS_REDIRECT_DIR enabled" storage-driver=overlay2
```

## The fix

It turns out that more recent changes to the kernel require adding a second option
to the overlay module -- `metacopy=off`. Comments on the above blog post and 
StackOverflow question mention this, but I missed it at first!

Add the following to `/etc/modprobe.d/disable-overlay-redirect-dir.conf`:
```bash
options overlay metacopy=off redirect_dir=off
```

Then, you can stop docker, reload the module, and restart docker to check your new status:

```bash
systemctl stop docker
modprobe -r overlay
modprobe overlay
systemctl start docker
docker info
...
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
```

Hooray!
