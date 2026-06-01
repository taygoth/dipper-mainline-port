# Known Issues

- Tested only on `Dipper new MP v2.1`; other Mi 8 camera/board variants may
  need DTS changes.
- USB-C / PD: when a DRP dock has its own PD charger plugged into its
  upstream port, the phone does not auto-initiate PR_Swap. The phone
  keeps sourcing VBUS to the dock and draining its own battery. Work
  around it by writing `sink` to the phone's typec power_role node:
  `echo sink > /sys/class/typec/port0/power_role`. This is a userspace
  policy gap — a small udev/systemd helper will land separately.
- Rear IMX363 RAW capture works, but actuator/VCM and OIS are not supported
  yet.
- IR/front/secondary camera support is incomplete.
- Voice calls work, but UCM/voice-call tuning still needs review.
- Some kernel patches are local-port quality and are not upstream-ready.
- Suspend and full sensor support still need audit.
