# ESP32 RFID Traffic Override & Firebase Telemetry Receiver

[![Platform](https://img.shields.io/badge/Platform-PlatformIO-orange.svg)](https://platformio.org/)
[![Hardware](https://img.shields.io/badge/Hardware-ESP32--DevKit-blue.svg)](https://www.espressif.com/)
[![RFID](https://img.shields.io/badge/RFID-MFRC522-lightblue.svg)](https://www.nxp.com/)
[![Database](https://img.shields.io/badge/Database-Firebase%20RTDB-yellow.svg)](https://firebase.google.com/)

An ESP32-based intelligent traffic gateway integrating an RC522 RFID reader with hardware UART communications and Google Firebase Realtime Database. It monitors road vehicle congestion and dynamic timers while allowing authorized personnel (such as emergency or traffic enforcement officers) to manually override signal phases using RFID authentication.

---

## Features

- **RFID Priority Override**: Continuously polls the MFRC522 reader. When an authorized UID (`37 E0 96 04`) is scanned, issues a `"Manual"` command over UART to switch upstream traffic controller states and sets cloud `/Mode` to `2`.
- **Road Metric Ingestion**: Ingests traffic density metrics (`R1`, `R1T`, `R2`, `R2T`, `Mode`) over hardware UART `Serial1` (GPIO 16 RX / GPIO 17 TX at 115200 baud).
- **Firebase Cloud Synchronization**: Streams current road metrics to Firebase Realtime Database paths `/R1C`, `/R1T`, `/R2C`, `/R2T`, and `/Mode`.
- **Diagnostic Logging**: Outputs full UID hex dumps, authorization state, and parsed traffic frames over the primary USB serial monitor.

---

## Hardware & Pinout

### 1. RC522 RFID Module (SPI)

| RC522 Pin | ESP32 GPIO | Description |
| :--- | :--- | :--- |
| **VCC** | `3.3V` | 3.3V Power Supply ONLY |
| **RST** | `GPIO 4` | Reset Pin (`RST_PIN`) |
| **SDA / SS** | `GPIO 5` | Slave Select (`SS_PIN`) |
| **SCK** | `GPIO 18` | SPI Clock (`SCK_PIN`) |
| **MISO** | `GPIO 19` | Master In Slave Out (`MISO_PIN`) |
| **MOSI** | `GPIO 23` | Master Out Slave In (`MOSI_PIN`) |
| **GND** | `GND` | Common Ground |

### 2. UART Serial1 Interface

| ESP32 Pin | Signal | Target Device | Notes |
| :--- | :--- | :--- | :--- |
| `GPIO 16` | `RX1` | Upstream Controller (TX) | Data input line |
| `GPIO 17` | `TX1` | Upstream Controller (RX) | Command output line |

---

## Software Dependencies & Configuration

From [`platformio.ini`](platformio.ini):

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
lib_deps = 
    mobizt/Firebase ESP32 Client@^4.4.17
    miguelbalboa/MFRC522@^1.4.12
```

### Credentials Setup
Update credentials in [`src/main.cpp`](src/main.cpp):

```cpp
#define FIREBASE_PROJECT_API_KEY "YOUR_API_KEY"
#define FIREBASE_DATABASE_URL    "https://your-project.firebaseio.com/"
#define FIREBASE_EMAIL           "your-email@example.com"
#define FIREBASE_PASSWORD        "your-password"

#define WIFI_SSID                "YOUR_WIFI_SSID"
#define WIFI_PASSWORD            "YOUR_WIFI_PASSWORD"
```

---

## Build & Flash

```bash
# Compile
pio run

# Flash to device
pio run --target upload

# Serial monitor
pio device monitor -b 115200
```

---

## Project Structure

```
espreciver2/
├── src/
│   └── main.cpp         # RC522 SPI scanning, UART parsing & Firebase cloud sync
└── platformio.ini       # PlatformIO build configuration
```

---

## License

MIT License.
