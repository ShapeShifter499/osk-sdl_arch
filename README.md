# osk-sdl_arch
Getting osk-sdl working to help decrypt tablets and other touchscreen enabled devices running x86_64 bit Arch Linux.

Forked PKGBUILD and related files originally from https://github.com/dreemurrs-embedded/Pine64-Arch

osk-sdl main project, code, and bug reports can be found at https://gitlab.com/postmarketOS/osk-sdl

Issues about how this package works can be filed here. 

Issues regarding how osk-sdl works in general should be brought up with the official repository at: https://gitlab.com/postmarketOS/osk-sdl/-/issues

## Installation

```sh
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/packages/osk-sdl
cd osk-sdl
makepkg -fsri
```

## Configuration

Make sure osk-sdl is installed to your system and then add osk-sdl to your ```/etc/mkinitcpio.conf``` hooks.
osk-sdl should be inserted before the ```filesystems``` hook but after ```udev``` or ```systemd``` hook.
Optionally include the ```keyboard``` and ```keymap``` hooks if your device has a detachable keyboard or if you sometimes want to plug in a USB or OTG keyboard for passphrase input.

Example:
<pre>
 HOOKS=(base udev autodetect keyboard keymap consolefont modconf block lvm2 <b>osk-sdl</b> filesystems fsck)
</pre>

Next you will want to make sure you incude the necessary kernel modules to support the display and touchscreen. 
Add those to your ```/etc/mkinitcpio.conf``` modules.

Example list of modules taken from a HP Pro Tablet 408 G1.
```i915``` for the Intel graphics
```hid_multitouch```,```i2c_hid```, ```i2c_hid_acpi``` for touchscreen support
**THESE WILL BE DIFFERENT DEPENDING ON YOUR DEVICE**
<pre>
MODULES=(i915 hid_multitouch i2c_hid i2c_hid_acpi)
</pre>

Generate a new init with ```mkinitcpio -P```


Lastly make sure you've added the proper device to your kernel command line. mkinitcpio generation includes variables from the kernel command line.
<pre>
 cryptdevice=<i>device</i>:<i>dmname</i>

* <i>device</i> is the path to the device backing the encrypted device. Usage of <a href="https://wiki.archlinux.org/title/Persistent_block_device_naming">persistent block device naming</a> is strongly recommended.

* <i>dmname</i> is the <b>d</b>evice-<b>m</b>apper name given to the device after decryption, which will be available as "/dev/mapper/<i>dmname</i>"

* If a LVM contains the <a href="https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LUKS_on_LVM">encrypted root</a>, the LVM gets activated first and the volume group containing the logical volume of the encrypted root serves as <i>device</i>. It is then followed by the respective volume group to be mapped to root. The parameter follows the form of "cryptdevice=<i>/dev/vgname/lvname</i>:<i>dmname</i>".
</pre>
