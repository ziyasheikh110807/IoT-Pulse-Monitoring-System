# IoT-Pulse-Monitoring-System
#include <WiFi.h>
#include "ThingSpeak.h"
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// ================= WIFI =================
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

// ============== THINGSPEAK ==============
unsigned long channelNumber = YOUR_CHANNEL_ID;
const char* writeAPIKey = "YOUR_WRITE_API_KEY";

WiFiClient client;

// ================= OLED =================
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// ============== PULSE SENSOR ============
const int sensorPin = A0;

int threshold = 525;

unsigned long lastBeatTime = 0;
int bpm = 0;

// ================= LEDS =================
#define BLUE_LED 5
#define RED_LED 4
#define YELLOW_LED 7

// ============== TIMER ===================
unsigned long lastUploadTime = 0;

void setup() {

  Serial.begin(115200);

  analogReadResolution(10);

  pinMode(BLUE_LED, OUTPUT);
  pinMode(RED_LED, OUTPUT);
  pinMode(YELLOW_LED, OUTPUT);

  // OLED Start
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED not found");
    while (1);
  }

  display.clearDisplay();
  display.display();

  // WiFi Connect
  WiFi.begin(ssid, password);

  Serial.print("Connecting WiFi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nWiFi Connected ✅");

  // ThingSpeak Start
  ThingSpeak.begin(client);
}

void loop() {

  int sensorValue = analogRead(sensorPin);

  // ===== HEARTBEAT DETECTION =====
  if (sensorValue > threshold) {

    unsigned long currentTime = millis();

    if (currentTime - lastBeatTime > 300) {

      bpm = 60000 / (currentTime - lastBeatTime);

      lastBeatTime = currentTime;
    }
  }

  // ===== LED CONTROL =====
  digitalWrite(BLUE_LED, LOW);
  digitalWrite(YELLOW_LED, LOW);
  digitalWrite(RED_LED, LOW);

  if (bpm > 60 && bpm < 100) {

    digitalWrite(BLUE_LED, HIGH);

  } else if (bpm <= 60 && bpm > 30) {

    digitalWrite(YELLOW_LED, HIGH);

  } else if (bpm >= 100) {

    digitalWrite(RED_LED, HIGH);
  }

  // ===== OLED DISPLAY =====
  display.clearDisplay();

  display.setTextSize(2);
  display.setTextColor(WHITE);
  display.setCursor(10, 5);
  display.print("BPM");

  display.setTextSize(3);
  display.setCursor(10, 30);
  display.print(bpm);

  display.setTextSize(1);
  display.setCursor(80, 5);

  if (bpm > 60 && bpm < 100) {

    display.print("NORMAL");

  } else if (bpm <= 60 && bpm > 30) {

    display.print("LOW");

  } else if (bpm >= 100) {

    display.print("HIGH");
  }

  display.display();

  // ===== SEND TO THINGSPEAK =====
  if (millis() - lastUploadTime > 10000) {

    ThingSpeak.setField(1, bpm);

    int x = ThingSpeak.writeFields(channelNumber, writeAPIKey);

    if (x == 200) {

      Serial.println("Data sent to ThingSpeak ✅");

    } else {

      Serial.println("ThingSpeak Error ❌");
    }

    lastUploadTime = millis();
  }

  // ===== SERIAL MONITOR =====
  Serial.print("Signal: ");
  Serial.print(sensorValue);

  Serial.print(" | BPM: ");
  Serial.println(bpm);

  delay(20);
}
