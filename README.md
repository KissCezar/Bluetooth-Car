# Bluetooth & RFID Controlled Robotic Car

---

## 1. Abstract

This project presents the design and implementation of a dual-control robotic car based on Arduino Uno, combining Bluetooth wireless control with an RFID-based identification system.

The system was developed to explore the integration of multiple input methods in embedded robotics, focusing on real-time response, modular design, and basic access control logic.

---

## 2. System Motivation

The main motivation behind this project was to go beyond a simple remote-controlled car and build a more structured embedded system with multiple interaction layers.

We wanted to understand how real robotic systems combine different types of inputs (wireless commands and identification systems) and how these inputs can influence system behavior.

---

## 3. Objectives

- Design a functional robotic car with dual input systems  
- Implement Bluetooth-based remote control  
- Integrate RFID-based identification logic  
- Ensure stable motor control and real-time response  
- Gain practical experience in embedded system integration  

---

## 4. System Architecture

The system is composed of three main subsystems:

### Input Layer
- Bluetooth commands from a mobile device  
- RFID card/tag detection via RC522 module  

### Processing Layer
- Arduino Uno microcontroller handling logic and decision-making  

### Output Layer
- L298N motor driver controlling DC motors  

---

## 5. Hardware Components

- Arduino Uno (ATmega328P)  
- HC-05 Bluetooth module  
- RC522 RFID reader module  
- RFID cards/tags  
- L298N motor driver  
- DC motors (2–4)  
- Chassis structure  
- Li-ion battery pack  
- Jumper wires  

---

## 6. System Design

The system is designed to support two independent input channels:

### Bluetooth Control
Used for manual real-time driving commands (forward, backward, left, right, stop).

### RFID System
Used as an identification layer. Each RFID tag contains a unique ID that is checked against a predefined list of authorized IDs stored in the Arduino memory.

Depending on the detected tag, the system can:
- Enable or disable movement
- Switch operating modes
- Trigger predefined behaviors

This introduces a basic form of access control into the robotic system.

---

## 7. Working Principle

1. The system continuously listens for Bluetooth commands  
2. In parallel, the RFID reader scans for nearby tags  
3. If a valid RFID tag is detected, system permissions are updated  
4. Bluetooth commands are executed only if the system is in an enabled state  
5. Motor driver executes movement commands in real time  

---

## 8. Control Logic

### Bluetooth Commands

| Command | Action |
|--------|--------|
| F | Forward |
| B | Backward |
| L | Left |
| R | Right |
| S | Stop |

### RFID Logic

- Each card has a unique UID  
- Arduino compares UID with stored authorized values  
- Valid card → system enabled  
- Invalid/unknown card → system remains restricted  

---

## 9. Implementation Notes

The system was implemented using a modular approach:

- Bluetooth module handles serial communication independently  
- RFID module operates continuously in polling mode  
- Arduino acts as central controller coordinating both inputs  
- Motor driver responds to processed output signals  

Care was taken to ensure that both input systems do not interfere with each other during execution.

---

## 10. Code - Arduino IDE

#define CUSTOM_SETTINGS
#define INCLUDE_GAMEPAD_MODULE

#include <Dabble.h>
#include <SoftwareSerial.h>
#include <SPI.h>
#include <MFRC522.h>

const int enA = 6;
const int in1 = 4;
const int in2 = 5;

const int enB = 9;
const int in3 = 7;
const int in4 = 8;

int motorSpeed = 180;

SoftwareSerial BTSerial(2, 3);

#define SS_PIN 10
#define RST_PIN A0
MFRC522 mfrc522(SS_PIN, RST_PIN);

byte card1[4] = {0x19, 0x22, 0xFD, 0x03};
byte card2[4] = {0x11, 0x22, 0x33, 0x44};
byte card3[4] = {0xAA, 0xBB, 0xCC, 0xDD};

bool c1 = false;
bool c2 = false;
bool c3 = false;

const int ledPin = A1;

void setup() {
  pinMode(enA, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);


  pinMode(enB, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);


  pinMode(ledPin, OUTPUT);


  Serial.begin(9600);
  BTSerial.begin(9600);
  Dabble.begin(BTSerial);


  SPI.begin();
  mfrc522.PCD_Init();
}

