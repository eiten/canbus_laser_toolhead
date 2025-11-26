# 🔥 Laser CANbus Toolhead PCB

*🇬🇧 English version: [README.md](README.md)*

Ein kompaktes, robustes Toolhead-Board für Klipper-basierte Lasergravierer und -schneider (CoreXY). Dieses Board integriert Stromversorgung, Lasertreiber-Logik, CAN-Bus Kommunikation und Input Shaping (ICM-20602) auf kleinstem Raum.

![Laser CANbus Toolhead PCB](img/Laser%20CANbus%20Toolhead.png)

## 📚 Inhalt

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [🔥 Laser CANbus Toolhead PCB](#-laser-canbus-toolhead-pcb)
  - [📚 Inhalt](#-inhalt)
  - [✨ Features](#-features)
    - [🧠 Mikrocontroller](#-mikrocontroller)
    - [🔗 CAN-Bus Kommunikation](#-can-bus-kommunikation)
    - [📊 Input Shaping](#-input-shaping)
    - [⚡ Laser-Leistungssteuerung](#-laser-leistungssteuerung)
    - [🎛️ Laser-Signalsteuerung](#️-laser-signalsteuerung)
    - [🔌 Stromversorgung](#-stromversorgung)
    - [🚨 Diagnose & Monitoring](#-diagnose--monitoring)
  - [📋 Klipper Konfiguration](#-klipper-konfiguration)
    - [Basis MCU Setup](#basis-mcu-setup)
    - [Input Shaping (ICM-20602)](#input-shaping-icm-20602)
    - [Laser Steuerung](#laser-steuerung)
  - [🔌 Pinout & Steckerbelegung](#-pinout--steckerbelegung)
    - [J101 - Power & CAN Input (Micro-Fit 3.0, 2x2)](#j101---power--can-input-micro-fit-30-2x2)
    - [J101 - Strom & CAN Eingang (Micro-Fit 3.0, 2x2, THT)](#j101---strom--can-eingang-micro-fit-30-2x2-tht)
    - [J102 - Laser Output (Micro-Fit 3.0, 1x3, THT)](#j102---laser-output-micro-fit-30-1x3-tht)
    - [Debug/Programming Header (Rückseite)](#debugprogramming-header-rückseite)
  - [🔧 Installation & Setup](#-installation--setup)
    - [1. CAN-Bus Konfiguration](#1-can-bus-konfiguration)
    - [2. Firmware Flash](#2-firmware-flash)
    - [3. UUID ermitteln](#3-uuid-ermitteln)
  - [⚠️ Sicherheitshinweise](#️-sicherheitshinweise)
  - [🛠️ BOM (Bill of Materials) - Highlights](#️-bom-bill-of-materials---highlights)
  - [🛠️ Technische Spezifikationen](#️-technische-spezifikationen)
  - [📝 Changelog](#-changelog)
    - [Rev. 1](#rev-1)
    - [Rev. 0 (Erste Veröffentlichung)](#rev-0-erste-veröffentlichung)
  - [📚 Weitere Ressourcen](#-weitere-ressourcen)

<!-- /code_chunk_output -->



## ✨ Features

### 🧠 Mikrocontroller
- **MCU:** STM32F072CBU6 (Cortex-M0, 48MHz, CAN-fähig)
- **Stabilität:** 12MHz Quarz für maximale CAN-Bus Stabilität
- **Firmware:** Klipper-kompatibel

### 🔗 CAN-Bus Kommunikation
- **Transceiver:** SN65HVD230 mit ESD-Schutz
- **Terminierung:** Split-Terminierung via Solder-Jumper
- **Slope-Control:** Schaltbar für EMI-Optimierung

### 📊 Input Shaping
- **Sensor:** On-board ICM-20602 Beschleunigungs-/Gyroskopsensor (SPI)
- **Zweck:** Klipper Resonanzmessung für perfekte Druckqualität
- **Vorteil:** Bessere Verfügbarkeit und moderne Sensortechnologie

### ⚡ Laser-Leistungssteuerung
- **Schaltung:** 24V / 6A High-Side Switch (CJAC70P06 P-MOSFET, 60V Spannungsfestigkeit)
- **Spitzenstrom:** 8A Fähigkeit
- **Soft-Start:** Begrenzt Einschaltstrom (Rise-Time ca. 1.2ms)
- **Sicherheit:** Hardware-Pull-Down verhindert ungewollte Aktivierung
- **Begrenzung:** Max 6A begrenzt durch Micro Fit 3.0 Steckverbinder mit 0.75mm² Draht

### 🎛️ Laser-Signalsteuerung
- **PWM:** 5V Level-Shifted via 74AHCT1G125 Buffer
- **Qualität:** Saubere Flanken, echtes Hardware-PWM via STM32 Timer
- **Kompatibilität:** Gängige Diodenlaser

### 🔌 Stromversorgung
- **Eingang:** 24V mit 250mA PTC-Sicherung und SMF24A TVS-Diode
- **5V Rail:** MP2459 Buck Converter (bis 60V Input tolerant)
- **3.3V Rail:** XC6206 LDO für MCU und Peripherie

### 🚨 Diagnose & Monitoring
- **Power-LEDs:** 24V In, 24V Sys, 5V, 3.3V
- **Status-LEDs:** Laser-Enable, Laser-PWM, Heartbeat

## 📋 Klipper Konfiguration

### Basis MCU Setup
```ini
[mcu toolhead]
canbus_uuid: <deine-uuid>  # Mit "ls /dev/serial/by-id/*" oder "~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0" ermitteln

[temperature_sensor toolhead_mcu]
sensor_type: temperature_mcu
sensor_mcu: toolhead
```

### Input Shaping (ICM-20602)
```ini
[mpu9250]
cs_pin: toolhead:PA4
spi_bus: spi1
#axes_map: x,y,z  # Je nach Drucker-Orientierung konfigurieren

[resonance_tester]
accel_chip: mpu9250
probe_points:
    150, 150, 20  # An deine Druckbettgröße anpassen
```

### Laser Steuerung
```ini
# Laser PWM Signal
[output_pin laser_pwm]
pin: toolhead:PB14
pwm: True
cycle_time: 0.001          # 1kHz PWM Frequenz
shutdown_value: 0          # Sicherheit: Laser aus bei Notfall

# Laser Enable (optional)
[output_pin laser_enable]
pin: toolhead:PB15
value: 0
shutdown_value: 0

# Heartbeat LED (optional)
[output_pin heartbeat]
pin: toolhead:PA9
pwm: True
cycle_time: 1.0
```

## 🔌 Pinout & Steckerbelegung

### J101 - Power & CAN Input (Micro-Fit 3.0, 2x2)
| Pin | Signal | Beschreibung |
|-----|---------|--------------|
| 1 | **+24V** | Hauptstromversorgung (High Current) |
| 2 | **GND** | Masse |
| 3 | **CAN_H** | CAN-Bus High Signal |
### J101 - Strom & CAN Eingang (Micro-Fit 3.0, 2x2, THT)
| Pin | Signal | Beschreibung |
|-----|---------|--------------|
| 1 | **+24V** | Hauptstromversorgung (High Current) |
| 2 | **GND** | Masse |
| 3 | **CAN_H** | CAN-Bus High Signal |
| 4 | **CAN_L** | CAN-Bus Low Signal |

### J102 - Laser Output (Micro-Fit 3.0, 1x3, THT)
| Pin | Signal | Beschreibung |
|-----|---------|--------------|
| 1 | **GND** | Laser-Masse |
| 2 | **PWM** | 5V PWM-Signal (Level-Shifted) |
| 3 | **+24V** | Geschaltete Laser-Stromversorgung (Soft-Start) |

### Debug/Programming Header (Rückseite)
| Pad | Pin | Signal | Funktion |
|-----|-----|---------|----------|
| 1 | - | **5V** | 5V Einspeisung (vom Programmer) |
| 2 | - | **3.3V** | VTref (Referenzspannung) |
| 3 | PA13 | **SWDIO** | Serial Wire Debug I/O |
| 4 | PA14 | **SWCLK** | Serial Wire Debug Clock |
| 5 | - | **NRST** | Reset-Signal |
| 6 | - | **GND** | Masse |

> **💡 Bootloader-Modus:** Um den STM32 in den DFU/Bootloader-Modus zu versetzen (z.B. für Erst-Flash mit Katapult), brücke das **BOOT0** Pad mit **3.3V** während des Einschaltens.

## 🔧 Installation & Setup

### 1. CAN-Bus Konfiguration
- CAN-Bus Terminierung je nach Position im Netzwerk setzen
- Baudrate: 1 Mbit/s (Standard für Klipper)

### 2. Firmware Flash
1. Board in DFU-Modus versetzen (BOOT0 brücken)
2. Klipper für STM32F072 mit CAN-Support kompilieren
3. Firmware flashen: `make flash FLASH_DEVICE=<dfu-device>`

### 3. UUID ermitteln
```bash
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```

## ⚠️ Sicherheitshinweise

- **Laser-Sicherheit:** Immer Schutzbrille tragen
- **Stromversorgung:** Nur mit 24V DC betreiben
- **Erste Inbetriebnahme:** Laser-Leistung langsam hochfahren
- **Notfall:** Hardware-Pull-Down sorgt für sicheren Zustand

## 🛠️ BOM (Bill of Materials) - Highlights

| Komponente | Wert/Typ | Funktion | Package | Bestellnummer |
|------------|----------|----------|---------|---------------|
| **U101** | STM32F072CBU6 | Hauptmikrocontroller | UFQFPN-48 | STM32F072CBU6 |
| **U102** | MP2459GJ-Z | Buck Converter 24V→5V | TSOT-23-8 | MP2459GJ-Z |
| **U105** | SN65HVD230DR | CAN-Bus Transceiver | SOIC-8 | SN65HVD230DR |
| **U106** | ICM-20602 | 6-Achsen IMU (Beschleunigungs-/Gyroskop) | LGA-16 | ICM-20602 |
| **Q101** | CJAC70P06 | P-MOSFET (Laser-Schalter, 60V) | SOIC-8 | CJAC70P06 |
| **D101** | SMF24A | TVS-Diode (Überspannungsschutz) | DO-214AC | SMF24A |
| **F101** | 1812L025 | PTC-Sicherung 250mA | 1812 | 1812L025 |
| **Y101** | 12MHz | Quarz für CAN-Stabilität | HC-49/S | 12MHz Quarz |
| **J101/J102** | Micro-Fit 3.0 | THT Steckverbinder | THT | Micro-Fit 3.0 THT |

## 🛠️ Technische Spezifikationen

| Parameter | Wert | Einheit |
|-----------|------|---------|
| **Eingangsspannung** | 24 ± 2 | V |
| **Laser-Strom (max)** | 4 | A |
| **CAN-Baudrate** | 1 | Mbit/s |
| **PWM-Frequenz** | 1 | kHz |
| **Soft-Start Zeit** | ~1.2 | ms |
| **Betriebstemperatur** | -10 bis +70 | °C |
| **Abmessungen** | TBD | mm |

## 📝 Changelog

### Rev. 1
- **Transistor Upgrade:** MOSFET durch CJAC70P06 ersetzt (60V Spannungsfestigkeit)
- **Sensor Upgrade:** Wechsel von ADXL345 zu ICM-20602 (bessere Verfügbarkeit, moderner 6-Achsen IMU)
- **Steckverbinder Änderung:** Micro Fit 3.0 Steckverbinder von SMD zu THT geändert
- **Bestückungsdruck Verbesserung:** Steckerbelegungen zum Bestückungsdruck hinzugefügt für einfachere Montage
- **Layout Verbesserungen:** Bestückungsplatz aufgeräumt und Routing optimiert
- **Dokumentation:** Übersichtsbild an aktuelles Design angepasst
- **BOM Update:** Bauteil-Bestellnummern hinzugefügt und Produktionsdaten aktualisiert
- **Produktionsdateien:** Montage- und Produktionsdateien mit aktuellen Bauteilen aktualisiert

### Rev. 0 (Erste Veröffentlichung)
- Erstes PCB-Design mit STM32F072CBU6 Mikrocontroller
- CAN-Bus Kommunikation mit SN65HVD230 Transceiver
- 24V/4A Laser-Leistungssteuerung mit AO4407A MOSFET
- ADXL345 Beschleunigungssensor für Input Shaping
- MP2459 Buck Converter für Stromversorgung

## 📚 Weitere Ressourcen

- [Klipper Dokumentation](https://www.klipper3d.org/Config_Reference.html)
- [CAN-Bus Setup Guide](https://www.klipper3d.org/CANBUS.html)
- [Input Shaping](https://www.klipper3d.org/Resonance_Compensation.html)
- [Katapult Firmware Flasher](https://github.com/Arksine/katapult)