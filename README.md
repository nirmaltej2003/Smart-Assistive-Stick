# 🦯 Smart Assistive Stick

### Empowering the Visually Impaired with IoT and Embedded Systems

---

## 📘 Overview

The **Smart Assistive Stick** is an IoT-based innovation designed to enhance the **mobility, safety, and independence** of visually impaired individuals. It integrates multiple smart technologies such as **ultrasonic sensors, GPS, GSM, servo motors, and LCD display** to assist users in navigating safely while staying connected with caregivers in real time.

This project combines **hardware and software intelligence** to detect obstacles, track location, and send emergency alerts, ensuring both independence and safety.

---

## 🧩 Features

- **🔊 Real-Time Voice Guidance:** Provides obstacle alerts using a speaker.
- **📍 GPS Tracking:** Tracks the user’s live location.
- **📶 GSM Connectivity:** Sends SMS alerts to guardians in emergencies.
- **⚠️ Obstacle Detection:** Uses ultrasonic sensors for 360° scanning.
- **🆘 SOS Emergency Button:** Sends immediate location-based SMS during distress.
- **🔋 Power Efficient:** Rechargeable and optimized for long usage.
- **💡 LCD Display:** Displays real-time information like alerts and system status.

---

## 🧠 Technologies Used

### 🔹 Hardware Components
| Component | Function |
|------------|-----------|
| **Arduino Nano** | Central microcontroller for system operations |
| **Ultrasonic Sensor** | Detects obstacles and measures distances |
| **GPS Module** | Provides user’s real-time location |
| **GSM Module** | Sends SMS alerts for emergencies |
| **Servo Motor** | Enables rotation for broader detection |
| **LCD Display** | Shows obstacle and system data |
| **Speaker** | Provides audio feedback |
| **SOS Button** | Activates emergency alert |
| **Power Supply** | Ensures stable and portable power |

### 🔹 Software Tools
- **Arduino IDE** – Microcontroller programming
- **Embedded C** – Firmware development
- **Proteus Simulator** – Circuit testing
- **SolidWorks** – 3D modeling of the stick

---

## 🧾 Functional Description

1. **Obstacle Detection:**  
   Ultrasonic sensors detect nearby objects within a defined threshold (20–350 cm) and trigger an alert via buzzer or voice.

2. **Location Tracking:**  
   The GPS module continuously tracks coordinates and updates the caregiver via GSM.

3. **Emergency Alert System:**  
   On pressing the SOS button, an SMS containing the current location is sent to the guardian.

4. **User Interaction:**  
   The stick provides feedback through voice alerts, vibration, and an LCD display.

---

## 💻 Implementation Code (Arduino)

```cpp
#include <SoftwareSerial.h>
#include <TinyGPS.h>

#define trigPin 9
#define echoPin 10
#define buzzerPin 6
#define buttonPin 2
#define distanceThreshold 20

TinyGPS gps;
SoftwareSerial gpsSerial(3, 4);
SoftwareSerial gsmSerial(7, 8);

long duration;
int distance;
String emergencyContact = "+91XXXXXXXXXX";

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);
  Serial.begin(9600);
  gpsSerial.begin(9600);
  gsmSerial.begin(9600);
}

void loop() {
  checkForObstacle();
  if (digitalRead(buttonPin) == LOW) {
    sendEmergencySMS();
    delay(2000);
  }
}

void checkForObstacle() {
  digitalWrite(trigPin, LOW); delayMicroseconds(2);
  digitalWrite(trigPin, HIGH); delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;
  digitalWrite(buzzerPin, (distance < distanceThreshold) ? HIGH : LOW);
}

void sendEmergencySMS() {
  float lat, lon; unsigned long age;
  gps.f_get_position(&lat, &lon, &age);
  if (age != TinyGPS::GPS_INVALID_AGE) {
    String msg = "Emergency! Need help. Location: https://maps.google.com/?q=" + String(lat, 6) + "," + String(lon, 6);
    gsmSerial.println("AT+CMGF=1"); delay(1000);
    gsmSerial.println("AT+CMGS=\"" + emergencyContact + "\""); delay(1000);
    gsmSerial.println(msg); delay(100);
    gsmSerial.write(26); delay(5000);
    Serial.println("SMS Sent");
  }
}
