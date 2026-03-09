# ☕ Espresso Shot Terminator (ESPHome)

An ESPHome-powered automation that automatically stops your espresso machine's pump when a target weight is reached, using real-time BLE scale data.

## 🚀 Features
* **BLE Scale Integration:** Connects to WeighMyBru scale.
* **Auto-Stop Logic:** Fires a relay to stop the pump exactly at your target weight.
* **Smart Reset:** Automatically resets the "Armed" status when you remove the cup from the scale.
* **Home Assistant Dashboard:** Control target weight, arm/disarm, and monitor status in real-time.

## 🛠 Hardware Required
* **Microcontroller:** ESP32 (Required for BLE). https://www.aliexpress.com/item/1005005983735481.htm
* **Relay:** Relay module (connected to the machine's brew button)
* **Scale:** WeighMyBrew.

## 🔌 Wiring
| Component | ESP32 Pin |
| :--- | :--- |
| Relay Signal | GPIO16 |
| Relay VCC | 5V / VIN |
| Relay GND | GND |

## ⚙️ Installation

1.  **Prepare Secrets:** Create a `secrets.yaml` file in your ESPHome directory (use `secrets.yaml.example` as a template).
2.  **Flash ESP32:** Use the ESPHome dashboard or command line to flash `coffee-relay.yaml` to your device.
3.  **Add to Home Assistant:** The device will be auto-discovered. Add the entities to your dashboard for control.

## 📝 Usage
1.  Set your **Target Weight** (e.g., 36.0g) in Home Assistant.
2.  Press **Arm Shot Stop**.
3.  Start your espresso shot.
4.  The relay will trigger and stop the flow once the weight is met!

---
*Disclaimer: Modifying espresso machines involves high voltage. Proceed at your own risk.*
