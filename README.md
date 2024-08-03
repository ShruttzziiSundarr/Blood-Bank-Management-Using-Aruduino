# Blood Bank Management System Using Arduino

## Overview

The Blood Bank Management System using Arduino is designed to monitor and manage blood inventory in a blood bank. The system tracks various parameters such as blood type, quantity, and expiration date. It leverages sensors and an Arduino microcontroller to automate and streamline the management process, ensuring that blood supplies are accurately monitored and managed.

## Methodology

The development of the Blood Bank Management System is divided into the following phases:

### 1. Design and Development

- **Hardware and Software Design:**
  - **Sensors:** Select and interface sensors for monitoring blood volume, temperature, and possibly other parameters.
  - **Circuit Design:** Design the circuit layout for Arduino Uno to interface with sensors and other components.
  - **Programming:** Program the Arduino microcontroller to collect, process, and transmit data regarding blood inventory.

- **Development Tools:**
  - Use the Arduino Integrated Development Environment (IDE) for coding and uploading programs to the Arduino.
  - Implement various sensors and modules as required.

### 2. Testing and Validation

- **System Testing:**
  - Test the system for accuracy and reliability by simulating blood inventory scenarios.
  - Validate the system’s performance by comparing the data with manual records or established management systems.

- **Error Handling:**
  - Address any issues or inaccuracies detected during testing.
  - Make necessary improvements to enhance system reliability and accuracy.

### 3. Integration and Deployment

- **System Integration:**
  - Integrate the Arduino-based system with a central server or a user interface for data storage and visualization.
  
- **Deployment:**
  - Deploy the system in blood banks or related facilities for real-time blood inventory management.
  - Develop a user-friendly interface for monitoring blood levels and other parameters.

### 4. Evaluation and Improvement

- **Performance Evaluation:**
  - Evaluate the system’s performance in actual operating conditions.
  
- **Feedback Collection:**
  - Collect feedback from blood bank staff and administrators to identify any issues or areas for improvement.

- **System Improvement:**
  - Enhance the system based on feedback to ensure optimal performance and user satisfaction.

## System Design

### Block Diagram

The block diagram of the system outlines the flow of data and interactions between components:

- **Power Supply:** Provides power to the system.
- **Sensors:** Monitor blood volume, temperature, etc.
- **Arduino Uno:** Processes sensor data and manages inventory.
- **Central Server/User Interface:** Displays data and manages inventory records.

![Block Diagram](path_to_block_diagram_image)

### Flowchart

The flowchart visualizes the steps involved in the blood bank management process:

![Flowchart](path_to_flowchart_image)

### Circuit Diagram

The circuit diagram illustrates the connections and components used in the system:

![Circuit Diagram](path_to_circuit_diagram_image)

## Components

- **Arduino Uno:** Microcontroller for processing and managing data.
- **Volume Sensor:** Measures the volume of blood.
- **Temperature Sensor:** Monitors the temperature of blood storage.
- **LCD Display:** Shows real-time data and status.
- **User Interface:** Provides a platform for monitoring and managing blood inventory.

## Installation and Usage

1. **Setup Arduino and Sensors:**
   - Connect sensors to the Arduino Uno according to the circuit diagram.
   - Power the system and ensure all connections are secure.

2. **Upload Code to Arduino:**
   - Use the Arduino IDE to upload the provided code to the Arduino Uno.

3. **Run and Monitor:**
   - Monitor blood inventory data on the LCD display.
   - Access the user interface for comprehensive management and reporting.

## Code

Here’s a sample code snippet for reading sensor data and displaying it:

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
