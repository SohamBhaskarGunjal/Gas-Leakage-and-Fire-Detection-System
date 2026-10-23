# 🚨 Smart IoT Gas Leakage and Fire Detection System

> **An intelligent, low-latency, multi-tier safety system combining dual-hazard sensing (MQ Gas + IR Flame), edge noise filtering, automated mechanical servo valve cut-off, human-intelligible vocal guidance, and instant Wi-Fi/cloud alerts.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Hardware-ESP32%20%7C%20NodeMCU-orange.svg)](#hardware-specifications)
[![Framework](https://img.shields.io/badge/Framework-Arduino%20C%2B%2B-blue.svg)](#software-and-libraries)
[![Documentation](https://img.shields.io/badge/Docs-MPCA%20Capstone%20Report-red.svg)](docs/MPCA%20CAPSTONE%20HARDWARE%20REPORT.pdf)
[![Live Demo](https://img.shields.io/badge/Demo-Watch%20Video-green.svg)](demo/Gas%20Leakage%20and%20Fire%20Detection%20System%20live%20demo.mp4)

---

## 📑 Quick Links
- 📹 **[Watch Live Demo Video](demo/Gas%20Leakage%20and%20Fire%20Detection%20System%20live%20demo.mp4)**
- 📄 **[Read MPCA Capstone Hardware Report (PDF)](docs/MPCA%20CAPSTONE%20HARDWARE%20REPORT.pdf)** | **[Download DOCX](docs/MPCA%20CAPSTONE%20HARDWARE%20REPORT.docx)**
- 💻 **[View Main Arduino Firmware](ESP32_Gas_Fire_System/ESP32_Gas_Fire_System.ino)**

---

## 👥 Project Team & Academic Credentials

**Department of Computer Science and Engineering (Data Science)**  
**St. John College of Engineering and Management (Autonomous), University of Mumbai**

| Student Name | PID No. | Role / Designation | Key Responsibilities |
| :--- | :--- | :--- | :--- |
| **Soham Gunjal** | `EU8057` | System Architect & Embedded Developer | Overall system design, embedded C++ firmware, noise filtering, servo logic, hardware integration |
| **Aditi Patil** | `EU8059` | Hardware & Sensor Interfacing Lead | Circuit schematic formulation, sensor hardware calibration, power distribution |
| **Suraj Sawant** | `EU8060` | IoT & Cloud Telemetry Specialist | Wi-Fi networking, webhook integrations, latency testing, telemetry dispatch |
| **Shubhra Shinde** | `EU8061` | Audio / Alert Actuators & QA Lead | ISD1820 voice module integration, acoustic/visual driver validation, documentation |

- **Project Guide:** Mr. Sachin Sase *(Assistant Professor, Dept. of CSE (Data Science), SJCEM)*
- **Head of Department:** Mrs. Joslyn Gracias *(Dept. of CSE (Data Science), SJCEM)*
- **Principal:** Dr. Kamal Shah *(St. John College of Engineering and Management)*

---

## 🔍 System Overview

Liquefied Petroleum Gas (LPG) and open flame hazards represent severe safety risks across domestic kitchens, commercial cafeterias, and institutional chemical laboratories. Conventional warning systems suffer from single-hazard monitoring, lack spoken evacuation instructions, and fail to autonomously isolate the gas source.

This project delivers a **dual-hazard edge safety controller** that combines:
1. **Multi-Hazard Sensor Array**: MQ combustible gas sensor + optical IR flame photodiode sensor.
2. **Noise Suppression Routine**: Multi-sample digital moving-average / confirmation filter to suppress false alarms caused by electrical noise or transient drafts.
3. **Automated Mechanical Shutoff**: SG90 micro-servo motor acting as an automatic cylinder regulator / valve cut-off switch.
4. **Multi-Tier Audio-Visual Alerting**:
   - Tri-color status indicators: **Green** (Safe/Normal), **Yellow** (Gas Warning), **Red** (Flame Hazard / Critical Emergency).
   - High-decibel piezo acoustic buzzer.
   - ISD1820 Voice Recording & Playback module broadcasting spoken emergency guidance (*"Warning! Gas leakage detected, vacate and ventilate the premises"*).
5. **Fail-Safe Offline Autonomy**: Full local detection, servo valve cutoff, and siren/voice alerting operate instantaneously with or without Wi-Fi connection.
6. **Instant Telemetry & Cloud Alerts**: Non-blocking background Wi-Fi connectivity triggering instant email dispatches via Google Apps Script Webhooks.

---

## 🛠️ Hardware Pinout & Wiring Specification

| Component / Subsystem | Module Pin | ESP32 GPIO Pin | Function / Logic Description |
| :--- | :--- | :--- | :--- |
| **MQ Gas Sensor Module** | Digital Out (`DO`) | `GPIO 27` | Active `LOW` on gas detection; 4-sample confirmation |
| **IR Flame Sensor Module** | Digital Out (`DO`) | `GPIO 26` | Active `LOW` on optical flame detection; 4-sample confirmation |
| **Green Status LED** | Anode (`+`) | `GPIO 18` | Active `HIGH`: Safe / Normal monitoring state |
| **Yellow Alert LED** | Anode (`+`) | `GPIO 19` | Active `HIGH`: Combustible gas leak warning |
| **Red Alert LED** | Anode (`+`) | `GPIO 23` | Active `HIGH`: Flame / Critical emergency state |
| **ISD1820 Voice Module** | `PLAYE` (Edge Play) | `GPIO 25` | Active `HIGH` pulse to trigger vocal evacuation prompt |
| **SG90 Servo Motor** | Signal (PWM) | `GPIO 13` | `0°` = Open (Normal) \| `90°` = Cut-off / Closed (Hazard) |
| **Power Supply** | `VCC` / `VIN` | `5V` Rail | Regulated 5V DC power with shared common `GND` |

---

## ⚙️ Operating Logic & State Machine

```
              +----------------------------+
              |     Power-On / Reset       |
              +--------------+-------------+
                             |
                             v
              +----------------------------+
              | 30s Sensor Preheating &    |
              | Wi-Fi Background Connect   |
              +--------------+-------------+
                             |
                             v
              +----------------------------+
    +-------->|    Continuous Sampling     |<--------+
    |         | (100ms interval, 4-sample) |         |
    |         +--------------+-------------+         |
    |                        |                       |
    |           [Hazard Detected? (Gas/Fire)]        |
    |                   /        \                   |
    |              NO  /          \  YES             |
    |                 v            v                 |
    |     +---------------+    +-------------------+ |
    |     | Green LED ON  |    | - Move Servo 90°  | |
    |     | Servo at 0°   |    | - Yellow/Red LED  | |
    |     | Buzzer/Voice  |    | - Voice Alert     | |
    |     | OFF           |    | - Cloud Webhook   | |
    |     +-------+-------+    +---------+---------+ |
    |             |                      |           |
    +-------------+                      +-----------+
```

### Response Characteristics
- **Stabilization Cycle**: 30-second initial warm-up ignores cold-sensor baseline drift.
- **Gas Leak Response Time**: $< 2.0$ seconds from volatile hydrocarbon contact to alarm and valve cut-off.
- **Flame Response Time**: $< 1.0$ second optical IR detection latency.
- **Fail-Safe Mechanism**: The valve automatically isolates the fuel flow upon alert and resets automatically once atmospheric levels return to safe baselines.

---

## 📁 Repository Structure

```
├── demo/
│   └── Gas Leakage and Fire Detection System live demo.mp4  # Working prototype demonstration video
├── docs/
│   ├── MPCA CAPSTONE HARDWARE REPORT.pdf                    # Full MPCA capstone hardware report (PDF)
│   ├── MPCA CAPSTONE HARDWARE REPORT.docx                   # Report source document (DOCX)
│   └── circuit_diagram.png                                 # Circuit schematic and connection diagram
├── ESP32_Gas_Fire_System/
│   └── ESP32_Gas_Fire_System.ino                           # Complete ESP32 production firmware
├── sketch_sep28a/
│   └── sketch_sep28a.ino                                   # Arduino IDE project sketch
├── MPCA CAPSTONE HARDWARE REPORT.pdf                        # Direct repository report link (PDF)
├── MPCA CAPSTONE HARDWARE REPORT.docx                       # Direct repository report link (DOCX)
├── LICENSE                                                 # MIT License
└── README.md                                               # Comprehensive project documentation
```

---

## 🚀 Getting Started

### 1. Prerequisites
- [Arduino IDE](https://www.arduino.cc/en/software) (Version 2.0+ recommended)
- ESP32 Board Package installed via Arduino Boards Manager:
  ```
  https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
  ```

### 2. Required Libraries
Install the following libraries using the **Arduino Library Manager** (`Ctrl + Shift + I`):
- `ESP32Servo` by Kevin Harrington / John K. Bennett
- `WiFi` (Built-in ESP32 core)
- `WiFiClientSecure` (Built-in ESP32 core)
- `HTTPClient` (Built-in ESP32 core)

### 3. Firmware Configuration
Open `ESP32_Gas_Fire_System/ESP32_Gas_Fire_System.ino` and update your Wi-Fi credentials and Google Apps Script Webhook URL:
```cpp
// Wi-Fi Credentials
const char* WIFI_SSID     = "YOUR_WIFI_SSID";
const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";

// Google Apps Script Webhook URL (For instant Gmail alerts)
const char* GOOGLE_SCRIPT_URL = "https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec";
```

### 4. Uploading to ESP32
1. Connect your ESP32 board via USB.
2. Select your board under **Tools > Board > ESP32 Arduino > DOIT ESP32 DEVKIT V1** (or your specific module).
3. Select the correct **COM Port**.
4. Set Baud Rate to `115200` in the Serial Monitor.
5. Click **Upload** (`Ctrl + U`).

---

## 🎥 Live Demo

A complete physical test demonstrating butane gas leak detection, optical flame triggering, SG90 servo shutoff action, ISD1820 voice announcement, and status LED transitions is recorded in:
👉 **[Watch Demo Video (`demo/Gas Leakage and Fire Detection System live demo.mp4`)](demo/Gas%20Leakage%20and%20Fire%20Detection%20System%20live%20demo.mp4)**

---

## 📜 Academic Affiliation & References
- **Institution:** St. John College of Engineering and Management, Palghar, Mumbai (University of Mumbai).
- **Standards Complied With:** ISO/IEC 60079-29-1 (Flammable gas detectors) & IS 1641:1988 (Fire safety codes).
- **UN Sustainable Development Goals (SDGs) Addressed:**
  - **SDG 3:** Good Health and Well-Being (Target 3.9)
  - **SDG 9:** Industry, Innovation, and Infrastructure (Target 9.4)
  - **SDG 11:** Sustainable Cities and Human Settlements (Target 11.5)

---

## 📄 License
This project is open-source under the [MIT License](LICENSE).
