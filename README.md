# 🔥 Laser CANbus Toolhead PCB

A compact, robust toolhead board for Klipper-based laser engravers and cutters (CoreXY). This board integrates power supply, laser driver logic, CAN-Bus communication, and input shaping (ADXL345) in the smallest possible space.

![Laser CANbus Toolhead PCB](img/Laser%20CANbus%20Toolhead.png)

## ✨ Features

### 🧠 Microcontroller
- **MCU:** STM32F072CBU6 (Cortex-M0, 48MHz, CAN-capable)
- **Stability:** 12MHz crystal for maximum CAN-Bus stability
- **Firmware:** Klipper-compatible

### 🔗 CAN-Bus Communication
- **Transceiver:** SN65HVD230 with ESD protection
- **Termination:** Split termination via solder jumper
- **Slope Control:** Switchable for EMI optimization

### 📊 Input Shaping
- **Sensor:** On-board ADXL345 accelerometer (SPI)
- **Purpose:** Klipper resonance measurement for perfect print quality

### ⚡ Laser Power Control
- **Circuit:** 24V / 4A High-Side Switch (AO4407A P-MOSFET)
- **Soft-Start:** Limits inrush current (Rise-time ~1.2ms)
- **Safety:** Hardware pull-down prevents unwanted activation

### 🎛️ Laser Signal Control
- **PWM:** 5V Level-shifted via 74AHCT1G125 buffer
- **Quality:** Clean edges, true hardware PWM via STM32 timer
- **Compatibility:** Common diode lasers

### 🔌 Power Supply
- **Input:** 24V with 250mA PTC fuse and SMF24A TVS diode
- **5V Rail:** MP2459 Buck converter (up to 60V input tolerant)
- **3.3V Rail:** XC6206 LDO for MCU and peripherals

### 🚨 Diagnostics & Monitoring
- **Power LEDs:** 24V In, 24V Sys, 5V, 3.3V
- **Status LEDs:** Laser Enable, Laser PWM, Heartbeat

## 📋 Klipper Configuration

### Basic MCU Setup
```ini
[mcu toolhead]
canbus_uuid: <your-uuid>  # Find with "ls /dev/serial/by-id/*" or "~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0"

[temperature_sensor toolhead_mcu]
sensor_type: temperature_mcu
sensor_mcu: toolhead
```

### Input Shaping (ADXL345)
```ini
[adxl345]
cs_pin: toolhead:PA4
spi_bus: spi1
axes_map: x,y,z

[resonance_tester]
accel_chip: adxl345
probe_points:
    150, 150, 20  # Adjust to your bed size
```

### Laser Control
```ini
# Laser PWM Signal
[output_pin laser_pwm]
pin: toolhead:PB14
pwm: True
cycle_time: 0.001          # 1kHz PWM frequency
shutdown_value: 0          # Safety: laser off during emergency

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

## 🔌 Pinout & Connector Assignment

### J101 - Power & CAN Input (Micro-Fit 3.0, 2x2)
| Pin | Signal | Description |
|-----|---------|-------------|
| 1 | **+24V** | Main power input (High Current) |
| 2 | **GND** | Ground |
| 3 | **CAN_H** | CAN-Bus High Signal |
| 4 | **CAN_L** | CAN-Bus Low Signal |

### J102 - Laser Output (Micro-Fit 3.0, 1x3)
| Pin | Signal | Description |
|-----|---------|-------------|
| 1 | **GND** | Laser Ground |
| 2 | **PWM** | 5V PWM Signal (Level-Shifted) |
| 3 | **+24V** | Switched Laser Power (Soft-Start) |

### Debug/Programming Header (Back Side)
| Pad | Pin | Signal | Function |
|-----|-----|---------|----------|
| 1 | - | **5V** | 5V Supply (from Programmer) |
| 2 | - | **3.3V** | VTref (Reference Voltage) |
| 3 | PA13 | **SWDIO** | Serial Wire Debug I/O |
| 4 | PA14 | **SWCLK** | Serial Wire Debug Clock |
| 5 | - | **NRST** | Reset Signal |
| 6 | - | **GND** | Ground |

> **💡 Bootloader Mode:** To force the STM32 into DFU/Bootloader mode (e.g., for initial flash with Katapult), bridge the **BOOT0** pad with **3.3V** during power-on.

## 🔧 Installation & Setup

### 1. CAN-Bus Configuration
- Set CAN-Bus termination according to position in network
- Baud rate: 1 Mbit/s (Klipper standard)

### 2. Firmware Flash
1. Put board into DFU mode (bridge BOOT0)
2. Compile Klipper for STM32F072 with CAN support
3. Flash firmware: `make flash FLASH_DEVICE=<dfu-device>`

### 3. Find UUID
```bash
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```

## ⚠️ Safety Notes

- **Laser Safety:** Always wear protective eyewear
- **Power Supply:** Only operate with 24V DC
- **Initial Setup:** Slowly ramp up laser power
- **Emergency:** Hardware pull-down ensures safe state

## 🛠️ BOM (Bill of Materials) - Highlights

| Component | Value/Type | Function | Package |
|-----------|------------|----------|---------|
| **U101** | STM32F072CBU6 | Main Microcontroller | UFQFPN-48 |
| **U102** | MP2459GJ-Z | Buck Converter 24V→5V | TSOT-23-8 |
| **U105** | SN65HVD230DR | CAN-Bus Transceiver | SOIC-8 |
| **U106** | ADXL343/ADXL345 | 3-Axis Accelerometer | LGA-14 |
| **Q101** | AO4407A | P-MOSFET (Laser Switch) | SOIC-8 |
| **D101** | SMF24A | TVS Diode (Overvoltage Protection) | DO-214AC |
| **F101** | 1812L025 | PTC Fuse 250mA | 1812 |
| **Y101** | 12MHz | Crystal for CAN Stability | HC-49/S |

## 🛠️ Technical Specifications

| Parameter | Value | Unit |
|-----------|-------|------|
| **Input Voltage** | 24 ± 2 | V |
| **Laser Current (max)** | 4 | A |
| **CAN Baud Rate** | 1 | Mbit/s |
| **PWM Frequency** | 1 | kHz |
| **Soft-Start Time** | ~1.2 | ms |
| **Operating Temperature** | -10 to +70 | °C |
| **Dimensions** | TBD | mm |

## 📚 Additional Resources

- [Klipper Documentation](https://www.klipper3d.org/Config_Reference.html)
- [CAN-Bus Setup Guide](https://www.klipper3d.org/CANBUS.html)
- [Input Shaping](https://www.klipper3d.org/Resonance_Compensation.html)
- [Katapult Firmware Flasher](https://github.com/Arksine/katapult)

---

*🇩🇪 Deutsche Version: [Readme.de.md](Readme.de.md)*