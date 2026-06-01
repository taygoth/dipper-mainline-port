# Kernel patches

Patch series for the Xiaomi Mi 8 (`dipper`) Mobian port.

Base: Mobian `linux-6.12-sdm845` 6.12.91-1 source tree (linux-6.12.91
with the Mobian patch stack applied). The series does not apply to a
vanilla `linux-6.12.91` tree, because it depends on the Mobian
downstream STM/FTS touchscreen driver and the Mobian-specific imx363
driver landing already being in the tree.

## Layout

Two independent series under `patches/`:

```
patches/
├── dipper-port/        Per-device support for Xiaomi Mi 8 (dipper)
│   ├── 0000-cover-letter.patch
│   └── 0001..0009     Touchscreen, panel, camera, audio, board DT, defconfig
└── tcpm-pmi8998/       Cross-device PMI8998 USB Type-C TCPM driver
    ├── 0000-cover-letter.patch
    └── 0001..0004     Backend refactor, port handler, SoC DT, OnePlus DT
```

The two series are independent at apply time. `dipper-port/` adds the
Mi 8 board DT (which itself enables Type-C through the PMI8998 TCPM
driver) plus all Mi 8-specific drivers. `tcpm-pmi8998/` adds the
PMI8998 TCPM port handler itself and enables it on the OnePlus 6 / 6T
(`sdm845-oneplus-common.dtsi`). The dipper board DT in `dipper-port/`
references the PMI8998 typec node, so to actually use Type-C on a
dipper both series must be applied together. The OnePlus boards work
with `tcpm-pmi8998/` alone.

## Apply order

Apply `tcpm-pmi8998` first, then `dipper-port`:

```
git am patches/tcpm-pmi8998/000[1-9]-*.patch
git am patches/dipper-port/000[1-9]-*.patch
```

The `0000-` files are cover letters for review only — skip them at
apply time or `git am` will stop on an empty patch.

The order matters: `dipper-port/0008` (the Mi 8 board DTS)
references `&pmi8998_typec` and `&pmi8998_vbus`, which are added by
`tcpm-pmi8998/0003`. If `dipper-port` is applied first, the
intermediate tree compiles the C code but cannot build
`sdm845-xiaomi-dipper.dtb` until `tcpm-pmi8998` lands.

The new PMI8998 compatibles introduced by `tcpm-pmi8998/0003`
(`qcom,pmi8998-typec`, `qcom,pmi8998-vbus-reg`) are not yet
documented in `Documentation/devicetree/bindings/`. For a Mobian
carry this is acceptable; a follow-up patch adding the binding
update is tracked separately and would be required for upstream
review.

## Series 1: dipper-port (9 patches)

| # | Topic |
| --- | --- |
| 0000 | Cover letter |
| 0001-0003 | Mobian downstream FTM5 touchscreen driver — three fixes |
| 0004 | dt-bindings: Samsung EA8074 panel binding |
| 0005 | drm/panel: Samsung EA8074 driver |
| 0006 | media: i2c: imx363 — start MCLK before supplies |
| 0007 | ASoC: codecs: TAS2557 smart amplifier driver |
| 0008 | arm64: dts: qcom: Xiaomi Mi 8 (dipper) board DTS |
| 0009 | arm64: configs: sdm845_defconfig — enable dipper modules |

### Built modules

After applying this series, the dipper board uses these modules
(in addition to the stock Mobian sdm845 set):

- `panel-samsung-ea8074`
- `ftm5` (`CONFIG_TOUCHSCREEN_STM_FTS_DOWNSTREAM=m`)
- `imx363` (with the reordered power-up sequence)
- `snd-soc-tas2557`

### Notes

Some patches are port-quality and not upstream-ready: the FTM5 fixes
target the Mobian downstream driver (not mainline). TAS2557 is a
local port driver for the legacy TI firmware format; the binding and
upstream story are still incomplete (the dipper DT uses
`compatible = "ti,tas2557"` without a matching YAML binding).
Upstreaming for the EA8074 panel driver and the IMX363 power-on fix
is tracked separately from this Mobian-bound port.

## Series 2: tcpm-pmi8998 (4 patches)

| # | Topic |
| --- | --- |
| 0000 | Cover letter |
| 0001 | usb: typec: qcom-pmic: dispatch port backend via resource table |
| 0002 | usb: typec: qcom-pmic: add PMI8998 port handler |
| 0003 | arm64: dts: qcom: pmi8998: add Type-C and VBUS regulator nodes |
| 0004 | arm64: dts: qcom: sdm845-oneplus-common: enable Type-C |

## Hardware validation

| Series | Device | Status |
| --- | --- | --- |
| dipper-port | Xiaomi Mi 8 (dipper) | End-to-end on the "new MP v2.1" board variant |
| tcpm-pmi8998 | Xiaomi Mi 8 (dipper) | Sink + host + role-swap + DRP dock + PD identity ACK |
| tcpm-pmi8998 | OnePlus 6 (enchilada) | Untested |
