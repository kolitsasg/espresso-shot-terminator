# ☕ Espresso Shot Terminator (ESPHome)

An ESPHome-powered automation that automatically stops your espresso machine's pump when a target weight is reached, using real-time BLE scale data from a WeighMyBru² scale.

## 🚀 Features

* **BLE Scale Integration:** Connects wirelessly to the [WeighMyBru²](https://github.com/031devstudios/weighmybru2) scale via Bluetooth Low Energy, reading real-time weight from the Bean Conqueror characteristic (`6E400004`).
* **Auto-Stop on Weight:** Fires a relay to stop the pump the moment your target weight is reached — no manual intervention needed during the shot.
* **Always Armed:** The system is armed automatically as soon as the BLE connection is established. No buttons to press, no app to open.
* **Smart Reset:** Automatically resets when you remove the cup from the scale (weight drops below 1g), ready for the next shot.
* **Home Assistant Integration:** Set target weight, monitor live shot weight, and test the relay from your HA dashboard.
* **Drip-Through Compensation:** Set your target weight slightly below your desired yield to account for water that drips through after the pump stops (typically 3–5g depending on your machine).

## 🛠 Hardware

| Component | Notes |
| :--- | :--- |
| **ESP32 Relay Board** | ESP32-WROOM-32E with onboard relay. [AliExpress link](https://www.aliexpress.com/item/1005005983735481.htm) |
| **Scale** | [WeighMyBru²](https://weighmybru.com) — open-source smart espresso scale with BLE |
| **3D Printed Case** | Enclosure for the ESP32 relay board: [MakerWorld](https://makerworld.com/models/1951211?appSharePlatform=copy) |

## 🔌 Wiring

### Relay to Brew Switch (Lelit Elizabeth)

The Lelit Elizabeth's brew button has a 3-pin connector with three wires:

| Wire Position | Function |
| :--- | :--- |
| Edge wire (white) | **Ground** |
| Middle wire | LED indicator |
| Other edge wire | **Brew signal** (12.5V when active) |

Connect the relay output in parallel with the brew switch:

* **Relay COM** → Brew signal wire (edge)
* **Relay NO** → Ground wire (white, other edge)

When the relay fires, it momentarily shorts the brew signal to ground — the same as pressing the brew button — which toggles the brew off.

### Powering the ESP32 Relay Board

The ESP32 relay board is powered from **mains voltage (AC 90–250V)**. Connect the board's L and N terminals to the power switch on the side of the Lelit Elizabeth, so the relay board turns on and off with the machine.

### ESP32 Pin Assignment

| Function | GPIO |
| :--- | :--- |
| Relay control | GPIO16 |

## ⚙️ Installation

1. **Prepare Secrets:** Create a `secrets.yaml` file in your ESPHome directory:
   ```yaml
   wifi_ssid: "your-wifi-name"
   wifi_password: "your-wifi-password"
   ```

2. **Update MAC Address:** In `coffee-relay.yaml`, replace the MAC address with your WeighMyBru scale's address:
   ```yaml
   ble_client:
     - mac_address: XX:XX:XX:XX:XX:XX  # Your scale's MAC
   ```
   You can find this using the nRF Connect app on your phone, or from the ESPHome BLE tracker logs.

3. **Flash ESP32:** Use the ESPHome dashboard or command line to flash `coffee-relay.yaml` to your device.

4. **Add to Home Assistant:** The device will be auto-discovered. Add the entities to your dashboard.

## 📝 Usage

1. **Turn on the machine** — the ESP32 boots and auto-connects to the scale.
2. **Place your cup on the scale** — the scale auto-tares on startup.
3. **Set your Target Weight** in Home Assistant (e.g., 32g for a 36g yield with ~4g drip-through).
4. **Press the brew button on the machine** — espresso starts flowing.
5. **The relay fires automatically** when the target weight is reached, stopping the pump.
6. **Remove the cup** — the system resets and is ready for the next shot.

## 🔧 BLE Protocol Notes

The WeighMyBru² scale exposes a Nordic UART Service (NUS) with several characteristics:

| UUID | Function |
| :--- | :--- |
| `6E400001` | NUS Service |
| `6E400002` | GaggiMate weight data (custom binary protocol) |
| `6E400003` | Command channel (write commands, receive heartbeats) |
| `6E400004` | **Bean Conqueror weight data (4-byte little-endian float)** ← used by this project |

This project subscribes to `6E400004` notifications which deliver weight as a raw IEEE 754 float — the simplest format to parse.

## ⚠️ Disclaimer

Modifying espresso machines involves **mains voltage (120/230V AC)**. Incorrect wiring can cause electric shock, fire, or equipment damage. If you are not comfortable working with mains voltage, have a qualified electrician perform the installation. **Proceed at your own risk.**

## 🙏 Credits

* [WeighMyBru²](https://github.com/031devstudios/weighmybru2) by 031 Dev Studios — the open-source scale that makes this possible
* [ESPHome](https://esphome.io) — the firmware framework
* [Home Assistant](https://www.home-assistant.io) — the smart home platform
* WeighMyBru Discord community for protocol guidance
