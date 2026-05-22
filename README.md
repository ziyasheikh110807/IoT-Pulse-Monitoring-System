IoT-Based Physiological Monitoring & Telemetry System

An end-to-end biomedical IoT application built on the Arduino Giga R1 WiFi architecture designed to capture, process, and stream real-time physiological data to the cloud for remote health analytics.

🛠️ System Architecture & Tech Stack

Hardware: Arduino Giga R1 WiFi, Photoplethysmography (PPG) Heartbeat Sensor, OLED Display Modules
Firmware: Embedded C++ (Arduino IDE)
Cloud/IoT: ThingSpeak REST API Protocol

📊 Core Features

Biometric Signal Processing: Real-time analog-to-digital conversion (ADC) of PPG optical sensor signals to calculate stable Beats Per Minute (BPM).

Edge Visualization: Local, low-latency data rendering on an integrated OLED display for immediate feedback.

Cloud Telemetry Pipeline: Established a secure client connection to stream calculated physiological metrics directly to a dedicated remote IoT channel.

Data Synchronization: Implemented non-blocking delay parameters to handle stable network sockets and prevent data collision during cloud transmission.

💾 Core Logic Snippet

C++
// --- Peripheral Initialization Matrix ---
#define BLUE_LED 5
#define RED_LED 4
#define YELLOW_LED 7

// ADC Multi-Channel Telemetry Targets
const int sensorPin = A0; 
int threshold = 525;      // Firmware-side peak detection configuration
unsigned long lastUploadTime = 0;


📈 Cloud Telemetry

The system transmits real-time data streams to a cloud-hosted dashboard, mapping physiological metrics onto an analytics platform to track variations and baseline trends over time.

Developed as part of my Electronics and Communication Engineering portfolio.

## 📊 Live Signal Processing Waveform

Real-time analog photoplethysmography (PPG) signal acquisition displayed on the IDE Serial Plotter, mapping the continuous pulse waveform alongside steady real-time Beats Per Minute (BPM) tracking.

![Physical Hardware System Layout](Screenshot%202026-05-22%20at%201.36.06%20PM.png)

![ThingSpeak Output Graph](Screenshot%202026-05-22%20at%201.36.47%20PM.png)
