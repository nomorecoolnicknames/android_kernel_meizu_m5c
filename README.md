# Linux kernel for the Meizu M5c — LineageOS 20

Linux 4.9 kernel for the Meizu M5c (MediaTek MT6737M), used for Android 13 /
LineageOS 20.

## Origin

- Base: the MediaTek ALPS Linux 4.9 release published at
  [nyancrimew/mtk-t-alps-release-q0-kernel-4.9-lc](https://github.com/nyancrimew/mtk-t-alps-release-q0-kernel-4.9-lc).
- MT6737M platform, board and driver code ported from MediaTek and Meizu
  kernel sources released under the GPL for this phone, plus new work for
  Linux 4.9 and Android 13.
- The battery profiles in
  `drivers/misc/mediatek/include/mt-plat/mt6735/include/mach/mt_battery_meter_table_m5c_stock.h`
  are the per-vendor tables of the stock M5c kernel (one set per battery
  supplier), not per-unit calibration.
- Original authorship and copyright notices are kept. The kernel is licensed
  under the GPL version 2; see [COPYING](COPYING). Upstream notes are in
  [README](README).

Some driver parameters and log messages carry a `forge_` / `FORGE` prefix.
They are part of the interface used by the device configuration and are kept
as they are.

## Status

Android 13 runs with hardware rendering. Suspend, Wi-Fi, camera, audio and
sensors are integrated. The battery uses the stock fuel-gauge tables; the stock
charge currents and charging temperature limits are not in this version.
Long-run acceptance of sleep and charging continues.

## Building

Use the AOSP GCC 4.9 toolchain (`aarch64-linux-android-4.9`).

```sh
mkdir -p out/include
ln -s "$PWD/include/dt-bindings" out/include/dt-bindings
make O="$PWD/out" ARCH=arm64 CROSS_COMPILE=aarch64-linux-android- m5c_defconfig
make O="$PWD/out" ARCH=arm64 CROSS_COMPILE=aarch64-linux-android- -j8 Image
gzip -n -9 -c out/arch/arm64/boot/Image > Image.gz
```

The phone boots `Image.gz` with its original stock device tree appended. The
text form of that device tree is `arch/arm64/boot/dts/mediatek/m5c-stock.dts`.
The defconfig also builds the reference `k37mv1_bsp_k49` device tree, whose
board pin configuration (`cust.dtsi`) is committed as generated text. The
device-tree rule uses the prebuilt host tool `scripts/dtc/dtc_overlay` from the
upstream release.

## Not included

- Binary board-configuration files (`*.dws`) and the DCT executable; the
  generated `cust.dtsi` is used instead.
- Prebuilt binaries of the upstream tree that this kernel does not use:
  firmware images (`firmware/*.ihex`), documentation images,
  `ufdt_apply_overlay`, `mkimage`, an audio archive and the module-signing
  test key.

No kernel image, proprietary firmware, device identity or per-unit
calibration data is included.
