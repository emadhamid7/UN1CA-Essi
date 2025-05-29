# Installing UN1CA for Exynos 1280 devices

## Requirements

- A computer or an external media with at least 6 GB of free space and exFAT format.
  - If working with an external media, exFAT format is a **must**. FAT32 has a 4 GB file size limit, which the UN1CA builds well exceed, and NTFS is read-only on the phone.
  - If working with a laptop, make sure you plug it in just in case. If you're travelling, make sure you disable sleep on lid closure just in case. Refer to your OS's/desktop environment's documentation for more info.
- A custom recovery (OrangeFox preferred).
- A data cable for connecting the phone to a computer if working with one.
- At least 20% of charge on the phone.
- ADB installed and fully working on the computer if working with one.
  - For devices that can't flash the required firmware, a working installation of Fastboot is a requirement too.
  - You can get both of them from either Google's website ([platform-tools](https://developer.android.com/tools/releases/platform-tools)) ([Google USB drivers for Windows](https://developer.android.com/studio/run/win-usb)) or your preferred package repository. For example, Ubuntu users can install `android-sdk-platform-tools` and add themselves to `plugdev` group while NixOS users should enable `packages.adb.enable`, add `adbusers` to `users.users.<username>.extraGroups` and perform `nixos-rebuild switch`.
- Optionally a full data backup unless you feel experimental.
- Brain, time and patience.

## Installation

Download the UN1CA firmware, custom recovery and disabled vbmeta. Once that's done, flash your custom recovery the way you prefer (either via flashing ZIP from another custom recovery or using Odin) and boot into it.

Also flash the required firmware as seen in `firmwares.md`. If you don't have that firmware or are using another variant of the same device, flash your latest stock One UI firmware that matches the UN1CA version (E.g. One UI 6.1 for UN1CA One UI 6.1.1).

This procedure consists of two sections and an optional extra step. Please read each section carefully.

### If working with a computer with Platform Tools installed

Connect your phone to the computer and head over to ADB sideload.

- In TWRP, ADB sideload is under Advanced.
- In Orangefox, ADB sideload is under Menu.

Open a command prompt/terminal window (On Windows, you have to Shift+right click to reveal "Open a PowerShell window here" option) in the folder you downloaded UN1CA into (usually the downloads folder) and give it this command:

```
adb sideload UN1CA_X.Y.Z-a1b2c3d4_YYYYMMDD_device-sign.zip
```

> [!NOTE]
> On Windows, you might have to give full path to ADB when using ADB commands. This can be worked around by adding its folder to the "Path" variable under `sysdm.cpl` > Advanced tab > Environment variables...
>
> On Linux and macOS, you can do the same step by `export PATH=/path/to/platform-tools:$PATH`. The `:$PATH` notation here is important as that will make your shell preserve the previous PATH directories and save you from losing access to other tools. If you installed `adb` and `fastboot` from your package manager, you don't have to do this since it's already installed into a directory that's contained in your PATH variable.

If you could download and flash the required firmware beforehand, you should be able to boot into the system. If not, skip to "I don't have such a firmware version for my CSC" section.

### If working with an external media

Insert or plug in your external media that has UN1CA flashable ZIP on it.

In TWRP, head over to "Install" and locate the flashable. Tap on it and swipe on the slider.

In Orangefox, you just need to locate the flashable. Tap on it and swipe on the slider.

If you could download and flash the required firmware beforehand, you should be able to boot into the system. If not, skip to "I don't have such a firmware version for my CSC" section.

### I don't have such a firmware version for my CSC!

First of all, don't panic. It's not the end of the world. Just because your specific CSC/variant doesn't have the required firmware version doesn't mean you can't install and use UN1CA.

Now, if you've calmed down, let's get to unpacking your stock firmware and flashing the `vendor` image from it.

First, extract the AP archive from your stock firmware. More specifically, we'll need `super.img.lz4` from it.

Once that's done, we'll have to uncompress the LZ4 archive. Linux and macOS can install the `lz4` package from their respective repositories and do `lz4 super.img.lz4 super.img` while Windows users should use [7-Zip-zstd](https://github.com/mcmilk/7-Zip-zstd).

After that, we'll need `lpunpack`. Linux and macOS users should get this with the respective packages (For example, that's the `android-tools` package on NixOS). If you're a Windows user or your distribition doesn't package `lpunpack`, you can alternatively make use of [a Python script with the same name](https://github.com/unix3dgforce/lpunpack).

```
lpunpack -p vendor super.img .
```

Or, if using the Python script,

```
python3 lpunpack.py -p vendor super.img
```

Once that's done, reboot your phone to Fastboot mode from the custom recovery and run this:

```
fastboot flash vendor vendor.img
```

If all goes well, you should be able to boot into the system! Enjoy!
