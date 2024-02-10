# Build your own Srcful Energy Gateway

To build your own Srcful Energy Gateway, you will need the following hardware:

- x1 Raspberry Pi 4 (4GB or 8GB)
- x1 Secure Element, such as the ATECC608A or ATECC608B (recommended)
- x1 Groove connector to female jumpers
- x1 MicroSD card (16GB or larger)
- x1 Power supply (5V, 3A)
- x1 Ethernet cable
- x1 Case (optional)

## Connect the Hardware

The secure element uses i2c to communicate with the Raspberry Pi. Connect the secure element to the Raspberry Pi using the following pinout:

| Pin on Secure Element | Connects to on Raspberry Pi |
| --------------------- | --------------------------- |
| SCL (Pin 1)           | SCL (GPIO 3)                |
| SDA (Pin 2)           | SDA (GPIO 2)                |
| 5V (Pin 3)            | 5V Power (Pin 4)            |
| GND (Pin 4)           | Ground (Pin 6)              |

## Prepare the Raspberry Pi

Once you have connected the secure element to the Raspberry Pi, you can prepare the Raspberry Pi by following this guide: [Getting started with the Srcful Energy Gateway](diy-oss.md)
