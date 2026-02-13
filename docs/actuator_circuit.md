# Actuator Control Circuit

## Overview
The actuator control circuit uses N-channel MOSFETs to provide high-current switching capability for controlling external loads such as valves, motors, or relays.

## Circuit Design

### MOSFET Driver Configuration
- **Transistors**: 2x IRF540N N-channel MOSFETs (Q1, Q2)
- **Gate Control**: Direct drive from ESP32 GPIO pins
- **Configuration**: Low-side switching

### Component Details

#### IRF540N Specifications
- **V_DS**: 100V
- **I_D**: 33A continuous
- **R_DS(on)**: 44mΩ @ V_GS = 10V
- **V_GS(th)**: 2-4V
- **Gate Charge**: 71nC

### Gate Drive Circuit
Each MOSFET gate is driven through a 10kΩ pull-down resistor (R1-R2) to ensure the MOSFET remains off when the ESP32 GPIO is in high-impedance state (e.g., during boot).

```
GPIO12/13 ----[10kΩ]---- Gate
                |
               GND
```

### Protection Features
1. **Pull-down resistors**: Prevent floating gates during ESP32 reset/boot
2. **Flyback diodes**: (To be added) for inductive load protection
3. **Current sensing**: ACS712 monitors total load current

## Load Specifications
- **Maximum continuous current**: 20A per channel (limited by ACS712 range)
- **Maximum voltage**: 12V DC (system voltage)
- **Typical loads**: 
  - Solenoid valves: 0.5-2A
  - DC motors: 1-5A
  - Relay coils: 50-200mA

## Control Interface
- **GPIO12**: Actuator Channel 1
- **GPIO13**: Actuator Channel 2
- **Logic High (3.3V)**: MOSFET ON, load active
- **Logic Low (0V)**: MOSFET OFF, load inactive

## Design Decisions
1. **IRF540N selection**: Provides generous headroom for current handling and low on-resistance for efficiency
2. **Low-side switching**: Simpler gate drive circuit, compatible with 3.3V logic from ESP32
3. **Direct GPIO drive**: V_GS(th) of IRF540N is low enough for 3.3V GPIO to reliably turn on the MOSFET
4. **10kΩ pull-downs**: Prevent accidental activation while keeping minimal load on GPIO pins

## Future Improvements
- Add flyback diodes (e.g., 1N4007) across inductive loads
- Consider gate driver IC for faster switching if PWM control is needed
- Add current limiting or foldback protection circuit
