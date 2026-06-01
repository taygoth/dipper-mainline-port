# Xiaomi Mi 8 (dipper) mainline Mobian port

Experimental Mobian/mainline Linux port for Xiaomi Mi 8 (`dipper`).

Tested device:

- Model: Xiaomi Mi 8 (`dipper`)
- Board string: `Xiaomi Technologies, Inc. Dipper new MP v2.1`
- Kernel base: Mobian `linux-6.12-sdm845` (`6.12.91`)

Other Mi 8 board and camera variants may need DTS changes, especially camera
GPIOs, regulators and sensor I2C addresses.

This is a preview build. Installing it replaces the Android userdata/rootfs.
Keep backups of Android partitions before testing.

## Status

| Component | Status | Notes |
| --- | --- | --- |
| Display | Working | Samsung EA8074 DSI command mode panel |
| Touchscreen | Working | ST FTM5, local cleanup still needed |
| Volume keys | Working | GPIO keys |
| Wi-Fi | Working | Firmware extracted on first boot by droid-juicer |
| Bluetooth | Working | Pairing and A2DP tested |
| Bluetooth audio | Working | Headphones tested |
| USB-C dual-role / PD | Working | Sink + host + role-swap + DRP dock + PD identity ACK |
| Charging | Working | Battery charging works |
| Battery reporting | Working | Fuel gauge works in Phosh |
| Modem | Working | Calls, SMS, USSD and mobile data tested |
| Speaker audio | Working | TAS2557 smart amplifier |
| Microphones | Working | Built-in mic path tested; voice-call tuning still needs cleanup |
| Voice calls | Working | Works with stock q6voice modules and UCM routing |
| Rear main camera | Partial | IMX363 RAW capture works; actuator/OIS not supported yet |
| IR camera | Partial | Media graph builds; stream not working yet |
| Other cameras | Partial | Bring-up in progress |
| Sensors | Untested | Needs audit |
| Suspend | Untested | Needs audit |

## Layout

```text
kernel/
  patches/       Kernel patch series (apply in order)

userspace/
  droid-juicer/  First-boot firmware extraction config
  ucm2/          ALSA UCM2 profiles
  systemd/       Runtime unit snippets and a Phosh OSK drop-in
  scripts/       dipper-call-audio-workaround (voice-call glue)
```

Ready-to-test images should be published as release assets, not committed to
this repository.

## Firmware

Proprietary firmware blobs are not redistributed here. The
`userspace/droid-juicer/xiaomi,dipper.toml` config extracts the required files
from the device's own `vendor`, `modem`, `bluetooth` and `persist` partitions
on first boot.

## Install

See `install.md`.

## Known Issues

See `known-issues.md`.
