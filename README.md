# Sleepy: Wearable Sleep Monitoring and Feedback System

Sleepy is a two-device system that monitors sleep-related signals (movement, breathing audio, and environment)
and visualizes a nightly sleep quality score on a physical needle gauge with LEDs for quick interpretation.

**General sketch (physical features):**
![General Sketch](images/01_general_sketch.png)

---

## 1) Sensor Device (Bedside Sensing Unit)

**Devices / Parts**
- MCU + Wireless: ESP32-C3-WROOM-02 (BLE/Wi-Fi)
- IMU: Bosch BMI270 (motion / vibration)
- Microphone: INMP441 (I2S digital mic for breathing/snoring features)
- Temp/Humidity: Sensirion SHT31-D (sleep environment monitoring)
- Power: 3.7V LiPo (500–1000 mAh) + MCP73831 charger (+ optional MAX17048 fuel gauge)

**How it works (high level)**
The sensing unit collects motion and audio signals overnight. It performs basic DSP (filtering + windowing)
and feature extraction (movement variance, breathing sound energy, spectral bands), then computes a sleep score
(or sends features) to the display unit via BLE.

**Detailed sketch**
![Sensor Device Sketch](images/02_sensor_device_sketch.png)

---

## 2) Display Device (Gauge + LED Feedback Unit)

**Devices / Parts**
- MCU + Wireless: ESP32-C3-WROOM-02 (BLE)
- Stepper gauge motor: Switec X25.168
- Stepper driver: ULN2003A
- LED: WS2812B RGB LED (x1–3)
- Button: 6x6mm tactile switch
- Power: 3.7V LiPo (1000–2000 mAh) + MCP73831 charger + AP2112K-3.3 regulator

**How it works (high level)**
The display unit receives sleep score updates over BLE and maps the score to a needle position (0–100).
LED colors indicate status zones (e.g., green = good, yellow = moderate, red = poor). A button toggles modes
(live / last-night summary / pairing).

**Detailed sketch**
![Display Device Sketch](images/03_display_device_sketch.png)

---

## 3) System Communication + Detailed Diagram (2 figures total)

**Figure A — Communication overview**
![Communication Figure](images/04_comm_overview.png)

**Figure B — Detailed dataflow diagram**
![Dataflow Diagram](images/05_dataflow_diagram.png)

**Dataflow summary**
1) Sensor device samples IMU + Mic + Env sensors  
2) DSP: filtering, windowing, feature extraction  
3) ML / scoring: sleep quality score + status classification  
4) BLE transmission to display  
5) Display maps score to stepper needle + LED indicators; button changes display modes