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

---

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

### Microcontroller Implementation
The **dsPIC33FJ32MC202** Digital Signal Controller (DSC) serves as the "brain" for the motor control:
1.  **Input:** Receives feedback from Hall-effect sensors and the encoder.
2.  **Processing:** Processes the feedback signals against a predefined switching table.
3.  **Output:** Generates high-frequency PWM signals. By adjusting the duty cycle of these signals, the effective voltage (and thus the current) is regulated, allowing for precise control of motor speed and torque.


### 🔬 Experimental Validation
To verify the hardware design and validate the switching logic, a staged testing approach was implemented:

1. **Initial Logic Validation:** During the breadboard prototyping phase, an **Arduino UNO** was used to generate the commutation signals. This allowed for rapid verification of the switching cycle and Hall sensor feedback logic without the complexity of configuring the dsPIC's peripheral registers.
2. **Driver Testing:** The MOSFET driver circuit (IR2110) was tested independently to ensure the 3.3V-to-12V level shifting was stable and that the gate signals were clean.
3. **Final Implementation:** Once the switching logic and power stages were validated, the control was migrated to the **dsPIC33FJ32MC202** for the final PCB implementation to take advantage of its high-speed PWM and DSP capabilities.

![MOSFET Driver Testing](media/ir2110_hardware.jpg)


![BLDC Control Experiment](media/sec2_hardware.png)


---

## 3. Quasi-Resonant Induction Heating

Induction heating offers significant advantages over traditional resistive coils or gas stoves, including rapid heating, superior thermal efficiency, and precise temperature control. This section details the design of the high-frequency resonant inverter used to convert electrical energy into magnetic energy for heating.

### Inverter Topology Selection
For this project, a **Single-Ended (SE) Quasi-Resonant (QR) Inverter** was selected. While Half-Bridge (HB) inverters are common for high-power industrial use, the SE topology was chosen for its:
* **Cost-Efficiency:** Requires only one switching device (IGBT) and a single resonant capacitor ($C_r$).
* **Suitability:** Perfectly suited for domestic applications under 2kW, which meets our system's requirements.
* **Soft-Switching:** Enables Zero Voltage Switching (ZVS), reducing switching losses and improving overall energy conversion efficiency.

![Quasi-Resonant Converter](media/sec3_matlab.png)

### Circuit Theory and Mathematical Modeling
The Single-Ended Parallel Resonant Converter utilizes a tank network formed by the inductor ($L_r$) and capacitor ($C_r$). The peak voltage ratings for the switch and capacitor (typically 1,200V) are calculated based on loading conditions and maximum mains voltage.

#### Key Design Equations:
The peak current ($I_{PK}$) and resonant voltage ($V_{RES}$) are critical for component selection and system stability.

* **Energy Stored ($E$):** The energy stored in the inductive part of the load during the ON time ($T_{ON}$) is given by:
$$E = 0.5L \times I_{PK}^2$$

* **Peak Current ($I_{PK}$):** This is proportional to the ON time and the DC bus voltage:
$$I_{PK} = T_{ON} \times \left( \frac{V_{dc-bus}}{L} \right)$$

* **Resonant Voltage ($V_{RES}$):** Expressed in terms of $T_{ON}$ and $V_{dc-bus}$:
$$V_{RES} = \frac{T_{ON} \times V_{dc-bus}}{\sqrt{LC}}$$

---

### Control Strategy
The power source for this converter is rectified line voltage (unfiltered) to achieve a near-unity power factor. 
* **Frequency Range:** We employ a switching frequency control scheme operating between **20kHz and 60kHz**. 
* **Acoustic Noise Mitigation:** By staying above 20kHz, we avoid the human audible range.
* **Power Scaling:** The system utilizes a "Soft Start" beginning at 60kHz, gradually reaching maximum power at the lower frequency bound of 20kHz.

![Quasi-Resonant Waveforms](media/qr_waveform.png)

---
## 4. 5-DOF Robotic Arm & System Integration

For the ingredient handling system, I utilized a custom-designed **5-DOF (Degrees of Freedom) robotic arm**. This manipulator was responsible for the precise pick-and-place operation of food ingredients into the induction heating zone.
![Robotic_Arm](media/sec4_hardware.png)

### Scope and Evolution
It is important to note the developmental timeline of this system:
* **2019 - 2020 (M.Tech Thesis):** The primary focus was on the **Power Electronics and Motor Control** (as detailed in Sections 1-3). The robotic arm used in this prototype operated on a structured sequence rather than autonomous environmental sensing.
* **2022 - 2023 :** My subsequent research shifted toward **Intelligent Robotics**. I have since developed advanced autonomous arms capable of navigating cluttered environments using complex obstacle-avoidance algorithms. These newer systems can replace the basic manipulator in future iterations of the automated kitchen.

### Demonstration
Below is a demonstration of the robotic system in action, showcasing the integration between the mobile app commands, the power electronics, and the mechanical manipulator:

[![Pancake Making Robot](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://www.youtube.com/watch?v=YOUR_VIDEO_ID)
*Watch: 5-DOF Robotic Arm preparing a dish.*

> **Related Research:** > For my more recent work on autonomous navigation and intelligent manipulation in complex environments, please visit my [Intelligent Robotics Portfolio](LINK_TO_YOUR_OTHER_PROJECT).
---

## 🚀 Future Scope
* **Autonomous Robotics:** Implementing Inverse Kinematics (IK) for the 5-DOF arm.
* **AI & HRI:** Integrating voice recognition and adaptive recipe learning.
* **Thermal Vision:** Using IR sensors for smarter ingredient detection.
