# Build~bott~ing UN1CA for Exynos 1280 devices

> [!WARNING]
> Only `m34x` is in active maintenance! Other devices are NOT guaranteed to build successfully!

## Requirements

- A machine strong enough to build AOSP Android 14 successfully running any Linux distro other than NixOS and its derivatives.
  - NixOS is exclusively out of here because UN1CA build system makes use of `sudo`, which defeats the purpose of sandboxed building. If you want to create a dependency set for NixOS, you're on your own.
  - Windows Subsystem for Linux (WSL) is acceptable, but you'll need to have twice the specs mentioned here since WSL only allocates half the specs into the VMs.
  - proot, chroot and Docker/Podman environments (such as containers, Distrobox) won't work since you won't be able to insert kernel modules and set up loop devices, which is a part of the building process.
  - VMware/VirtualBox/KVM/Proxmox/etc is fine, but make sure you allocate enough resources to the VM.
- Active internet connection.
- Package dependencies for UN1CA. This is mentioned in detail further down the documentation.
- (If building in VM, optional) Active filesystem access to the build system in case you want to transfer files elsewhere without relying on internet connection.
- [Last **One UI 6.1.1** firmware from **Galaxy S23 FE Europe** (SM-S711B/EUX)](https://samfw.com/firmware/SM-S711B/EUX/S711BXXS6DYBH).
- Last **One UI 6.1** firmware from your respective device.
  - Galaxy A53 5G Europe (SM-A536B/EUX) last got One UI 6.1 so there's no need to exclusively gather this outside of the build process.
  - Galaxy A25 Egypt (SM-A256E/EGY) [A256EXXS7BYC4/A256EOJM7BYC4/A256EXXS7BYC4](https://samfw.com/firmware/SM-A256E/EGY/A256EXXS7BYC4)
  - Galaxy M34 5G India (SM-M346B/INS): [M346BXXS7CYB2/M346BODM7CYB2/M346BXXS7CYB2](https://samfw.com/firmware/SM-M346B/INS/M346BXXS7CYB2)
  - Galaxy A33 5G
    - SM-A336E/INS last got One UI 6.1 so there's no need to exclusively gather this outside of the build process.
    - SM-A336B/EUX last got One UI 6.1 so there's no need to exclusively gather this outside of the build process.
  - Galaxy F34 (SM-E346B/INS): [E346BXXS7CYB1/E346BODM7CYB1/E346BXXS7CYB1](https://samfw.com/firmware/SM-E346B/INS/E346BXXS7CYB1)
- Brain, time and patience.

## Package dependencies

### For Ubuntu 24.04 Desktop with Default Selection

```
sudo apt update
sudo apt upgrade -y
sudo apt install -y git attr ccache clang ffmpeg golang libbrotli-dev libgtest-dev libprotobuf-dev libunwind-dev libpcre2-dev libzstd-dev linux-modules-extra-$(uname -r) lld protobuf-compiler webp openjdk-11-jdk nodejs npm curl cmake lz4 liblz4-dev xxhash libxxhash-dev android-sdk device-tree-compiler flex bison llvm brotli
```
```
git clone https://github.com/affggh/mkdtimg --depth=1
cd mkdtimg
make
sudo install mkdtimg /usr/local/bin/
cd -
```

## Configuring Git and gathering the sources

> [!NOTE]
> You don't NEED a GitHub account to be able to configure Git. You won't push repositories. The patching process needs Git to be configured so that it can use Git to apply the patches. Failure to do so will cause `Tell me who you are.` error and fail the build process.

```
git config --global user.name "Ichika Hoshino"
git config --global user.email "ichika@example.com"
```
```
git clone https://github.com/kurtbahartr/UN1CA-s5e8825 --recursive --recurse-submodules --depth=1
cd UN1CA-s5e8825
```

## Building

```
source buildenv.sh s5e8825 <codename>
```

Possible codenames;
- `a25x`
- `a33x`
- `a53x`
- `f34x`
- `m34x`

```
mkdir -p out/odin/SM-S711B_EUX
echo -n "S711BXXS6DYBH/S711BOXM6DYBH/S711BXXS6DYBH" > out/odin/SM-MS711B_EUX/.downloaded
```

Now unzip and copy over the firmware you downloaded into `out/odin/SM-S711B`. More specifically the AP, BL, CP and CSC (**NOT** HOME_CSC!) files.

Rinse and repeat for your target device. Refer to the requirements for the specific firmware to download and `echo -n` into the respective `.downloaded` file. For example, for Galaxy M34 5G, that would be:

```
mkdir -p out/odin/SM-M346B_INS
echo -n "M346BXXS7CYB2/M346BODM7CYB2/M346BXXS7CYB2" > out/odin/SM-M346B_INS/.downloaded
```

Once you've done all that, it's about time we get to the building process.

```
run_cmd make_rom
```

If you get `system.img.sparse already exists; not overwriting` or something similar, just remove the file it complains about from `out/fw/*` and continue on with the same command.

If all goes well, you should have your very own build of UN1CA in the `out` directory! Congrats!