# ADBK2 Keyboard Wiring Reference

## Hardware Overview
- **Microcontroller**: Seeeduino XIAO BLE (nRF52840)
- **Port Expanders**: 2x MCP23017 (I2C)
- **Matrix**: 16 rows × 8 columns (128 keys max)
- **Keyboard Type**: Membrane keyboard (no diodes)

---

## Component Connections

### Seeeduino XIAO BLE (U1)
| Pin | Function | Connection |
|-----|----------|------------|
| 3V3 | Power | VCC for both MCP23017s |
| GND | Ground | Common ground |
| D4 (SDA) | I2C Data | SDA on both MCP23017s |
| D5 (SCL) | I2C Clock | SCL on both MCP23017s |
| D0-D3, D6-D10 | GPIO | Available for rotary encoder |

---

### MCP23017 #1 - U2 (I2C Address: 0x20)
**Function**: 16 Keyboard Matrix Rows

| Pin | Function | Connection |
|-----|----------|------------|
| VDD | Power | 3V3 from XIAO |
| VSS | Ground | GND |
| SDA | I2C Data | Shared I2C bus |
| SCL | I2C Clock | Shared I2C bus |
| A0, A1, A2 | Address | All connected to GND (Address = 0x20) |
| RESET | Reset | Pull-up to VCC |
| GPA0 | GPIO | **ROW1** → Membrane row 1 |
| GPA1 | GPIO | **ROW2** → Membrane row 2 |
| GPA2 | GPIO | **ROW3** → Membrane row 3 |
| GPA3 | GPIO | **ROW4** → Membrane row 4 |
| GPA4 | GPIO | **ROW5** → Membrane row 5 |
| GPA5 | GPIO | **ROW6** → Membrane row 6 |
| GPA6 | GPIO | **ROW7** → Membrane row 7 |
| GPA7 | GPIO | **ROW8** → Membrane row 8 |
| GPB0 | GPIO | **ROW9** → Membrane row 9 |
| GPB1 | GPIO | **ROW10** → Membrane row 10 |
| GPB2 | GPIO | **ROW11** → Membrane row 11 |
| GPB3 | GPIO | **ROW12** → Membrane row 12 |
| GPB4 | GPIO | **ROW13** → Membrane row 13 |
| GPB5 | GPIO | **ROW14** → Membrane row 14 |
| GPB6 | GPIO | **ROW15** → Membrane row 15 |
| GPB7 | GPIO | **ROW16** → Membrane row 16 |

---

### MCP23017 #2 - U3 (I2C Address: 0x21)
**Function**: 8 Keyboard Matrix Columns

| Pin | Function | Connection |
|-----|----------|------------|
| VDD | Power | 3V3 from XIAO |
| VSS | Ground | GND |
| SDA | I2C Data | Shared I2C bus |
| SCL | I2C Clock | Shared I2C bus |
| A0 | Address | Connected to VCC |
| A1, A2 | Address | Connected to GND (Address = 0x21) |
| RESET | Reset | Pull-up to VCC |
| GPA0 | GPIO | **COL1** → Membrane column 1 |
| GPA1 | GPIO | **COL2** → Membrane column 2 |
| GPA2 | GPIO | **COL3** → Membrane column 3 |
| GPA3 | GPIO | **COL4** → Membrane column 4 |
| GPA4 | GPIO | **COL5** → Membrane column 5 |
| GPA5 | GPIO | **COL6** → Membrane column 6 |
| GPA6 | GPIO | **COL7** → Membrane column 7 |
| GPA7 | GPIO | **COL8** → Membrane column 8 |
| GPB0-GPB7 | GPIO | Unused (available for expansion) |

---

## I2C Address Configuration

### MCP23017 #1 (0x20):
- A0 = GND
- A1 = GND
- A2 = GND
- Result: 0b0100000 = 0x20

### MCP23017 #2 (0x21):
- A0 = VCC (3V3)
- A1 = GND
- A2 = GND
- Result: 0b0100001 = 0x21

---

## Membrane Keyboard Matrix Connections

Connect your membrane keyboard's ribbon cables:
- **ROW1-16**: Connect to MCP23017 #1 (U2) pins GPA0-GPA7, GPB0-GPB7
- **COL1-8**: Connect to MCP23017 #2 (U3) pins GPA0-GPA7

The matrix scanning works as follows:
1. Firmware drives each column LOW one at a time
2. Firmware reads all row pins
3. If a key is pressed, the corresponding row will be pulled LOW
4. Key position = (row, column)

---

## Rotary Encoder Connections (Future)

Available XIAO GPIO pins for rotary encoder:
- **Encoder A**: D0 (P0.02)
- **Encoder B**: D1 (P0.03)
- **Encoder Switch**: D2 (P0.28)
- Additional pins: D3, D6, D7, D8, D9, D10

---

## Power Supply

- Input: USB-C on Seeeduino XIAO BLE (5V)
- Regulated Output: 3.3V from XIAO's onboard regulator
- Current Draw:
  - XIAO BLE: ~20mA active, <5µA sleep
  - Each MCP23017: ~1mA typical
  - Total: <25mA (well within USB current limits)

---

## Important Notes

1. **Decoupling Capacitors**: Add 0.1µF ceramic capacitors between VDD and VSS/GND pins of each MCP23017, placed as close as possible to the IC.

2. **Pull-up Resistors**: The firmware configures internal pull-ups on the row pins, so no external resistors needed for the matrix.

3. **I2C Pull-ups**: The XIAO BLE board typically has I2C pull-up resistors onboard. If not, add 4.7kΩ resistors from SDA and SCL to 3V3.

4. **Reset Pins**: Both MCP23017 RESET pins should be pulled HIGH (3V3) for normal operation. Use a 10kΩ resistor.

5. **PCB Considerations**:
   - Keep I2C traces (SDA/SCL) short and parallel
   - Avoid routing I2C lines near high-frequency signals
   - Use ground plane for better signal integrity
   - Consider ESD protection on the USB port

---

## Firmware Configuration

Your ZMK configuration files are already set up correctly:
- `boards/shields/adbk2/boards/seeeduino_xiao_ble.overlay` - Defines both MCP23017s
- `boards/shields/adbk2/adbk2.dtsi` - Matrix configuration (16 rows, 8 columns)
- `boards/shields/adbk2/adbk2.keymap` - Key mappings

After building and flashing the firmware, your keyboard should work immediately!
