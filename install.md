# Installation

For testers with an unlocked Xiaomi Mi 8 bootloader.

Releases publish a `dipper-boot-<version>.img` (kernel + DTB + Mobian
initramfs). The rootfs is **not** shipped here — use a stock Mobian
sdm845 weekly rootfs and overlay the `userspace/` tree from this repo
on top of it before flashing.

## Prepare the rootfs (one-time)

1. Download a stock Mobian sdm845 phosh weekly rootfs from
   https://images.mobian.org/sdm845-phosh/weekly/.
2. Mount it locally (loopback) and copy the contents of `userspace/`
   from this repo into the rootfs:

   - `userspace/ucm2/` → `/usr/share/alsa/ucm2/`
   - `userspace/droid-juicer/xiaomi,dipper.toml` →
     `/usr/share/droid-juicer/devices/`
   - `userspace/systemd/dipper-call-audio-workaround.service` →
     `/etc/systemd/system/`
   - `userspace/systemd/user/mobi.phosh.OSK.service.d/` →
     `/etc/systemd/user/mobi.phosh.OSK.service.d/`
   - `userspace/scripts/dipper-call-audio-workaround` →
     `/usr/local/bin/` (chmod +x)

3. Unmount and keep the modified rootfs image ready to flash.

## Flash

1. Back up Android partitions first.
2. Download `dipper-boot-<version>.img` and `SHA256SUMS` from Releases
   and verify checksums:

   ```sh
   sha256sum -c SHA256SUMS
   ```

3. Boot the device into fastboot (power + volume up + volume down).
4. Flash:

   ```sh
   fastboot flash userdata mobian-dipper-rootfs.img
   fastboot flash boot     dipper-boot-<version>.img
   fastboot reboot
   ```

## First boot

`droid-juicer` extracts firmware from the device's own `vendor` /
`modem` / `bluetooth` / `persist` partitions, runs `update-initramfs`
to include that firmware in the initrd, and rewrites `boot.img` on the
boot partition. Audio, modem and GPU acceleration may not be fully
available on this boot.

Reboot once. From the second boot on, all firmware is loaded from the
initramfs at startup, `adsp` / modem / GPU come up automatically, and
audio works.

Exact filenames and device-specific notes should be checked against the
release notes for the image being tested.
