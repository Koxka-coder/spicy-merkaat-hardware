# Power Supply Design

## Overview
The power system is designed to provide stable voltage rails for the ESP32 module and peripheral sensors/actuators from a 7-12V DC input supply.

## Power Architecture

### Input Protection
- Input voltage range: 7-12V DC
- Reverse polarity protection via diode D1 (1N4007)
- Input filtering with electrolytic capacitor C1 (100uF)

### Voltage Regulation

#### 5V Rail (LM2596 Buck Converter)
- **Regulator**: LM2596S-5.0
- **Input**: 7-12V DC
- **Output**: 5V @ 3A max
- **Efficiency**: ~85% typical
- **Application**: Powers sensors and actuator drivers

#### 3.3V Rail (ESP32 Internal)
- **Source**: ESP32 module internal LDO
- **Input**: 5V from LM2596
- **Output**: 3.3V @ 500mA
- **Application**: ESP32 core, WiFi/BT, and 3.3V peripherals

## Current Budget

| Component | Voltage | Current (Typical) | Current (Max) |
|-----------|---------|-------------------|---------------|
| ESP32-WROOM-32 | 3.3V | 80mA | 240mA (WiFi TX) |
| ACS712 Current Sensor | 5V | 10mA | 15mA |
| LED Indicator | 5V | 10mA | 20mA |
| I2C Sensors (est.) | 5V | 20mA | 50mA |
| Actuator MOSFETs (logic) | 5V | 5mA | 10mA |
| **Total** | - | ~125mA | ~335mA |

## Power Consumption Estimates
- Idle (WiFi connected): ~100mA @ 5V = 0.5W
- Active (WiFi + sensors): ~150mA @ 5V = 0.75W
- Peak (WiFi TX + actuators): ~300mA @ 5V = 1.5W

## Design Decisions
1. **LM2596 Selection**: Chosen for high efficiency and integrated design, reducing external component count
2. **5V as main rail**: Provides compatibility with common 5V sensors while allowing ESP32 to use its internal regulator
3. **Separate filtering**: Dedicated decoupling capacitors (C2-C4) near each IC to ensure clean power delivery
