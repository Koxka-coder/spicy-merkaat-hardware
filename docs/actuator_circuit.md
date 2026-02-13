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
- **R_DS(on)**: 44mΩ @ V_GS = 10V, ~100-200mΩ @ V_GS = 3.3V (estimated)
- **V_GS(th)**: 2-4V
- **Gate Charge**: 71nC

**Note on 3.3V Operation**: When driven by 3.3V logic, the IRF540N will have higher on-resistance than the specified 44mΩ (which is at V_GS=10V). At 3.3V gate voltage, the on-resistance is typically 2-4x higher, estimated at 100-200mΩ (using 150mΩ as typical). This results in:
- Power dissipation at 10A: P = I²R = 100 × 0.15 = 15W (significant heat generation)
- For high-current applications (>10A), consider using logic-level MOSFETs (e.g., IRLZ44N) or adding a gate driver circuit

### Gate Drive Circuit
Each MOSFET gate is connected to ground through a 10kΩ pull-down resistor (R3-R4) to ensure the MOSFET remains off when the ESP32 GPIO is in high-impedance state (e.g., during boot or reset).

```
GPIO12/13 --------+------- Gate (Q1/Q2)
                  |
                [10kΩ]  R3/R4 (pull-down)
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
1. **IRF540N selection**: Provides generous headroom for current handling (33A rating). Note: At 3.3V gate drive, on-resistance is ~150mΩ (vs 44mΩ at 10V), so efficiency is reduced at high currents. For optimal efficiency at currents >5A, consider logic-level alternatives like IRLZ44N
2. **Low-side switching**: Simpler gate drive circuit, compatible with 3.3V logic from ESP32
3. **Direct GPIO drive**: V_GS(th) of IRF540N (2-4V) allows the MOSFET to turn on with 3.3V GPIO, though with higher on-resistance than at 10V
4. **10kΩ pull-downs (R3-R4)**: Ensure MOSFETs remain off during ESP32 boot/reset when GPIO pins are in high-impedance state, preventing unintended actuator activation during system startup

## Performance Considerations
- **Recommended load range**: 0-5A per channel for acceptable efficiency with 3.3V drive
- **For >5A loads**: Consider upgrading to logic-level MOSFETs (IRLZ44N, IRLB8721) or adding gate driver
- **Thermal management**: Add heatsink for continuous operation above 3A per channel

## Future Improvements
- Add flyback diodes (e.g., 1N4007) across inductive loads
- Upgrade to logic-level MOSFETs (IRLZ44N, IRLB8721) for better efficiency at high currents
- Consider gate driver IC (e.g., TC4427) for faster switching if PWM control is needed
- Add current limiting or foldback protection circuit
- Add heatsinks or improve thermal management for continuous high-current operation
