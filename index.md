# Futuristic Model of Automatic Electric Cooking Machine

### M.Tech Thesis Project | Electrical Engineering
**Arjun Vallyath Anil** *Rajiv Gandhi Institute of Technology*

---

## Project Overview
The objective was to design and build a fully automated electric cooking machine capable of preparing food autonomously upon receiving orders via a mobile application. While this system includes robotics and mobile integration, my primary research and development focused on **Power Electronics, Motor Control Systems, and Hardware Design**. The current prototype serves as a high-fidelity testbed for electrical control. Advanced AI, Human-Robot Interaction (HRI), and fully autonomous arm path-planning are considered **Future Scope** for subsequent iterations of this research.

![Cooking Robot Demo](media/cooking_machine_intro.gif)

### The Vision
As the demand for convenience grows, this technology serves as a solution for:
* **Busy Professionals:** Streamlining meal prep for individuals with tight schedules.
* **Future Automated Restaurants:** Reducing labor costs and ensuring consistent food quality through robotics.

---

## 📄 Published Research
The core innovations of this project were presented and published at the **IEEE International Conference on Emerging Trends in Engineering Science and Technology (2020)**.

> **Citation:** > Arjun V.A, Meera Khalid, “Futuristic Model of Automatic Electric Cooking Machine”, *IEEE International Conference on Emerging Trends in Engineering Science and Technology*, December 2020.  
> **DOI:** [10.1109/PICC51425.2020.9362400](https://doi.org/10.1109/PICC51425.2020.9362400)  
> **Full Paper:** [Read on IEEE Xplore](https://ieeexplore.ieee.org/document/9362400)

---
## 🛠 Technical Architecture
The system is divided into five distinct electrical sections, coordinated by a central mother controller.
* Power supply
* BLDC Motor & PID Control
* Quasi-Resonant Converter for Induction Heating
* Robotic Arm
* IOT with ESP8266
![System Architecture](media/block_diagram.png)

## 1. Power Supply Module
This section comprises a step-down transformer, a bridge rectifier, and multiple voltage regulators. The primary role of this module is to provide regulated electrical power to the various subsystems of the prototype. The specific voltage requirements for each component are as follows:
* **Resonant Converter:** 310V (Rectified Mains)
* **BLDC Motor:** 24V
* **IR2110 Gate Driver:** 12V & 5V
* **Servo Motors:** 6V
* **ESP8266 NodeMCU:** 5V
* **dsPIC33FJ32MC202:** 3.3V


### Circuit Operation & Regulation
The design utilizes a multi-stage regulation strategy to maintain strict voltage tolerances across all hardware. The regulation suite includes the **LM350**, **LM338**, **L7812**, **L7805**, and **LD33**.

![Power Supply Block Diagram](media/powersupply_circuit.png)

### The Conversion Process:
1.  **Step-Down & Rectification:** The 230V AC mains is stepped down to 24V AC via a transformer and rectified to DC. This 24V DC rail serves as the primary input for the high-current regulators.
2.  **High-Power Rails:** * The **LM338** provides a 12V, 5A output specifically for the BLDC motor drive and the L7812 stage.
    * The **LM350** provides a 6V, 3A output dedicated to the six servo motors within the robotic arm.
3.  **Logic & Control Rails:** * The 12V DC output from the **L7812** powers the **IR2110** gate driver.
    * This 12V rail is further regulated by the **L7805** (5V for the ESP8266) and the **LD33** (3.3V for the dsPIC33FJ32MC202 microcontroller).

![Power Supply Hardware](media/sec1_hardware.png)
      
## 2. BLDC Motor Control System

This section details the implementation of the Brushless DC (BLDC) motor control, focusing on the commutation logic, driver circuitry, and the integration of the dsPIC33FJ32MC202 microcontroller.

### Motor Theory and Operation
The BLDC motor used in this prototype is a synchronous motor featuring surface-mounted permanent magnets on the rotor and poly-phase armature windings on the stator. 

* **Working Principle:** Mechanical torque is developed through the interaction between the magnetic field of the rotor magnets and the electromagnetic field produced by the stator coils.
* **Feedback Mechanism:** To achieve precise commutation, multiple Hall-effect sensors are utilized to detect the real-time position of the rotor.

![BLDC Block Diagram](media/sec2_block.png)

### Inverter & Driver Circuitry
A **3-phase bridge inverter** consisting of six MOSFET switches is employed to drive the motor. The switching sequence is determined by the Hall sensor inputs to ensure the stator windings are energized in the correct order.

#### High-Speed Gate Driving (IR2110)
Because the **dsPIC33** microcontroller operates at a 3.3V logic level, it cannot directly trigger the MOSFETs, which require a 12V gate-to-source voltage ($V_{GS}$) for efficient switching.

* **Driver Role:** The **IR2110** high-voltage, high-speed driver acts as the bridge. It independently handles high and low-side referenced output channels.
* **Level Shifting:** It boosts the 3.3V PWM signals from the controller to the 12V range required by the MOSFET gates.
* **Specifications:** The IR2110 operates within a 10V to 20V supply range and provides a peak output current of 2.5A, ensuring rapid charging of the MOSFET gate capacitance.

![MOSFET Driver Circuit](media/control_bldc.png)

### 🖥️ Microcontroller Implementation
The **dsPIC33FJ32MC202** Digital Signal Controller (DSC) serves as the "brain" for the motor control:
1.  **Input:** Receives feedback from Hall-effect sensors and the encoder.
2.  **Processing:** Processes the feedback signals against a predefined switching table.
3.  **Output:** Generates high-frequency PWM signals. By adjusting the duty cycle of these signals, the effective voltage (and thus the current) is regulated, allowing for precise control of motor speed and torque.

---

### 🔬 Experimental Validation
To verify the design, the MOSFET driver circuit was tested independently before integration with the motor. The following images demonstrate the experimental setup and the final successful control of the BLDC motor.

![MOSFET Driver Testing](media/ir2110_hardware.jpg)
![BLDC Control Experiment](media/sec2_hardware.png)



### 3. Quasi-Resonant Induction Heating
I focused on the design of a **Quasi-Resonant Inverter** to achieve Zero Voltage Switching (ZVS), which minimizes power loss during high-frequency induction.

* **Simulation:** Validated using **MATLAB/Simulink**.
* **Waveform Analysis:** The design ensures the IGBT (T1) switches at the optimal point of the resonant cycle to maximize efficiency.

![Quasi-Resonant Waveforms](image_6bb3c3.png)
![Matlab Simulation](image_6bb367.png)

---

---

## 🚀 Future Scope
* **Autonomous Robotics:** Implementing Inverse Kinematics (IK) for the 5-DOF arm.
* **AI & HRI:** Integrating voice recognition and adaptive recipe learning.
* **Thermal Vision:** Using IR sensors for smarter ingredient detection.
