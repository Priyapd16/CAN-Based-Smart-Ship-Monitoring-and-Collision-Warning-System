# CAN-Based Smart Ship Monitoring and Collision Warning System

## Overview
A distributed embedded system that monitors ship safety parameters — collision risk,
temperature, gas leaks, and fire — using multiple LPC21xx microcontroller nodes
communicating over a CAN (Controller Area Network) bus. Sensor data from independent
nodes is transmitted in real time and consolidated onto a central LCD and UART display,
giving a single at-a-glance view of ship status.

## System Architecture

**ECU1 – Collision Detection Node**
- HC-SR04 ultrasonic sensor, swept across 3 angular positions via servo motor
- Classifies obstacle distance into SAFE / WARNING / DANGER zones
- Local LED indicator + CAN broadcast of distance and status (IDs `0x102`, `0x103`)

**ECU2 – Environmental Safety Node**
- LM35 temperature sensor (ADC-based)
- Digital gas and flame sensors
- Classifies temperature into Normal / Warning / Alarm bands
- CAN broadcast of temperature, status, gas, and flame readings (IDs `0x201`–`0x204`)

**Central Receiver Node**
- Listens continuously on the CAN bus
- Decodes incoming frames by message ID
- Displays combined ship status on a 20x4 character LCD
- Logs all readings over UART for serial monitoring

## Communication Protocol
- CAN2 bus @ 125 kbps
- Message IDs uniquely identify sensor source and data type
- Each node transmits asynchronously; the receiver updates only the relevant
  display field per incoming frame, keeping the LCD always current

## Features
- Real-time collision distance monitoring with proximity alerts
- Fire and gas leak detection with instant warning
- Centralized status display (LCD + UART)
- Modular, CAN-based architecture — easy to add more sensor nodes

## Hardware Used
- LPC2129/LPC2148 microcontrollers (x3)
- HC-SR04 ultrasonic distance sensor
- LM35 temperature sensor
- Flame sensor
- Servo motor (scanning mechanism)
- 20x4 character LCD
- CAN transceivers (MCP2551)
## Flow Diagram
flowchart TD
    A["ECU1: Distance node<br/>HC-SR04 + servo scan"] -->|CAN1 bus<br/>0x102, 0x103| C["Receiver node<br/>Listens on CAN1 + CAN2"]
    B["ECU2: Environment node<br/>Temp, gas, flame"] -->|CAN2 bus<br/>0x201, 0x203, 0x204| C
    C --> D["LCD display<br/>20x4 status view"]
    C --> E["UART log<br/>Serial terminal output"]
## Tech Stack
- Embedded C
- LPC21xx CAN2 peripheral (polling-based RX/TX)
- UART (115200 baud) for serial logging
