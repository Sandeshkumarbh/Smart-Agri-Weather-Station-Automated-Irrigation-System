# Smart-Agri-Weather-Station-Automated-Irrigation-System

An ESP32-based IoT smart agriculture system that monitors environmental and soil conditions and automatically controls a water pump based on soil moisture. Sensor data is displayed locally on a 20×4 I2C LCD and remotely through a Blynk IoT dashboard.

Project Overview
The system combines multiple sensors with an ESP32 to monitor agricultural conditions in real time.
The ESP32 collects:
Air temperature
Air humidity
Soil moisture
Soil temperature
Atmospheric pressure
Gas/smoke sensor level
Based on soil moisture, the ESP32 automatically controls a relay connected to a water pump.
Sensor data is sent to the Blynk Cloud through Wi-Fi for remote monitoring.

Features
Real-time environmental monitoring
Automatic irrigation based on soil moisture
Remote monitoring using Blynk IoT
20×4 I2C LCD local display
Multiple sensor integration
ESP32 Wi-Fi connectivity
Serial Monitor debugging
Wokwi simulation support

Hardware Components
| Component                            | Purpose                    | ESP32 Pin      |
| ------------------------------------ | -------------------------- | -------------- |
| ESP32 DevKit                         | Main controller            | —              |
| DHT22                                | Air temperature & humidity | GPIO 4         |
| DS18B20                              | Soil temperature           | GPIO 18        |
| Soil Moisture Sensor / Potentiometer | Soil moisture simulation   | GPIO 35        |
| MQ-2                                 | Gas/smoke sensing          | GPIO 34        |
| BMP180                               | Atmospheric pressure       | I2C            |
| 20×4 I2C LCD                         | Local display              | SDA 21, SCL 22 |
| Relay Module                         | Water pump control         | GPIO 26        |
| Water Pump                           | Irrigation actuator        | Relay          |


Pin Configuration
DHT22
DATA  → GPIO 4
VCC   → 5V
GND   → GND

DS18B20
DQ    → GPIO 18
VCC   → 5V
GND   → GND

Soil Moisture Sensor
AOUT  → GPIO 35
VCC   → 5V
GND   → GND

MQ-2
AOUT  → GPIO 34
VCC   → 5V
GND   → GND

BMP180
SDA   → GPIO 21
SCL   → GPIO 22

LCD 20×4 I2C
SDA   → GPIO 21
SCL   → GPIO 22
VCC   → 5V
GND   → GND

Relay
IN    → GPIO 26
VCC   → 5V
GND   → GND

Software & Libraries

 Development
  ESP32
  Arduino framework
  Wokwi
  Blynk IoT
  Git & GitHub

 Arduino Libraries
  WiFi
  Blynk
  Wire
  LiquidCrystal_I2C
  DHT sensor library
  OneWire
  DallasTemperature
  Adafruit BMP085 Library

How the System Works
DHT22 ─────────────┐
DS18B20 ───────────┤
Soil Moisture ─────┤
MQ-2 ──────────────┤
BMP180 ────────────┤
                   ↓
                 ESP32
                   ↓
        ┌──────────┴──────────┐
        ↓                     ↓
   LCD Display          Blynk Cloud
                              ↓
                       Blynk Dashboard

Soil Moisture
      ↓
Decision Logic
      ↓
   ESP32
      ↓
    Relay
      ↓
 Water Pump


 Automatic Irrigation Logic
The system uses two thresholds:
Soil moisture < 30%
        ↓
    Pump ON

Soil moisture > 60%
        ↓
    Pump OFF
Between 30% and 60%, the pump maintains its previous state. This provides hysteresis and prevents rapid ON/OFF switching around a single threshold.


Blynk Datastreams
| Virtual Pin | Parameter        |
| ----------- | ---------------- |
| V0          | Air Temperature  |
| V1          | Air Humidity     |
| V2          | Soil Moisture    |
| V3          | Soil Temperature |
| V4          | Air Pressure     |
| V5          | Gas Level        |
| V6          | Pump Status      |

LCD Display
The LCD displays:
Temp:24.0 C
Hum:40.0%
Soil:65%
ST:25.0 P:OFF

Blynk Dashboard
The Blynk dashboard provides remote monitoring of:
Air temperature
Air humidity
Soil moisture
Soil temperature
Air pressure
Gas sensor level
Pump status


Running the Project in Wokwi
Open the Wokwi project.
Add the required libraries.
Open sketch.ino.
Add your own Blynk credentials.
Start the simulation.
Open the Serial Monitor.
Open your Blynk dashboard.
Change the simulated soil moisture value to test automatic irrigation.


Project Structure
Smart-Agriculture/
│
├── sketch.ino
├── diagram.json
├── libraries.txt
└── README.md

Future Improvements
Real soil moisture sensor calibration
Real water pump and power supply
Mobile pump control
Blynk notifications
Low-water-level detection
Rain detection
Data logging
Historical graphs
Deep-sleep power optimization
Real-time clock and irrigation scheduling
Solar-powered operation

Limitations
This version was developed and tested using Wokwi simulation. Sensor readings in simulation do not represent real physical measurements.
The MQ-2 value is treated as a raw ADC reading and is not calibrated to a gas concentration such as ppm.
For a real deployment, sensor calibration, electrical isolation, power management, waterproofing, and safe pump/relay wiring would be required.

Project Status
Completed prototype: ESP32 + multi-sensor monitoring + automatic irrigation + LCD + Blynk IoT dashboard.
Author

Sandesh Kumar BH
ECE Student | Embedded Systems & IoT Enthusiast
