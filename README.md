[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/AlBFWSQg)

# a14g-final-submission

    * Team Number:
    * Team Name:
    * Team Members:
    * Github Repository URL:
    * Description of test hardware: (development boards, sensors, actuators, laptop + OS, etc)

## 1. Video Presentation

## 2. Project Summary

#### **A. Device Description**

- The Smart Dog Feeder is an IoT-enabled automated feeding device that monitors food and water levels, detects the presence of a pet, and allows remote interaction through a web-based dashboard. It solves the problem of inconsistent feeding for pet owners who are away from home.
- Inspired by the need for convenient pet care in busy households, this project ensures pets are always fed, hydrated, and monitored by providing real-time updates and remote control capabilities.
- The feeder utilizes MQTT over Wi-Fi to send multiple sensors data to the cloud and receive commands from a Node-RED dashboard hosted on Azure, enabling monitoring and control from anywhere.

#### **B. Device Functionality**

**Core Controller**

* **ATSAMW25 Microcontroller (SAMD21 + WINC1500)** : Serves as the central processing unit. Runs FreeRTOS to manage multiple concurrent tasks such as sensor monitoring, MQTT communication, motor control, and user interaction.

**Communication Module**

* **Wi-Fi Connectivity (WINC1500)** : Provides real-time communication to a cloud-based MQTT broker hosted on Azure, where a Node-RED dashboard enables remote control and monitoring.
* **MQTT Protocol** : Used for both data publishing (e.g., food level, alerts) and command reception (e.g., dispense food, play audio).

**Sensors**

1. **VEML7700 Ambient Light Sensor** : Placed on a separate PCB at multiple hopper levels to detect granular food fullness (e.g., 20%, 40%, up to 100%), all connected to an I2C Multiplexer.
2. **I2C Multiplexer (TCA9548A)** : Enables connection of 8 I2C VEML7700 sensors without address conflict, supporting scalable sensor arrays.
3. **Water Detection Sensor** : Detects the presence of water in the drinking bowl and halts the pump when water is sufficient.
4. **Analog Water Level Sensor** : Measures the tank’s water volume via ADC.
5. **LSM6DSOX IMU** **Accelerometer**: Monitors for device tilting or tampering to check for abnormal orientation.
6. **SHT40 Temperature & Humidity Sensor** : Monitors ambient environmental conditions to help ensure comfort and prevent hardware failure (in progress).
7. **Tamper Switch** : Detects unauthorized access to the hopper lid or refill compartment (in progress).
8. **Proximity/Distance Sensor** : Detects the dog’s presence near the feeder, enabling snapshot capture or conditional feeding logic (in progress/working on RP2040 board).
9. **Dog Request Button** : A large, easy-press button that trained pets can use to request feeding. Prevents spam with internal debounce and cooldown logic (in progress).
10. **Magnetic Switch** :Detect the magnet attached to the Motor's dispensing wheel to count/stop exact cycle rotation for consistent food portion distribution.

**Actuators**

* **DC Motor with Limit Switch** : Dispenses food by rotating the mechanism exactly once per cycle. The magnetic switch ensures portion accuracy.
* **Peristaltic Water Pump** : Fills the bowl only when needed, as indicated by the water detection sensor. Automatically stops on detection.
* **LED Driver** : Drives high power LED to add lighting for the VEML Mux and Camera at night time (in progress).
* **Audio Module (Speaker + MCU + Amplifier)** : Plays customizable audio prompts when feeding begins or when commanded remotely (working on the RP2040).
* **RP2040 + Camera Module** : Captures and uploads a snapshot of the pet when triggered by user command or dog presence detection (working on the RP2040 - connected via UART to main PCB).

**Supporting Components**

* **RTC Module** : Keeps accurate time and triggers scheduled feeding and watering routines (in progress).
* **GPIO expander**: Used to reduce the pins needed for tamper switch, temperature sensor, and distance sensor by triggering one interrupt if any interrupt found from those three (in progress).

**Block Diagram:** 

![1746603966593](image/README/1746603966593.png)

#### C. Challenges

We aimed for a more ambitious projects with plenty sensors and actuators. Thus, in the process, we face a lot of challenges in firmware, hardware, software, integration such as: 

**SD Card Issue on PCB:** We encountered a significant problem with the SD card on the PCB, which could impact bootloading. Initially, we suspected it was a firmware configuration issue, such as incorrect pin assignments. However, after testing with the SAMW25 development board and checking signals with an oscilloscope, we discovered that the CLK pin was incorrectly grounded. We attempted to reflow the connections and inspected nearby traces, which allowed some signal to pass, although it was still noisy—likely due to poor soldering beneath the diode. Interestingly, the SD card on our second board works perfectly, reinforcing our suspicion that there was a soldering error on the first board.

**I2C Configuration Delays:** We utilized a total of five I2C components, with two key components functioning correctly. The distance sensor is incomplete, and the temperature sensor and GPIO expander require proper configuration. While we managed to establish successful connections for all components, time constraints prevented us from testing all of them. Therefore, we decided to put the three that do not affect main functionality on hold; they are primarily useful for additional tracking.

**Stack Overflow During Integration:** We faced multiple stack overflow issues during operation due to the large number of components in use. To address this, we increased the maximum heap size to around 1400 and attempted to free up stack space using functions like `vTaskDelete`. Unfortunately, these functions frequently caused crashes in our main flow. As a solution, we opted to combine and optimize our drivers into six major tasks (motor, pump, Wi-Fi, MQTT send, ADC/IMU, CLI) and managed them using semaphores triggered by manual and MQTT commands. All data from the motor, pump, and ADC/IMU will be sent via MQTT by adding it to a queue directed to a small MQTT send task. This approach simplifies management and significantly reduces the stack allocation needed for other tasks.

**LED Driver Issue:** Unfortunately, our PCB for the LED driver was shorted before the demo, forcing us to skip this component.

**Mechanical Prototyping Challenges:** We encountered issues with laser prototyping, which caused the plastic used to construct the dispenser wheel to burn. Additionally, we ran out of time to complete the food storage case. We may need to refine the design further to ensure it fits compactly.

#### D. **Prototype Learnings**


#### E. Next Steps and Takeaways


#### F. **Project Links**

## 3. Hardware & Software Requirements


## 4. Project Photos & Screenshots

## Codebase

- A link to your final embedded C firmware codebases
- A link to your Node-RED dashboard code
- Links to any other software required for the functionality of your device
