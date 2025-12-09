# 📝 Changelog

*🇬🇧 English version: [CHANGELOG.md](CHANGELOG.md)*

Alle wichtigen Änderungen am Laser CANbus Toolhead PCB Projekt werden in dieser Datei dokumentiert.

Das Format basiert auf [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
und dieses Projekt folgt [Semantic Versioning](https://semver.org/spec/v2.0.0.html) für Hardware-Revisionen.

## [Unveröffentlicht]

## [Rev. 0.1] - 2025-11-26

### 🔧 Hardware-Änderungen
- **Transistor Upgrade:** MOSFET durch CJAC70P06 ersetzt (60V Spannungsfestigkeit, verbesserte Sicherheitsmarge)
- **Sensor Upgrade:** Wechsel von ADXL345 zu ICM-20602 (bessere Verfügbarkeit, moderner 6-Achsen IMU mit Gyroskop)
- **Steckverbinder Änderung:** Micro Fit 3.0 Steckverbinder von SMD zu THT geändert für bessere mechanische Stabilität

### 🎨 Design-Verbesserungen
- **Bestückungsdruck Verbesserung:** Steckerbelegungen zum Bestückungsdruck hinzugefügt für einfachere Montage und Fehlersuche
- **Layout-Optimierung:** Bestückungsplatz aufgeräumt und Routing für bessere Signalintegrität optimiert
- **Dokumentation:** Übersichtsbild an aktuelles Design angepasst

### 📋 Dokumentation & Produktion
- **BOM Update:** Bauteil-Bestellnummern für einfachere Beschaffung hinzugefügt
- **Produktionsdateien:** Montage- und Produktionsdateien mit aktuellen Bauteilen aktualisiert
- **Dokumentation:** Umfassendes Changelog und Inhaltsverzeichnis zu README-Dateien hinzugefügt
- **Sprachunterstützung:** Verbesserte Dokumentationsstruktur mit ordentlichen Sprachverlinkungen

### 🔌 Elektrische Spezifikationen
- **Spannungsfestigkeit:** Verbessert auf 60V (CJAC70P06 vs. vorherigem AO4407A)
- **Strombelastbarkeit:** 6A kontinuierlich, 8A Spitzenstrom beibehalten
- **Sensor-Fähigkeiten:** Upgrade auf 6-Achsen Bewegungserkennung (Beschleunigungssensor + Gyroskop)

## [Rev. 0.0] - 2025-11-25

### 🚀 Erste Veröffentlichung
- **Mikrocontroller:** STM32F072CBU6 (Cortex-M0, 48MHz, CAN-fähig)
- **CAN-Bus Kommunikation:** SN65HVD230 Transceiver mit ESD-Schutz
- **Laser-Leistungssteuerung:** 24V/4A Steuerung mit AO4407A MOSFET
- **Input Shaping:** ADXL345 Beschleunigungssensor für Klipper Resonanzmessung
- **Stromversorgung:** MP2459 Buck Converter (24V zu 5V) und XC6206 LDO (5V zu 3.3V)
- **Sicherheitsfeatures:** Hardware-Pull-Down, TVS-Schutz, PTC-Sicherung
- **Diagnose:** Mehrere Status-LEDs für Systemüberwachung

### 🔧 Features
- Klipper-kompatible Firmware-Unterstützung
- CAN-Bus Kommunikation mit 1 Mbit/s
- Hardware-PWM für Laser-Steuerung
- Split-Terminierung via Solder-Jumper
- Soft-Start Schaltung für Laser-Stromversorgung
- Debug/Programming-Header für Entwicklung

---

## 📊 Versions-Vergleich

| Feature | Rev. 0.0 | Rev. 0.1 |
|---------|----------|----------|
| **MOSFET** | AO4407A | CJAC70P06 (60V) |
| **Sensor** | ADXL345 (3-Achsen) | ICM-20602 (6-Achsen) |
| **Steckverbinder** | SMD | THT |
| **Spannungsfestigkeit** | Standard | 60V Verbessert |
| **Bestückungsdruck** | Basis | Erweitert mit Pinbelegungen |

---

*Für technische Dokumentation siehe [README.de.md](README.de.md)*