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

## 10. Testing and Results

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

## 11. Challenges and Solutions

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

## 12. Limitations

- No advanced security encryption for RFID  
- Limited range for Bluetooth communication  
- No obstacle detection system implemented  
- System depends on stable power supply  

---

## 13. Future Improvements

- Integration of ultrasonic sensors for obstacle avoidance  
- Improved RFID security with encrypted authentication  
- Mobile application with custom UI  
- Addition of autonomous driving mode  
- Optimization for low-power operation  

---

## 14. Conclusion

This project demonstrates a functional embedded system combining wireless control and RFID-based identification.

The integration of multiple input sources introduces basic system-level decision making, making the project closer to real-world robotics applications.

It provided hands-on experience in embedded architecture design, hardware integration, and system debugging.

---

## 15. Authors

Aetherion Robotics Team  
Student Engineering Project  

---


