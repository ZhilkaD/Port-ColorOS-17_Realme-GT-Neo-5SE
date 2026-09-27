# Port ColorOS 17 for Realme GT Neo 5 SE (senna_jr / RMX3700)

![Android](https://img.shields.io/badge/Android-17%20(cp2a)-blue?style=flat-square)
![Status](https://img.shields.io/badge/Build-UNOFFICIAL-orange?style=flat-square)
![SELinux](https://img.shields.io/badge/SELinux-Permissive-yellow?style=flat-square)
![GApps](https://img.shields.io/badge/GApps-Not%20Included-red?style=flat-square)

Unofficial port of **ColorOS 17 (Android 17)** for the **Realme GT Neo 5 SE** (`senna_jr` / `RMX3700`).
ColorOS 17 system half (OnePlus 13 dump by lddnsk) running on top of the stock RealmeUI vendor stack.
*Personal port, not affiliated with ColorOS / OnePlus / realme.*

* **System:** `CP2A.260605.016`, security patch `2026-09-01`, SDK 37
* **Vendor:** stock RealmeUI `RMX3701_16.0.5.1010(EX01)`, kernel `5.10.236` GKI
* **Identity:** device reports as `realme GT Neo 5 SE` (RMX3700)

> **Important Notices:**
> * **Experimental port:** ColorOS 17 was never released for this device. Expect rough edges.
> * **SELinux permissive** (patched `vendor_boot` cmdline). adb runs as root. Do not use for banking/sensitive data.
> * **Integrity:** AVB is off (`vbmeta` flashed with verity/verification disabled). Play Integrity fails, Google Pay and banking apps will not work.
> * **No GMS:** Chinese ColorOS product partition — no Google apps preinstalled. Gboard etc. can be sideloaded.
> * **Single-slot:** the ROM occupies slot A only; slot B is not a fallback.
> * **No OTA:** all updates are manual fastboot flashes.
> * **First boot takes 10–15 minutes** — do not touch the phone.

---

## Changelog — 2026-09-27

* First public ColorOS 17 port for this device.
* Boots, stable across reboots and cold boot.
* Fingerprint (UDFPS) at any brightness, 144 Hz, calls (VoLTE), mobile data.
* Wi-Fi hotspot fixed (netd patched for kernel 5.10 `close_range`/`CLOEXEC_DEFAULT` incompatibility).
* Camera hole artifact in the top-left corner fixed (realme display RRO overlay restored into `my_product`).
* Device renamed to realme GT Neo 5 SE (`my_manifest` patched; only market/display names changed, platform fingerprints untouched).

---

## Tested Base (Read Before Flashing)

Exactly one firmware base was tested: **Global RMX3701**:
```text
ro.build.display.id = RMX3701_16.0.5.1010(EX01)
ro.boot.prjname     = 22623
```

Check your parameters before flashing:
```bash
adb shell getprop ro.boot.prjname
```
> If `prjname` is not `22623`, do not flash.

---

## Hardware Status

### Working
* Boot and system stability
* Display at 144 Hz
* Touch
* Speaker, earpiece, voice calls (VoLTE)
* Mobile data, Wi-Fi, Bluetooth
* Wi-Fi hotspot
* In-display fingerprint
* Camera hardware (all lenses), video recording
* NFC, GPS, sensors, vibration

### Known Issues
* **No camera app** — `/product/app/OplusCamera` is an empty stub in the CN dump. Use any third-party camera app; the hardware works.
* **No GMS** — install Google apps manually at your own risk (A17 ColorOS framework compatibility is not guaranteed).
* Chinese ColorOS apps (KeKe Market, etc.) are present and can be uninstalled.
* A few harmless Oplus services log errors (tc/BPF limit-speed controller) — cosmetic only.

---

## Installation Guide

### Prerequisites
* Unlocked bootloader.
* [platform-tools](https://developer.android.com/studio/releases/platform-tools) in PATH or in `C:\platform-tools`.
* **Full backup:** all data will be wiped.

### Option 1: flash.bat (recommended)

1. Extract the archive to a short path without non-ASCII characters (e.g. `C:\c17`)
2. Power off → **Volume Down + Power** (bootloader)
3. Connect cable, run `flash.bat`
4. The script flashes the bootloader stage, reboots into fastbootd automatically, flashes all super partitions, wipes data and reboots
5. First boot: 10–15 minutes, do not interrupt

### Option 2: Manual commands

**Bootloader stage:**
```cmd
set PATH=C:\platform-tools;%PATH%
fastboot --disable-verity --disable-verification flash vbmeta BOOTLOADER\vbmeta.img
fastboot --disable-verity --disable-verification flash vbmeta_system BOOTLOADER\vbmeta_system.img
fastboot flash vbmeta_vendor BOOTLOADER\vbmeta_vendor.img
fastboot flash boot_a BOOTLOADER\boot.img
fastboot flash dtbo_a BOOTLOADER\dtbo.img
fastboot flash vendor_boot_a BOOTLOADER\vendor_boot.img
fastboot flash recovery_a BOOTLOADER\recovery.img
fastboot set_active a
fastboot reboot fastboot
```

**fastbootd stage** (logical partitions must be recreated if they were deleted):
```cmd
fastboot flash system_a SYSTEM\system.img
fastboot flash system_ext_a SYSTEM\system_ext.img
fastboot flash product_a SYSTEM\product.img
fastboot flash my_product_a SYSTEM\my_product.img
fastboot flash my_manifest_a SYSTEM\my_manifest.img
fastboot flash my_preload_a SYSTEM\my_preload.img
fastboot flash vendor_a VENDOR_SIDE\vendor.img
fastboot flash vendor_dlkm_a VENDOR_SIDE\vendor_dlkm.img
fastboot flash odm_a VENDOR_SIDE\odm.img
fastboot flash odm_dlkm_a VENDOR_SIDE\odm_dlkm.img
fastboot flash my_stock_a VENDOR_SIDE\my_stock.img
fastboot flash my_company_a VENDOR_SIDE\my_company.img
fastboot flash my_carrier_a VENDOR_SIDE\my_carrier.img
fastboot flash my_bigball_a VENDOR_SIDE\my_bigball.img
fastboot flash my_engineering_a VENDOR_SIDE\my_engineering.img
fastboot flash my_heytap_a VENDOR_SIDE\my_heytap.img
fastboot flash my_region_a VENDOR_SIDE\my_region.img
fastboot erase userdata
fastboot erase metadata
fastboot reboot
```

Google Drive: [Download](https://drive.google.com/file/d/1Af1ypwC273jSFRnp4dozSQbNacqlKz6N/view)