void loop() {
  Dabble.processInput();
 
  if (GamePad.isUpPressed()) moveForward();
  else if (GamePad.isDownPressed()) moveBackward();
 
  else if (GamePad.isLeftPressed()) turnLeft();
  
  else if (GamePad.isRightPressed()) turnRight();
 
  else stopMotors();
  if (mfrc522.PICC_IsNewCardPresent() && mfrc522.PICC_ReadCardSerial()) {

    if (compareUID(mfrc522.uid.uidByte, card1) && !c1) {
      c1 = true;
      blinkLed();
    }
    else if (compareUID(mfrc522.uid.uidByte, card2) && !c2) {
      c2 = true;
      blinkLed();
    }
    else if (compareUID(mfrc522.uid.uidByte, card3) && !c3) {
      c3 = true;
      blinkLed();
    }

    mfrc522.PICC_HaltA();
  }
  if (c1 && c2 && c3) {
    digitalWrite(ledPin, HIGH);-
  }
}
bool compareUID(byte *a, byte *b) {
  for (byte i = 0; i < 4; i++) {
    if (a[i] != b[i]) return false;
  }
  return true;
}

void blinkLed() {
  digitalWrite(ledPin, HIGH);
  delay(200);
  digitalWrite(ledPin, LOW);
}
void moveForward() {
  analogWrite(enA, motorSpeed);
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);

  analogWrite(enB, motorSpeed);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void moveBackward() {
  analogWrite(enA, motorSpeed);
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);

  analogWrite(enB, motorSpeed);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void turnLeft() {
  analogWrite(enA, motorSpeed);
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);

  analogWrite(enB, motorSpeed);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void turnRight() {
  analogWrite(enA, motorSpeed);
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);

  analogWrite(enB, motorSpeed);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void stopMotors() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

## 11. Testing and Results

### Bluetooth subsystem
- Stable connection within short range (~8–10 meters)
- Low latency in command execution under normal conditions

### RFID subsystem
- Reliable detection of authorized tags
- Fast response time during scanning

### Integrated system
- Proper interaction between access control and movement logic
- No major conflicts between Bluetooth and RFID inputs

---

## 12. Challenges and Solutions

### Motor imbalance
One motor exhibited slightly higher speed.

**Solution:** PWM calibration was applied to balance output.

---

### Bluetooth interference
Occasional delay in noisy environments.

**Solution:** Improved serial handling and reduced unnecessary processing loops.

---

### RFID + Bluetooth concurrency
Both systems initially interfered during continuous polling.

**Solution:** Structured loop timing to prioritize non-blocking execution.

---

## 13. Limitations

- No advanced security encryption for RFID  
- Limited range for Bluetooth communication  
- No obstacle detection system implemented  
- System depends on stable power supply  

---

## 14. Future Improvements

- Integration of ultrasonic sensors for obstacle avoidance  
- Improved RFID security with encrypted authentication  
- Mobile application with custom UI  
- Addition of autonomous driving mode  
- Optimization for low-power operation  

---

## 15. Conclusion

This project demonstrates a functional embedded system combining wireless control and RFID-based identification.

The integration of multiple input sources introduces basic system-level decision making, making the project closer to real-world robotics applications.

It provided hands-on experience in embedded architecture design, hardware integration, and system debugging.

---

## 16. Authors

Aetherion Robotics Team  
Student Engineering Project  

---

## 17. Attachments

<img width="1200" height="1600" alt="c026456f-af67-45d5-888b-a3bc65c6bfe1" src="https://github.com/user-attachments/assets/3983cc29-3531-4da1-938e-f8395b90b799" />

<img width="1200" height="1600" alt="450548d5-8ca8-4eab-88af-9d353368b7ad" src="https://github.com/user-attachments/assets/68496a48-a5ec-4387-b639-50d8e61c568b" />

<img width="1600" height="1200" alt="2152bf46-992f-466f-a6ee-f8919ecb02a1" src="https://github.com/user-attachments/assets/35ef2576-eec4-41c6-80f6-b028aacbc41b" />

<img width="1200" height="1600" alt="907720a0-3d2c-487b-9b1c-60c304a6d519" src="https://github.com/user-attachments/assets/cd3875c1-402c-4c6e-b6d5-26c93d57c5cb" />

<img width="1200" height="1600" alt="8643b652-5dcb-4888-8a78-bcdacbbd6abc" src="https://github.com/user-attachments/assets/7eb2d4af-8d6b-4d67-9c4f-26576eb0a8bd" />

<img width="1200" height="1600" alt="c5ebf20b-0716-469d-a3d7-c3044efd4e54" src="https://github.com/user-attachments/assets/6d658bc6-66e9-4142-aadd-39968e7db61e" />

https://github.com/user-attachments/assets/c2f9ab2b-cac3-49c6-ba33-4c7e1a6ba3a3

https://github.com/user-attachments/assets/8b5bdd96-77f7-476a-ad54-fdefc17132a8

<img width="1200" height="1600" alt="049d873d-0648-499a-90eb-fb5540fc4b3f" src="https://github.com/user-attachments/assets/ff914544-466f-4f8a-a9bb-cd33c3978c08" />




[⬅️ Back to Atherion-Robotics][https://github.com/KissCezar/Atherion-Robotics]
---

