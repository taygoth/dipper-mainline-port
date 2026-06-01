# Installation

For testers with an unlocked Xiaomi Mi 8 bootloader.

Releases provide an archive with:

- `boot.img`
- a Mobian rootfs image (or instructions referencing a stock Mobian image)
- `SHA256SUMS`

## Flow

1. Back up Android partitions before installing.
2. Download the preview archive from Releases and verify checksums.
3. Boot the device into fastboot.
4. Flash the rootfs to `userdata` and the kernel to `boot`:

   ```sh
   sha256sum -c SHA256SUMS
   fastboot flash userdata mobian-dipper-rootfs.img
   fastboot flash boot     boot.img
   fastboot reboot
   ```

5. **First boot.** `droid-juicer` extracts firmware from the device's own
   `vendor` / `modem` / `bluetooth` / `persist` partitions, runs
   `update-initramfs` to include that firmware in the initrd, and rewrites
   `boot.img` on the boot partition. Audio, modem and GPU acceleration may
   not be fully available on this boot.

6. **Reboot once.** From the second boot on, all firmware is loaded from
   the initramfs at startup, `adsp` / modem / GPU come up automatically,
   and audio works.

Exact filenames and any device-specific notes should be checked against
the release notes for the image being tested.
