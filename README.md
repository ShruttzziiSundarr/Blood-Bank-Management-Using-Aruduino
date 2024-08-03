# Health Monitoring System Using Arduino Uno

## Overview

The Health Monitoring System using Arduino Uno is designed to monitor vital signs such as SpO2, pulse rate, and temperature. The system collects physiological data through various sensors, processes it using Arduino Uno, and transmits it to a central server or mobile application for real-time feedback and continuous monitoring.

## Methodology

The development of the health monitoring system is divided into the following phases:

### 1. Design and Development

- **Hardware and Software Design:** 
  - Select and interface sensors for monitoring vital signs (SpO2, pulse rate, temperature).
  - Design the circuit layout for Arduino Uno.
  - Program the Arduino microcontroller to collect and transmit data.

- **Development Tools:**
  - Use the Arduino Integrated Development Environment (IDE) for programming.
  - Utilize various sensors and modules for data collection.

### 2. Testing and Validation

- **System Testing:** 
  - Test the system for accuracy and reliability with a group of volunteers.
  - Compare collected data with traditional health monitoring systems to ensure accuracy.

- **Error Handling:**
  - Address any errors or inconsistencies.
  - Make necessary improvements to the system based on testing results.

### 3. Integration and Deployment

- **System Integration:**
  - Integrate the system into a central server or mobile application for data storage and analysis.
  
- **Deployment:**
  - Deploy the system in various settings such as hospitals, homes, and remote areas.
  - Develop a user-friendly interface to provide real-time feedback on health status.

### 4. Evaluation and Improvement

- **Performance Evaluation:**
  - Evaluate the system's performance in real-world scenarios.
  
- **Feedback Collection:**
  - Gather feedback from healthcare providers and individuals using the system.
  - Analyze feedback to identify areas for improvement.

- **System Improvement:**
  - Enhance the system based on feedback to ensure optimal performance and user satisfaction.

## System Design

### Block Diagram

The block diagram of the system outlines the flow of data and interactions between components:

- **Power Supply:** Powers the system.
- **Sensors:** Measure physiological data (SpO2, pulse rate, temperature) from the human body.
- **Arduino Uno:** Converts analog sensor data to digital data.
- **Central Server/Mobile Application:** Receives data from Arduino, processes it, and displays it on an LCD display and mobile application.

![Block Diagram](path_to_block_diagram_image) 

### Flowchart

The flowchart visualizes the steps followed throughout the system management process, from start to finish:

![Flowchart](path_to_flowchart_image)

### Circuit Diagram

The circuit diagram illustrates the connections and components used in the system:

![Circuit Diagram](path_to_circuit_diagram_image)

## Components

- **Arduino Uno:** Microcontroller board for data processing.
- **SpO2 Sensor:** Measures oxygen saturation levels.
- **Pulse Rate Sensor:** Measures heart rate.
- **Temperature Sensor:** Measures body temperature.
- **LCD Display:** Displays real-time health data.
- **Mobile Application:** Provides a user interface for monitoring health status.

## Installation and Usage

1. **Setup Arduino and Sensors:**
   - Connect sensors to the Arduino Uno according to the circuit diagram.
   - Power the system.

2. **Upload Code to Arduino:**
   - Use the Arduino IDE to upload the provided code to the Arduino Uno.

3. **Run and Monitor:**
   - Monitor the data on the LCD display.
   - Check the mobile application for real-time feedback.

## Code

Here's an example code snippet for reading sensor data and displaying it on the LCD:

```cpp
#define USE_ARDUINO_INTERRUPTS true // Set-up low-level 
interrupts for most acurate BPM math
#include <PulseSensorPlayground.h> 
#include <Wire.h>
#include <LiquidCrystal_I2C.h> // Includes the 
PulseSensorPlayground Library
#include <Wire.h>
#include "MAX30105.h"
const int PulseWire = 0; // 'S' Signal pin connected to A0
const int LED13 = 13; // The on-board Arduino LED
int Threshold = 550; // Determine which Signal to "count as a 
beat" and which to ignore
const int analogPin = A1; // Analog pin for the sensor output
const float referenceVoltage = 5.0;
 
PulseSensorPlayground pulseSensor; // Creates an object
MAX30105 particleSensor;
LiquidCrystal_I2C lcd(0x27, 16, 2);
void setup() {
Serial.begin(9600);
 Serial.println("Initializing...");
 lcd.begin(16, 2);
 lcd.clear();
// Configure the PulseSensor object, by assigning our variables to it
pulseSensor.analogInput(PulseWire); 
pulseSensor.blinkOnPulse(LED13); // Blink on-board LED 
with heartbeat
pulseSensor.setThreshold(Threshold); 
 
// Double-check the "pulseSensor" object was created and began 
seeing a signal
 if (particleSensor.begin(Wire, I2C_SPEED_FAST) == false) { //Use 
default I2C port, 400kHz speed
 Serial.println("MAX30102 was not found. Please check 
wiring/power. ");
 while (1);
 }
 particleSensor.setup(0); //Configure sensor. Turn off LEDs
 particleSensor.enableDIETEMPRDY(); //Enable the temp ready 
interrupt. This is required.
if (pulseSensor.begin()) {
Serial.println("PulseSensor object created!");
 lcd.print("Pulse Sensor");
 lcd.setCursor(0, 1);
 lcd.print("Initialized");
 delay(2000);
 lcd.clear();
}
 else
 {
 Serial.println("Pulse Sensor Failed to Initialize!");
 lcd.print("Pulse Sensor");
 lcd.setCursor(0, 1);
 lcd.print("Failed");
 while (true);
 }
}
void loop() {
int myBPM = pulseSensor.getBeatsPerMinute(); 
 // Read the analog sensor value
 int sensorValue = analogRead(analogPin);
 float voltage = sensorValue * (referenceVoltage / 1023.0); 
Convert the sensor value to voltage 
// Calculate alcohol concentration in ppm (parts per million)
 float alcoholConcentration = map(voltage, 0.1, 2.0, 0, 500); 
 float temperature = particleSensor.readTemperature();
 float temperatureF = particleSensor.readTemperatureF();
 Serial.print("temperatureC=");
 Serial.print(temperature, 4);
 Serial.print(" temperatureF=");
 Serial.print(temperatureF, 4);
 Serial.println();
 Serial.print("Alcohol Concentration in air: ");
 Serial.print(alcoholConcentration);
 Serial.println(" ppm");
 lcd.setCursor(0, 2);
 lcd.print("air quality:");// Print the BPM value
 lcd.setCursor(12, 0);
 lcd.print(alcoholConcentration);
if (pulseSensor.sawStartOfBeat()) { // Constantly test to 
see if a beat happened
Serial.println("♥ A HeartBeat Happened ! "); // If true, print 
a message
Serial.print("BPM: ");
Serial.println(myBPM); 
 lcd.setCursor(0, 0);
 lcd.print("BPM:");// Print the BPM value
 lcd.setCursor(12, 0);
 lcd.print(myBPM); 
}
delay(1000);
}

