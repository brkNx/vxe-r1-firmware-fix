# VXE Dragonfly R1 - Dead Optical Sensor Fix & Official Firmware Recovery

[![VXE Dragonfly R1](https://img.shields.io/badge/Device-VXE%20Dragonfly%20R1-blue.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![Sensor: PAW3395](https://img.shields.io/badge/Sensor-PixArt%20PAW3395-brightgreen.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![MCU: CompX CX52850P](https://img.shields.io/badge/MCU-CompX%20CX52850P-orange.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![Firmware: Official v3.13](https://img.shields.io/badge/Firmware-Official%20v3.13-purple.svg)](https://github.com/brkNx/vxe-r1-firmware-fix/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-lightgrey.svg)](LICENSE)

Official, verified firmware (`VXE_R1_V3.13.bin`) and unbrick recovery guide for the **VXE Dragonfly R1** wireless gaming mouse suffering from a dead optical tracking sensor after accidental cross-flashing with R1 SE / SE+ firmware.

---

## 📸 Verified Working Status (Post-Recovery)

Below is the verified working status in ATK HUB after flashing `VXE_R1_V3.13.bin`. The mouse correctly identifies as **VXE R1** (restoring authentic PixArt PAW3395 optical tracking and USB product descriptor), with active battery reporting and wireless functionality:

<p align="center">
  <img src="assets/vxe_r1_atk_hub_success.jpg" alt="VXE Dragonfly R1 successfully recovered and recognized in ATK HUB" width="850">
</p>

---

## 🚨 The Issue: "Buttons and clicks work, but cursor won't move"

If you own a standard **VXE Dragonfly R1** and the cursor suddenly stops tracking while left/right clicks, wheel scrolls, USB enumeration, and LEDs still function normally, your mouse is **not physically broken**.

### Technical Root Cause
- **Hardware Architecture:** The standard **VXE Dragonfly R1** is powered by a **CompX CX52850P** MCU (Beken BK3633 core) paired with a high-end **PixArt PAW3395** optical sensor.
- **Accidental Cross-Flashing:** The budget **VXE R1 SE** and **R1 SE+** variants share the exact same USB Vendor ID (`0x3554`), Product ID (`0xF58F`), and MCU family, but utilize the cheaper **PixArt PAW3395SE** sensor.
- **What Went Wrong:** When users connect to ATK Hub or click "One-Click Recovery", ATK Hub often defaults to `VXE R1 SE` and flashes `VXER1SE_V3.14.bin`. Because the SE binary contains machine code tailored to the SE sensor's register addresses and pinouts, the MCU boots up (handling USB HID buttons), but **SPI communication with the physical PAW3395 sensor fails completely**, leaving the optical engine uninitialized and dead.
- **Why Web Flashing Fails:** Using web drivers like `hub.atk.pro` in Chromium browsers (Brave, Chrome) causes WebHID permission blocks on the bootloader (`VID_3554&PID_F406`), resulting in the dreaded `"Upgrade device not found"` loop. Furthermore, older binaries floating around (`R1-V1.12.bin`) originated from the F1 series and fail endpoint validation.

---

## 📥 Downloads

| File | Description | Download Link |
| :--- | :--- | :--- |
| **`VXE_R1_V3.13.bin`** | Official authentic CompX CX52850P firmware for VXE Dragonfly R1 (PAW3395) | [**Download v3.13**](https://github.com/brkNx/vxe-r1-firmware-fix/releases/download/v3.13/VXE_R1_V3.13.bin) |
| **Repository Clone** | Full source, docs, and direct binaries | `git clone https://github.com/brkNx/vxe-r1-firmware-fix.git` |

*Official ATK Cloud API Source:* `https://api-hub-cloud.atk.pro/firmware/13652/62863/r1` (`64ff9cd523604194782b5756b0f6ea89.bin`)

---

## 🛠️ Step-by-Step Unbrick & Recovery Guide

### Step 1: Prepare Your Environment
1. Close all browser tabs running `hub.atk.pro` to release exclusive WebHID handles.
2. Download and launch the official **ATK HUB desktop client** (v3.2.x or newer).
3. Connect the mouse directly to your PC via a USB-C data cable (do not use USB hubs). Unplug the 2.4G wireless dongle for now.

### Step 2: Flash via ATK HUB Desktop
1. In the ATK HUB desktop application, navigate to **Settings** (Gear icon ⚙️ on the bottom-left sidebar).
2. Under the settings menu, click on **Firmware Tools (固件工具)**.
3. Select **Mouse firmware tool**.
4. In the file picker, select **`VXE_R1_V3.13.bin`**.
5. Click **Upgrade**. The software will send the reset command, detect the bootloader, flash the 256KB image, and reboot the device.

---

### Step 3: Hardware Bootloader Mode (Emergency Fallback)
If ATK Hub shows *"Upgrade device not found"* or fails to switch the mouse automatically:

```
[Unplug Cable] ──> [Switch OFF] ──> [Hold LMB + RMB] ──> [Insert Cable] ──> [Release after 3s]
```

1. **Unplug** the USB-C cable from the mouse.
2. Flip the power switch on the bottom of the mouse to **OFF** (middle position).
3. Press and hold **Left Mouse Button + Right Mouse Button** simultaneously.
4. While keeping both buttons pressed, plug the USB-C cable back in.
5. Keep holding for 3 to 5 seconds, then release.
6. Check Windows Device Manager: the mouse will now be enumerated as:
   ```
   USB\VID_3554&PID_F406 (CompX USB Bootloader)
   ```
7. Open ATK HUB desktop. It will automatically detect the abnormal bootloader device and prompt the recovery modal, or you can flash `VXE_R1_V3.13.bin` directly via the Mouse Firmware Tool.
8. Once flashing reaches 100%, the mouse reboots into runtime PID `0xF58F` and optical tracking is immediately restored!

---

## ❓ FAQ (Frequently Asked Questions)

### Q: Did my mouse sensor burn out or break physically?
**A:** No. The PixArt PAW3395 sensor is 100% fine. The issue is purely software-level: the MCU was executing firmware with the wrong SPI initialization sequence and pin configuration. Once the correct v3.13 firmware is flashed, tracking works perfectly.

### Q: Why does ATK Hub display my mouse as "VXE R1SE"?
**A:** ATK Hub identifies mice by reading the USB Product Name descriptor programmed into the active firmware. When the SE firmware was flashed, the mouse began reporting its name as "VXE R1SE". Flashing `VXE_R1_V3.13.bin` restores the authentic "VXE R1" product identity.

### Q: Why does the web version (`hub.atk.pro`) fail with "No compatible devices found"?
**A:** Chromium browsers restrict certain WebHID top-level collections for security reasons. When the mouse reboots into bootloader mode (`PID_F406`), the browser does not have pre-granted permissions and fails to discover the endpoint. The desktop ATK HUB client uses native system HID access (`node-hid`) and handles this automatically.

### Q: Will this break my 2.4G wireless dongle pairing?
**A:** In most cases, existing 2.4G pairing information is preserved in NVRAM. If your mouse does not reconnect wirelessly after flashing:
1. Switch the mouse bottom switch to 2.4G wireless mode.
2. Plug the 2.4G receiver into the PC.
3. Press and hold **Left Click + Middle Wheel Click + Right Click** simultaneously for 3–5 seconds until the LED blinks rapidly to enter pairing mode.
4. Place the mouse close to the dongle; pairing will complete automatically.

---

## 🔬 Hardware & Endpoint Technical Reference

| Parameter | Application Runtime Mode | Bootloader / Recovery Mode |
| :--- | :--- | :--- |
| **Vendor ID (VID)** | `0x3554` (13652) | `0x3554` (13652) |
| **Product ID (PID)** | `0xF58F` (62863) | `0xF406` (62470) |
| **Primary HID Endpoint** | `MI_01&COL05` (UsagePage `0xFF04`, Usage `2`) | `MI_01&COL02` (UsagePage `0xFF02`, Usage `2`) |
| **Status Reporting** | Standard mouse reports + Vendor packets | `MI_01&COL01` (`0xB0` prepare, `0xB1` data) |
| **Optical Sensor** | PixArt PAW3395 (26,000 DPI, 650 IPS) | N/A (Bootloader execution) |
| **MCU Family** | CompX CX52850P (Beken BK3633 architecture) | Hardware ROM Bootloader |

---

## 📜 License & Disclaimer
- The firmware binary provided (`VXE_R1_V3.13.bin`) is an official, unmodified binary sourced directly from ATK Cloud CDN (`https://bpcdn.atkgear.com/hub-cloud/file/64ff9cd523604194782b5756b0f6ea89.bin`).
- All product names, logos, and brands are property of their respective owners (VXE / VGN / ATK Gear).
- This project is provided under the [MIT License](LICENSE) to assist community members in recovering bricked hardware.
