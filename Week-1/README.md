# Week 1 Outputs

You can check all Week-1 codes, images, and outputs here:
🔗 https://github.com/<Gopinayak02>/PNT-Lab-HANUMA-Weekly-Reports/tree/main/Week-1

# Week–1 Report: HANUMA Rover Development  
Duration:  1 Sept 2025 – 7 Sept 2025  
Project:   HANUMA – Hazard Analysis & Navigation Unit for Mine Assistance  
Interns:   B. Venkat Gopi Nayak & M. Bindu Madhavi  
Guided by: Dr. Shaik Fayaz Ahamed, SAHE  

---

# 1. Objective for Week–1
- Finalize complete system architecture for the HANUMA rover.  
- Identify all required components, sensors, and control modules.  
- Test basic rover mobility using L298N motor driver.  
- Establish ESP-NOW communication between transmitter & receiver modules.

## **motor_test.ino**
```cpp
// Basic motor driver test using L298N
int in1 = 5;
int in2 = 6;
int in3 = 9;
int in4 = 10;

void setup() {
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
}

void loop() {
  // forward
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  delay(2000);

  // backward
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  delay(2000);
}

🔹 espnow_rx.ino
// ESP-NOW Receiver (basic test)
#include <esp_now.h>
#include <WiFi.h>

typedef struct struct_message {
  int command;
} struct_message;

struct_message incomingData;

void OnDataRecv(const uint8_t * mac, const uint8_t *incomingDataBuffer, int len) {
  memcpy(&incomingData, incomingDataBuffer, sizeof(incomingData));
  Serial.print("Received Command: ");
  Serial.println(incomingData.command);
}

void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  esp_now_init();
  esp_now_register_recv_cb(OnDataRecv);
}

void loop() {}

ESP32 – Get MAC Address (Receiver)
cpp
#include <WiFi.h>

void setup() {
  Serial.begin(115200);

  // Put Wi-Fi into station mode (required for ESP-NOW)
  WiFi.mode(WIFI_STA);

  // Print MAC Address
  Serial.print("ESP32 Receiver MAC Address: ");
  Serial.println(WiFi.macAddress());
}

void loop() {

}

RESULT YOU WILL SEE IN SERIAL MONITOR
css
//example ESP32 Receiver MAC Address: 7C:DF:A1:45:9B:22

🔹 espnow_tx.ino
cpp
// ESP-NOW Transmitter (basic test)
#include <esp_now.h>
#include <WiFi.h>

uint8_t broadcastAddress[] = {0xFF,0xFF,0xFF,0xFF,0xFF,0xFF};

typedef struct struct_message {
  int command;
} struct_message;

struct_message testData;

void setup() {
  WiFi.mode(WIFI_STA);
  esp_now_init();
  esp_now_add_peer(broadcastAddress, ESP_NOW_ROLE_CONTROLLER, 1, NULL, 0);
}

void loop() {
  testData.command = 1; // forward
  esp_now_send(broadcastAddress, (uint8_t *) &testData, sizeof(testData));
  delay(500);
}

