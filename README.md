# Base system

## File systems

I set up a separate `/home` directory so the arch install can be blown away if needed without having to remove my personal files.

On a Windows dual boot system, I highly recommend using btrfs instead of ext4, because there are fantastic drivers for btrfs available.
Otherwise it doesn't really matter which one you use.
If you use btrfs, install `btrfs-progs`!

## Common packages

The common packages I include are the following:
```
base
base-devel          # for building packages and using the Arch build system
git                 # why it's not included in base-devel, I don't know
vim                 # or nano, emacs, or whatever your preference for a text editor
linux               # other kernel options are available, like linux-lts
linux-firmware      # you'll need the matching firmware option for your kernel
dos2unix            # convert line endings
inxi                # hardware info, useful for troubleshooting
pciutils
usbutils
pigz                # parallelized alternative to GNU gzip
zip                 # zip file compression
unzip               # decompression sold separately, apparently
```

Other packages that may be useful to have are

```
linux-lts           # can be useful to have as a fallback if the latest kernel sucks
linux-lts-firmware  # only needed
htop
gdisk               # if you prefer it to fdisk
networkmanager      # you could use systemd-networkd, but KDE has much better support for NetworkManager
bluez               # Bluetooth support
pacman-contrib      # mostly for pactree, which shows package dependencies
```

## Boot loader

`grub` is my go-to. You probably also want
```
efibootmgr          # for EFI support
os-prober           # for detecting other operating systems like Windows
```
`os-prober` needs to be explicitly enabled in `/etc/default/grub`.

## Drivers

You definitely want a microcode package (`amd-ucode` or `intel-ucode`)

## Audio

PulseAudio was the default until PipeWire came around. To get it working, you definitely want
```
pipewire
pipewire-pulse
wireplumber
```
and you might also want
```
pipewire-alsa
pipewire-jack
alsa-tools
alsa-utils
```

## Locale

I prefer 24-hour time in ISO 8601 format to the default US time format.
If you like this, add the following line to `/etc/locale.conf`:
```
LC_TIME=C.UTF-8
```

## User accounts

### Skeleton files

You probably want to set up default config files for any new users in `/etc/skel/`.

Grab my custom config files [here](https://github.com/brainandforce/my-configs), but it'll be a bit of work to install them since they're not hidden and need to be renamed.

### User creation

By default, `useradd` does not create a home directory for a new user unless the `-m` flag is provided.
This can be changed by adding the following line to `/etc/login.defs`:
```
CREATE_HOME yes
```
This line already exists in the stock Arch version of this file, but is commented out.

When creating a user with root privileges, pass the `-G wheel` option to add them to the admin (`wheel`) group.

### `sudo` configuration

I add my user to the `wheel` group, and in `/etc/sudoers` there is an option to allow users in `wheel` to use `sudo` for any command without a password.
There is also an alternate line that does the same thing, but does require a password.

If you insist on a password, I recommend including these lines in `/etc/sudoers`:
```
Defaults lecture=always
Defaults insults
```
There's also `opendoas` if you want something other than `sudo` for privilege escalation.

## AUR helpers

I use `yay` as my AUR helper, since it lets you pass through all `pacman` flags.

**Install your AUR helper when setting up the system.**
Per Arch policy, AUR helpers are not allowed in the `core` or `extra` repos, because users should always manually inspect the contents of a `PKGBUILD` before building the package in case they try to do something crazy, like [delete everything.](https://github.com/ValveSoftware/steam-for-linux/issues/3671)
For the same reason, Arch's build system does not allow you to build packages as root!
You'll need to create a normal user (with admin privileges) to install an AUR helper.

To build `yay`, switch to a non-root user, clone the repo (https://aur.archlinux.org/yay.git), and invoke `makepkg -si`.

**TODO: Test using the `nobody` user to invoke `makepkg -si` as root before creating an account.**

# Graphical interface

## KDE

KDE is my default choice of desktop environment for Linux.

If you want the full-featured system ready to go, just install `plasma-meta`; otherwise you can build your own minimalist KDE with `plasma-desktop` as the foundation. You will very likely also want to include:
```
plasma-pa                               # PulseAudio integration and soundd widget; kmix is deprecated 
networkmanager-qt                       # support for NetworkManager
plasma-systemmonitor                    # task manager, accessible with Meta+Esc
firefox                                 # or your browser of choice
konsole                                 # or some other terminal emulator
dolphin                                 # or some other file manager
kdenetwork-filesharing                  # support for network file shares
ark                                     # or some other archive manager
khelpcenter                             # help files
gwenview                                # for image viewing, but a browser can also handle that
powerdevil                              # control monitor brightness
kdeconnect                              # connect to a phone (download the KDE Connect app on it)
kwalletmanager                          # disable wallet popups
breeze-gtk                              # KDE Breeze theme for GTK applications
```

## Interface fonts

[Inter](https://fonts.google.com/specimen/Inter) (`inter-font`) is an excellent UI font with solid Unicode coverage; I pair it with [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono?query=Roboto+Mono) (`ttf-roboto-mono`) as the monospace font.

For web browsing, I like [IBM Plex](https://ibm.com/plex) (`ttf-ibm-plex`) as the default serif, sans serif, and monospace as an alternative to Arial/Times/Courier.
They are very consistent across a single page, as evidenced by https://ultimatemotherfuckingwebsite.com/.

## Greeter/display manager

The recommended greeter for KDE is SDDM, and it's pretty easy to integrate with other desktop environments as well.
You definitely want to get `sddm-kcm` if you want to configure it within the KDE System Settings.
The Breeze theme can be selected there and synchronized with your KDE settings for a uniform look.

Note that the settings for the SDDM greeter and your personal lock screen are in different places!
The SDDM wallpaper choice is global, and the lock screen wallpaper is only seen when you lock your session.

**TODO: figure out how to make the clock font thinner in the Breeze SDDM theme!**

KDE is also offering a new display manager, Plasma Login Manager.
Currently it's still in beta, but can be installed from the AUR (`plasma-login-manager-git`).

# Extras

## Discord

Discord is available with a few different implementations, but the easiest is just `discord`.
There is also `discord_arch_electron` which uses a system Electron implementation.

You also want to install `libunity` to get the notification icon to show up in the taskbar, even though it is not listed as an optional dependency.
`libappindicator` is also recommended to get the system tray icon to work.

## Fonts

`ttf-courier-prime` is a really good Courier.

## Visual Studio Code

There are a few package available that provide a build of VS Code:
```
extra/code                              # Arch Linux build
aur/visual-studio-code-bin              # Official Microsoft build
```
Microsoft's release version is actually *not* free software, despite the source being under an MIT license.
They also restrict the use of their extension store to their builds, and the open-source releases do not enable an extension store by default.
It is possible to use the [VSX]

Note that some extensions to 

