# wearable-postural-control-system


## 📌 Overview

The **Wearable Postural Control System** is designed to monitor spinal posture during daily activities and therapy exercises and provide real-time feedback when incorrect posture is maintained beyond a permitted limit.

The system uses **three Inertial Measurement Units (IMUs)** positioned on the upper trunk, middle trunk, and pelvis. Each IMU provides accelerometer, gyroscope, and magnetometer measurements.

The collected sensor data is calibrated and processed to estimate the relative orientation of different body segments. These measurements are then used to estimate spinal **bend, tilt, and twist**.

A personalized baseline posture is established for the user. The system evaluates deviations from this baseline and provides vibration feedback when the deviation exceeds the defined angular and time limits.

---

## 🎯 Objectives

* Monitor spinal posture during daily activities and therapy exercises
* Estimate spinal bend, tilt, and twist in real time
* Establish a subject-specific baseline posture
* Detect sustained deviations from the permitted posture range
* Provide real-time vibration feedback
* Reduce false alerts caused by short-duration movements
* Record posture information for long-term monitoring
* Provide data visualization through a companion mobile application

---

## 🏗️ System Architecture

                 ┌───────────────────┐
                 │    Upper Trunk    │
                 │       IMU 1       │
                 └─────────┬─────────┘
                           │
                           │
                 ┌─────────▼─────────┐
                 │    Middle Trunk   │
                 │       IMU 2       │
                 └─────────┬─────────┘
                           │
                           │
                 ┌─────────▼─────────┐
                 │      Pelvis       │
                 │       IMU 3       │
                 └─────────┬─────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │       ESP32       │
                 │                   │
                 │ Data Acquisition  │
                 │ Calibration       │
                 │ Orientation       │
                 │ Estimation        │
                 └─────────┬─────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │ Posture Analysis  │
                 │                   │
                 │ Bend              │
                 │ Tilt              │
                 │ Twist             │
                 └─────────┬─────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │ Decision Engine   │
                 │                   │
                 │ Baseline          │
                 │ Angular Threshold │
                 │ Time Threshold    │
                 └─────────┬─────────┘
                           │
                    ┌──────┴──────┐
                    │             │
                    ▼             ▼
             ┌────────────┐ ┌──────────────┐
             │ Vibration  │ │ Mobile App / │
             │  Feedback  │ │ Data Logging │
             └────────────┘ └──────────────┘
```

---

## 🔩 Hardware

### Main Components

| Component       | Purpose                                                       |
| --------------- | ------------------------------------------------------------- |
| ESP32           | Main controller, sensor processing and wireless communication |
| IMU × 3         | Measures acceleration, angular velocity and magnetic field    |
| Vibration Motor | Provides posture correction feedback                          |
| Battery         | Portable power source                                         |
| Wearable Mount  | Positions the sensors on the body                             |

### Sensor Placement

| Sensor | Position     | Purpose                                   |
| ------ | ------------ | ----------------------------------------- |
| IMU 1  | Upper trunk  | Measures upper body orientation           |
| IMU 2  | Middle trunk | Measures middle trunk orientation         |
| IMU 3  | Pelvis       | Provides lower-body reference orientation |

---

## 📡 Sensor Data Acquisition

Each IMU provides three types of measurements:

### Accelerometer

Measures linear acceleration along three axes.

```text
Ax
Ay
Az
```

The accelerometer provides information related to the direction of gravity and can therefore contribute to estimating body inclination.

### Gyroscope

Measures angular velocity about the three axes.

```
Gx
Gy
Gz
```

Gyroscope measurements provide information about rotational movement.

### Magnetometer

Measures the surrounding magnetic field.

```
Mx
My
Mz
```

Magnetometer measurements can provide a reference for heading/orientation estimation.

---

## ⚙️ Sensor Calibration

Raw sensor measurements contain bias and other errors.

The calibration stage is used to reduce these errors before orientation estimation.

```
Raw IMU Data
      ↓
Bias Calibration
      ↓
Corrected Sensor Data
      ↓
Orientation Estimation
```

Calibration is performed before the sensor measurements are used by the posture estimation stage.

---

## 🧭 Orientation Estimation

The system uses a **Direction Cosine Matrix (DCM)** based sensor-fusion approach for orientation estimation.

The DCM represents the orientation of a body coordinate frame relative to a reference frame.

The sensor measurements are combined to estimate the orientation of each IMU.

```
Accelerometer
       │
       ├──────────┐
       │          │
Gyroscope ───────►│ DCM Sensor Fusion
       │          │
Magnetometer ─────┘
                  │
                  ▼
             Orientation
```

The resulting orientation information is used to determine the relative orientation between the body segments.

---

## 📐 Posture Estimation

The three IMUs represent different sections of the body.

The relative orientation between these sections is used to estimate spinal movement.

The system monitors:

### Bend

Forward/backward movement of the trunk.

### Tilt

Lateral movement of the trunk.

### Twist

Rotational movement around the vertical axis.

The estimated posture is compared with the user's personalized baseline posture.

---

## 👤 Personalized Baseline

Different users naturally have different body structures and preferred posture positions.

Therefore, the system uses a **subject-specific baseline** rather than relying exclusively on one fixed posture value.

```
User Calibration
       ↓
