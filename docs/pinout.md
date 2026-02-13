# Pinout Documentation

## ESP32 Module Pinout

### Power Pins
- **VIN**: 7-12V DC input (connected to LM2596 input)
- **3.3V**: 3.3V output from ESP32 internal regulator
- **5V**: 5V output from LM2596
- **GND**: Ground

### GPIO Assignments

#### Digital I/O
- **GPIO2**: Status LED (active high)
- **GPIO4**: Reserved for I2C SDA
- **GPIO5**: Reserved for I2C SCL
- **GPIO12**: MOSFET Gate 1 (Actuator Control)
- **GPIO13**: MOSFET Gate 2 (Actuator Control)

#### Analog Inputs
- **GPIO34**: Current Sensor Input (ACS712 output)
- **GPIO35**: Voltage Monitor
- **GPIO36**: Temperature Sensor (optional)

#### Communication
- **RX (GPIO3)**: UART Receive
- **TX (GPIO1)**: UART Transmit

### Sensor Connector (J2 - JST-XH 4P)
1. 5V
2. GND
3. SDA (GPIO4)
4. SCL (GPIO5)

## Power Input (J1 - Screw Terminal)
1. VIN (7-12V DC)
2. GND
