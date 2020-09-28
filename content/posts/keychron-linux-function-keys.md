---
title: "Keychron Linux Function Keys"
date: 2020-09-28T20:36:25Z
---

On Linux, the [Keychron K2](https://www.keychron.com/products/keychron-k2-wireless-mechanical-keyboard)
doesn't register any of the F1-F12 function keys as actual F keys, instead, 
treating them as multimedia keys by default. Here's how to fix it!<!--more-->

They keyboard has 2 modes:
Windows/Android and MacOS, but neither mode worked properly out of the box.

To fix this:

- Set the keyboard to Windows mode via the side switch
- Use `Fn + X + L` (hold for 4 seconds) to set the function key row to "Function" mode. (usually all that's necessary on Windows)
- `echo 0 | sudo tee /sys/module/hid_apple/parameters/fnmode`

Once complete, my F1-F12 keys work properly, and holding `Fn` turns them into
multimedia keys.
You can use the `evtest` utility to check how keyboard keys are registering until
you get the above combination of settings configured properly.


To persist this change, add a module option for `hid_apple`:

```
echo "options hid_apple fnmode=0" | sudo tee -a /etc/modprobe.d/hid_apple.conf
```

You may need to rebuild your `initramfs` if `hid_apple` is included.
- ubuntu: `sudo update-initramfs -u`
- arch: `mkinitcpio -P`

See also:
- [/u/fratdaddyZC's comment on reddit for the `fnmode` tip](https://www.reddit.com/r/MechanicalKeyboards/comments/d5io49/keychron_k2_f_keys_dont_work_w_linux_help/f0m2u14/)
- [Kurgol's keychron repo of tips](https://github.com/Kurgol/keychron/blob/master/k2.md)
- [K2 manual](https://drive.google.com/file/d/1PsAKfM4lJVGuHL4oAFW7r10ACMfwFRpG/view)