Reference Posture
       ↓
Permitted Range
       ↓
Real-Time Posture
       ↓
Deviation Calculation
```

This allows the system to adapt the permitted posture range to the individual user.

---

## 🧠 Decision Engine

The decision stage evaluates the deviation between the current posture and the personalized baseline.

Two important conditions are considered:

1. **Angular deviation**
2. **Duration of deviation**

The vibration alert is generated only when the posture deviation exceeds the permitted angular threshold for longer than the defined time threshold.

```
                 Current Posture
                       │
                       ▼
              Compare with Baseline
                       │
                       ▼
               Angular Deviation
                       │
                       ▼
              Exceeds Threshold?
                  /          \
                No            Yes
                │              │
                ▼              ▼
              Normal       Start Timer
                               │
                               ▼
                       Time Threshold?
                          /        \
                        No          Yes
                        │            │
                        ▼            ▼
                     Ignore       Alert
                                      │
                                      ▼
                              Vibration Motor
```

This approach helps reduce false alerts caused by short movements or temporary posture changes.

---

## 📳 Vibration Feedback

A vibration motor connected to the ESP32 provides tactile feedback to the user.

When the decision engine determines that incorrect posture has been maintained beyond the permitted limit, the vibration motor is activated.

The feedback mechanism allows the user to recognize the posture deviation without requiring visual interaction with the mobile application.

---

## 📱 Mobile Application

A companion mobile application is being developed for posture monitoring and data visualization.

Planned functionality includes:

* Real-time posture information
* Posture history
* Data logging
* Progress tracking
* Visualization of posture deviations
* 3D representation of spinal posture
* Long-term monitoring

The ESP32 can use wireless connectivity to transmit posture and alert information to the application or cloud-based system.

---

## 🔄 Complete System Workflow

```
        ┌─────────────────────┐
        │   Three IMU Sensors │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │   Data Acquisition  │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ Sensor Calibration  │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ DCM Sensor Fusion   │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ Orientation         │
        │ Estimation          │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ Bend / Tilt / Twist │
        │ Estimation          │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ Compare with        │
        │ Baseline Posture    │
        └──────────┬──────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │ Angular + Time      │
        │ Threshold Evaluation│
        └───────┬───────┬─────┘
                │       │
             Normal   Deviation
                │       │
                │       ▼
                │  ┌───────────────┐
                │  │   Vibration   │
                │  │    Alert      │
                │  └───────────────┘
                │
                ▼
        ┌─────────────────────┐
        │ Mobile App / Data   │
        │ Logging & Analysis  │
        └─────────────────────┘
```

---

## 📊 Performance Evaluation

The prototype can be evaluated using quantitative measurements such as:

* Angular estimation error
* RMS error
* Low-angular-error performance
* Posture deviation detection accuracy
* Response time
* Sensor consistency
* Feedback response

Experimental measurements and plots can be added to the [`results`](./results) directory.

---

## 📁 Repository Structure

```
wearable-postural-control-system/
│
├── README.md
│
├── docs/
│   ├── system-architecture.png
│   ├── circuit-diagram.png
│   ├── mechanical-design.png
│   └── project-report.pdf
│
├── firmware/
│   └── src/
│       ├── main.cpp
│       ├── imu.cpp
│       ├── imu.h
│       ├── calibration.cpp
│       ├── calibration.h
│       ├── orientation.cpp
│       ├── orientation.h
│       ├── posture.cpp
│       ├── posture.h
│       ├── feedback.cpp
│       └── feedback.h
│
├── algorithms/
│   ├── dcm/
│   ├── posture-estimation/
│   └── decision-engine/
│
├── hardware/
│   ├── bill-of-materials.md
│   └── wiring.md
│
├── mobile-app/
│   └── README.md
│
├── data/
│   └── sample-data.csv
│
└── results/
    ├── angular-error.png
    ├── rms-error.png
    └── experimental-results.md
```

---

## 🚀 Future Improvements

* Improve orientation-estimation accuracy
* Implement advanced sensor-fusion techniques
* Develop machine-learning-based posture classification
* Improve personalized posture thresholds
* Develop a complete mobile application
* Add cloud-based long-term monitoring
* Improve 3D spine visualization
* Reduce device size and weight
* Improve battery efficiency
* Evaluate the system with larger user groups

---

## 🛠️ Technologies

### Hardware

**ESP32 • IMU • Vibration Motor • Battery**

### Programming

**C/C++ • Python**

### Algorithms

**Sensor Calibration • DCM Sensor Fusion • Orientation Estimation • Posture Estimation • Decision Logic**

### Connectivity

**Wi-Fi • Mobile Application**

---

## 📌 Project Status

🟡 **Prototype / Development**

The system is being developed as a wearable prototype for real-time posture monitoring, deviation detection, and user feedback.

---

## 👨‍💻 Author

### Farooq Jaleel

Electronics & Communication Engineering Student


---

## ⚠️ Disclaimer

This project is an engineering prototype intended for posture monitoring and feedback. It is not intended to diagnose, treat, or prevent any medical condition.
