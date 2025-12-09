# 📝 Changelog

*🇩🇪 Deutsche Version: [CHANGELOG.de.md](CHANGELOG.de.md)*

All notable changes to the Laser CANbus Toolhead PCB project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html) for hardware revisions.

## [Unreleased]

## [Rev. 0.1] - 2025-11-26

### 🔧 Hardware Changes
- **Transistor Upgrade:** Replaced MOSFET with CJAC70P06 (60V voltage rating, improved safety margin)
- **Sensor Upgrade:** Changed from ADXL345 to ICM-20602 (better availability, modern 6-axis IMU with gyroscope)
- **Connector Change:** Micro Fit 3.0 connectors changed from SMD to THT mounting for better mechanical stability

### 🎨 Design Improvements
- **Silkscreen Enhancement:** Added connector pin assignments to silkscreen for easier assembly and debugging
- **Layout Optimization:** Cleaned up component placement and optimized routing for better signal integrity
- **Documentation:** Updated overview image to reflect current design

### 📋 Documentation & Production
- **BOM Update:** Added component order numbers for easier procurement
- **Production Files:** Updated assembly and production files with current components
- **Documentation:** Added comprehensive changelog and table of contents to README files
- **Language Support:** Improved documentation structure with proper language links

### 🔌 Electrical Specifications
- **Voltage Rating:** Improved to 60V (CJAC70P06 vs. previous AO4407A)
- **Current Handling:** Maintained 6A continuous, 8A peak capability
- **Sensor Capability:** Upgraded to 6-axis motion sensing (accelerometer + gyroscope)

## [Rev. 0.0] - 2025-11-25

### 🚀 Initial Release
- **Microcontroller:** STM32F072CBU6 (Cortex-M0, 48MHz, CAN-capable)
- **CAN-Bus Communication:** SN65HVD230 transceiver with ESD protection
- **Laser Power Control:** 24V/4A control with AO4407A MOSFET
- **Input Shaping:** ADXL345 accelerometer for Klipper resonance measurement
- **Power Supply:** MP2459 buck converter (24V to 5V) and XC6206 LDO (5V to 3.3V)
- **Safety Features:** Hardware pull-down, TVS protection, PTC fuse
- **Diagnostics:** Multiple status LEDs for system monitoring

### 🔧 Features
- Klipper-compatible firmware support
- CAN-Bus communication at 1 Mbit/s
- Hardware PWM for laser control
- Split termination via solder jumper
- Soft-start circuit for laser power
- Debug/programming header for development

---

## 📊 Version Comparison

| Feature | Rev. 0.0 | Rev. 0.1 |
|---------|----------|----------|
| **MOSFET** | AO4407A | CJAC70P06 (60V) |
| **Sensor** | ADXL345 (3-axis) | ICM-20602 (6-axis) |
| **Connectors** | SMD | THT |
| **Voltage Rating** | Standard | 60V Enhanced |
| **Silkscreen** | Basic | Enhanced with pinouts |

---

*For technical documentation, see [README.md](README.md)*