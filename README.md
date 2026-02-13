# Spicy Merkaat Hardware

ESP32-based field node controller for industrial automation and IoT applications with WiFi connectivity, current sensing, and dual actuator control.

## Project Description

This hardware project implements a versatile field controller node designed for industrial monitoring and control applications. The system is built around the ESP32-WROOM-32 module, providing WiFi and Bluetooth connectivity for remote monitoring and control. The design includes integrated power management, current sensing capabilities, and high-current actuator control channels suitable for driving solenoid valves, motors, or relays.

### Key Features
- **ESP32 WiFi/BT Module**: Wireless connectivity and processing
- **Dual Voltage Rails**: 5V (3A) and 3.3V for peripherals and controller
- **Current Sensing**: Up to 20A monitoring via ACS712 Hall effect sensor
- **Dual Actuator Channels**: High-current MOSFET drivers (up to 20A per channel)
- **I2C Expansion**: Dedicated connector for external sensors
- **Wide Input Range**: 7-12V DC input with protection

## Key Components

### Microcontroller & Communication
- **ESP32-WROOM-32**: Main controller with WiFi and Bluetooth connectivity
- **UART Interface**: For programming and debugging

### Power Management
- **LM2596S-5.0**: High-efficiency 5V/3A step-down regulator (~85% efficiency)
- **Input Protection**: Reverse polarity protection via 1N4007 diode
- **Filtering**: 100uF electrolytic + ceramic capacitors for stable power delivery

### Actuator Control
- **2x IRF540N MOSFETs**: N-channel transistors for high-current switching (100V/33A rated, ~150mΩ R_DS(on) at 3.3V gate drive)
- **Pull-down Resistors**: Gate protection and default-off state during boot/reset
- **Direct GPIO Drive**: Compatible with 3.3V logic levels

### Sensing & Monitoring
- **ACS712 Hall Effect Sensor**: Bidirectional current sensing (±20A range)
- **Voltage Monitoring**: ADC inputs for system diagnostics
- **Status LED**: Visual operation indicator

### Connectivity
- **Screw Terminal**: Robust power input connection
- **JST-XH Connector**: I2C sensor expansion (5V, GND, SDA, SCL)

## Design Decisions Summary

### Power Architecture
**Decision**: Use LM2596 buck converter for 5V rail, feeding ESP32's internal 3.3V regulator

**Rationale**:
- High efficiency (~85%) reduces heat dissipation
- 5V rail provides compatibility with common industrial sensors and modules
- ESP32 internal regulator ensures clean 3.3V for the microcontroller core
- Single-stage conversion from 7-12V to 5V minimizes component count

**Trade-offs**: Additional power conversion stage adds slight inefficiency but provides better noise isolation for the ESP32

### Actuator Control
**Decision**: Direct GPIO drive to IRF540N MOSFETs in low-side switching configuration

**Rationale**:
- IRF540N gate threshold (2-4V) allows reliable turn-on with 3.3V GPIO
- At 3.3V gate voltage, R_DS(on) is ~150mΩ, which is acceptable for loads up to 5A
- Generous current rating (33A) provides safety margin for 20A loads
- Low-side switching simplifies gate drive circuit (no level shifting needed)
- 10kΩ pull-downs prevent floating gates during boot/reset, ensuring safe startup

**Trade-offs**: Low-side switching means load is not directly grounded, but suitable for most applications

### Current Sensing
**Decision**: Single ACS712 sensor for total load current monitoring

**Rationale**:
- Hall effect technology provides galvanic isolation
- ±20A range covers expected load spectrum
- Analog output directly compatible with ESP32 ADC
- Requires no additional power supply beyond 5V rail

**Trade-offs**: Single sensor monitors total current, not per-channel; adequate for fault detection and power monitoring

### Microcontroller Selection
**Decision**: ESP32-WROOM-32 module

**Rationale**:
- Integrated WiFi and Bluetooth for IoT connectivity
- Sufficient GPIO and ADC channels for all peripherals
- Built-in programming interface
- Low power modes for battery operation (if needed)
- Large community support and mature ecosystem

**Trade-offs**: Higher power consumption than simpler MCUs, but connectivity features justify the cost

### Input Protection
**Decision**: Series diode (1N4007) for reverse polarity protection

**Rationale**:
- Simple, low-cost solution
- 1A rating adequate for system current draw
- Negligible voltage drop (~0.7V) at operating currents

**Trade-offs**: Small power loss across diode; alternative would be P-channel MOSFET (more complex, higher cost)

## Repository Structure

```
spicy-merkaat-hardware/
├── README.md              # This file
├── kicad/                 # KiCad schematic and PCB design files
├── bom/
│   └── bom.csv           # Bill of Materials with part numbers
├── datasheets/           # Component datasheets (PDF)
└── docs/
    ├── pinout.md         # ESP32 and connector pinout reference
    ├── power_design.md   # Power supply design documentation
    └── actuator_circuit.md # Actuator control circuit details
```

## Getting Started

### Hardware Requirements
- 7-12V DC power supply (minimum 1A recommended)
- USB-to-UART adapter for ESP32 programming
- Actuator loads (solenoid valves, motors, relays - max 20A combined)

### Documentation
Detailed documentation is available in the `docs/` directory:
- **[pinout.md](docs/pinout.md)**: Complete GPIO assignments and connector pinouts
- **[power_design.md](docs/power_design.md)**: Power supply design, current budget, and calculations
- **[actuator_circuit.md](docs/actuator_circuit.md)**: Actuator driver design and specifications

### Bill of Materials
See [bom/bom.csv](bom/bom.csv) for the complete parts list with manufacturer part numbers.

## Future Enhancements
- Add flyback diodes for inductive load protection
- Upgrade to logic-level MOSFETs for better efficiency at high currents
- Add voltage monitoring protection (clamping diode for ADC input)
- Implement current limiting/foldback protection
- Add temperature sensor for thermal monitoring
- PCB layout optimization for EMI reduction
- Enclosure design for industrial environments

## License
[Specify your license here]

## Contributing
[Add contribution guidelines if applicable]

## Contact
[Add contact information or links]
