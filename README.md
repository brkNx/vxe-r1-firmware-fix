# VXE Dragonfly R1 - Dead Sensor Fix & Official Firmware Recovery (PAW3395 / CX52850P)

[![VXE Dragonfly R1](https://img.shields.io/badge/Device-VXE%20Dragonfly%20R1-blue.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![Sensor: PAW3395](https://img.shields.io/badge/Sensor-PixArt%20PAW3395-brightgreen.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![MCU: CompX CX52850P](https://img.shields.io/badge/MCU-CompX%20CX52850P-orange.svg)](https://github.com/brkNx/vxe-r1-firmware-fix)
[![Firmware: Official v3.13](https://img.shields.io/badge/Firmware-Official%20v3.13-purple.svg)](https://github.com/brkNx/vxe-r1-firmware-fix/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-lightgrey.svg)](LICENSE)

English | [Türkçe](#türkçe-kılavuz)

---

## English Guide

### 🚨 The Problem: "Mouse clicks work, but cursor does not move"
If you own a standard **VXE Dragonfly R1** (PixArt PAW3395 sensor) and your mouse cursor suddenly stopped moving while left/right clicks, scroll wheel, and RGB still work perfectly:
- **You accidentally flashed `VXER1SE_V3.14.bin` or used "One-Click Recovery" selecting `VXE R1 SE` in ATK Hub.**
- **Why it happens:** The VXE Dragonfly R1 uses a **PixArt PAW3395** sensor, whereas the R1 SE / R1 SE+ uses a **PAW3395SE** sensor. Both mice share the same **CompX CX52850P** MCU and USB Product ID (`0xF58F`). When R1 SE firmware is written to a standard R1, the MCU boots up normally (so USB and button clicks work), but the sensor driver fails SPI initialization. As a result, the optical sensor remains unpowered and dead.
- **Why ATK Hub gets stuck at "Upgrade device not found":** WebHID in browsers (like Brave or Chrome at `hub.atk.pro`) cannot access the bootloader (`VID_3554&PID_F406`) automatically, and older/corrupted binaries (`R1-V1.12.bin` from F1 series) fail endpoint verification.

---

### 🛠️ Solution & Recovery Steps

#### Step 1: Download the Authentic Firmware
Download **`VXE_R1_V3.13.bin`** directly from this repository:
- **[Download VXE_R1_V3.13.bin (Official Release)](https://github.com/brkNx/vxe-r1-firmware-fix/releases/latest)**
- *Checksum & Metadata:*
  - Chipset: CompX CX52850P
  - Sensor: PixArt PAW3395
  - Target Version: `3.13`
  - Normal Endpoint: `VID_3554&PID_F58F&MI_01&COL05`
  - Bootloader Endpoint: `VID_3554&PID_F406&MI_01&COL02`
  - Official ATK Cloud Source: `https://api-hub-cloud.atk.pro/firmware/13652/62863/r1`

#### Step 2: Use ATK HUB Desktop (Not Web Browser)
1. Close any `hub.atk.pro` browser tabs to prevent WebHID port conflicts.
2. Open the official desktop software: **ATK HUB** (version 3.2.x or newer).
3. If the mouse is already connected via USB-C cable and shows up:
   - Go to **Settings (Gear icon ⚙️)** on the left sidebar.
   - Click **Firmware Tools (固件工具)**.
   - Click **Mouse firmware tool**.
   - Browse and select `VXE_R1_V3.13.bin`.
   - Start the update.

#### Step 3: Hardware Bootloader Mode (If mouse is not recognized)
If ATK Hub shows "Device not found" or "Upgrade device not found":
1. Unplug the USB-C cable from the mouse.
2. Flip the mouse bottom power switch to **OFF** (middle position).
3. Press and hold **Left Click + Right Click** simultaneously.
4. While holding both buttons, plug the USB-C cable back into your PC.
5. Keep holding for 3–5 seconds, then release.
6. The mouse will enumerate as **`USB\VID_3554&PID_F406`** (CompX Bootloader).
7. ATK HUB desktop will automatically prompt "Abnormal Device Detected" / "One-Click Recovery", or you can load `VXE_R1_V3.13.bin` in the Mouse Firmware Tool.
8. Once the flash completes 100%, the mouse reboots and optical tracking is immediately restored!

---

### ❓ FAQ (Frequently Asked Questions)

**Q: Did my optical sensor physically burn out?**  
A: No! The PAW3395 hardware is intact. It only failed to communicate over SPI bus because the MCU was executing firmware instructions compiled for the SE sensor pinout.

**Q: Why does ATK Hub identify my mouse as "VXE R1SE"?**  
A: ATK Hub reads the USB Product Name string from the active firmware. When the SE firmware was flashed, the mouse announced its USB descriptor string as "VXE R1SE". Flashing `VXE_R1_V3.13.bin` restores the authentic "VXE R1" descriptor.

**Q: Will this break my 2.4G wireless receiver pairing?**  
A: No. If the mouse does not automatically connect to the 2.4G dongle after flashing, press and hold Left + Middle + Right buttons for 3 seconds while in wireless mode to initiate 2.4G re-pairing.

---

<br>

## Türkçe Kılavuz

### 🚨 Sorunun Nedeni: "Mouse tıklıyor ama imleç hareket etmiyor"
Standart bir **VXE Dragonfly R1** (PixArt PAW3395 sensörlü) fareniz varsa ve sol/sağ tıklar çalışmasına rağmen imleç ekranda hiç oynamıyorsa:
- **ATK Hub'da yanlışlıkla `VXER1SE_V3.14.bin` dosyası yüklendi veya "Tek Tıkla Kurtarma" (One-Click Recovery) ekranında `VXE R1 SE` modeli seçildi.**
- **Neden Olur?** Standart VXE Dragonfly R1, **PixArt PAW3395** sensörüne sahiptir; SE modelleri ise **PAW3395SE** sensörü kullanır. Her iki model de aynı **CompX CX52850P** ana kontrol çipini (MCU) ve USB Kimliğini (`0xF58F`) paylaşır. SE yazılımı standart R1'e yüklendiğinde MCU çalışır (tıklamalar, RGB ve USB algılama devam eder), ancak PAW3395 sensörünün SPI sürücüsü başlatılamaz. Sensör kapalı kaldığı için imleç hareket etmez.
- **Neden ATK Hub "Upgrade device not found" hatası veriyor?** Brave veya Chrome tarayıcısında WebHID (`hub.atk.pro`), Windows'taki `0xF406` bootloader arayüzünü otomatik yakalayamaz. Ayrıca internette dolaşan eski `R1-V1.12.bin` dosyaları F1 serisine aittir ve paket doğrulamasından geçemez.

---

### 🛠️ Çözüm ve Kurtarma Adımları

#### 1. Adım: Orijinal Firmware'i İndirin
Bu repodan resmi ve temiz firmware'i indirin:
- **[VXE_R1_V3.13.bin İndir (Resmi Sürüm)](https://github.com/brkNx/vxe-r1-firmware-fix/releases/latest)**
- *Teknik Detaylar:*
  - Donanım: CompX CX52850P MCU & PixArt PAW3395
  - Hedef Sürüm: `3.13`
  - Normal HID Endpoint: `VID_3554&PID_F58F&MI_01&COL05`
  - Bootloader Endpoint: `VID_3554&PID_F406&MI_01&COL02`
  - ATK Sunucu Kaynağı: `https://api-hub-cloud.atk.pro/firmware/13652/62863/r1`

#### 2. Adım: Masaüstü ATK HUB Programını Kullanın
1. Tarayıcıdaki `hub.atk.pro` sekmelerini kapatın (port çakışmasını engellemek için).
2. Bilgisayarınızda kurulu olan **ATK HUB** masaüstü uygulamasını açın.
3. Mouse kabloyla bağlıysa:
   - Sol menüdeki **Ayarlar (Çark ⚙️)** simgesine tıklayın.
   - **Firmware Tools (固件工具)** bölümüne gelin.
   - **Mouse firmware tool** seçeneğini açın.
   - İndirdiğiniz **`VXE_R1_V3.13.bin`** dosyasını seçip yüklemeyi başlatın.

#### 3. Adım: Donanımsal Bootloader Modu (Mouse Algılanmıyorsa)
Program "Upgrade device not found" diyorsa veya cihazı görmüyorsa:
1. Mouse'un Type-C kablosunu çıkarın.
2. Altındaki güç anahtarını **OFF** (orta konum) durumuna getirin.
3. **Sol Tık + Sağ Tık** butonlarına aynı anda basılı tutun.
4. Butonları bırakmadan Type-C kablosunu bilgisayara takın.
5. 3–5 saniye basılı tuttuktan sonra bırakın.
6. Mouse donanımsal olarak **`USB\VID_3554&PID_F406`** (CompX Bootloader) moduna geçer.
7. ATK HUB masaüstü programı otomatik olarak "Anormal Cihaz Algılandı" / "Tek Tıkla Kurtarma" penceresini açacaktır (veya Mouse Firmware Tool üzerinden dosyayı seçebilirsiniz).
8. Yükleme tamamlandığında fare yeniden başlar ve sensör anında çalışmaya başlar!

---

### ❓ Sıkça Sorulan Sorular (SSS)

**S: Faremin sensörü donanımsal olarak yandı mı / bozuldu mu?**  
C: Hayır! Sensör tamamen sağlamdır. Yalnızca çipteki yazılım SE sensörü için derlendiğinden PAW3395 ile SPI haberleşmesi kuramamıştır. Doğru yazılım yüklendiğinde ilk günkü haline döner.

**S: Program mouse'umu neden "VXE R1SE" olarak görüyor?**  
C: ATK Hub, cihaz adını yüklü firmware'in USB tanımlayıcısından okur. SE yazılımı yüklendiği için mouse kendini sisteme R1SE olarak tanıtmıştır. `VXE_R1_V3.13.bin` yüklendiğinde tekrar gerçek adı olan "VXE R1" olarak görünecektir.

**S: Kablosuz 2.4G dongle bağlantısı bozulur mu?**  
C: Genellikle eşleşme korunur. Eğer kablosuz modda bağlanmazsa, kablosuz moda alıp Sol + Orta (Tekerlek) + Sağ tık tuşlarına 3 saniye basılı tutarak dongle ile yeniden hızlıca eşleştirebilirsiniz.

---

### 📜 Lisans & Sorumluluk Reddi
- Firmware dosyası doğrudan ATK Cloud altyapısından temin edilmiş resmi binary'dir (`64ff9cd523604194782b5756b0f6ea89.bin`).
- Bu doküman, yanlış firmware yüklemesi sonucu sensör arızası yaşayan kullanıcıların cihazlarını kurtarması amacıyla hazırlanmıştır.
