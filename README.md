# IMU Serial Orientation Streaming (Arduino)

This repository implements low-level IMU communication and real-time orientation computation on an Arduino using the I²C interface. Raw accelerometer and gyroscope data are read directly from IMU registers, conditioned using low-pass filtering, fused using a complementary filter, and streamed over a serial interface for real-time visualization and external processing.

The code is designed to provide stable, rate-controlled orientation data suitable for real-time visualization pipelines.

---

## Project Overview

The goal of this project is to build a reliable embedded pipeline for acquiring IMU data, conditioning noisy sensor signals, computing orientation (roll, pitch, yaw), and transmitting that data over serial in a form suitable for real-time visualization.

Rather than relying on high-level abstraction libraries, the implementation interfaces directly with IMU registers via I²C, providing explicit control over sensor configuration, filtering, timing, and data handling.

This project emphasizes **embedded IMU communication, signal conditioning, and orientation computation**, not full state estimation or SLAM.

---

## System Architecture

IMU (ICM / MPU series, I²C)

↓

Raw Accelerometer & Gyroscope Data

↓

Low-Pass Filtering on gyro for yaw(noise attenuation)

↓

Calibration & Scaling

↓

Complementary Filter (sensor fusion)

↓

Orientation Computation (roll, pitch, yaw)

↓

Rate-Limited Serial Telemetry (USB, 115200 baud)

---

## Key Features

- Register-level I²C communication with the IMU
- Explicit accelerometer and gyroscope configuration
- Raw sensor calibration and bias correction
- Low-pass filtering of gyro signals to reduce high-frequency noise
- Complementary filter combining accelerometer and gyroscope data
- Stable roll and pitch estimation
- Gyroscope-based yaw integration
- Non-blocking timing using `micros()` and `millis()`
- Fixed-rate serial output (50 Hz) for real-time consumers

---

### Complementary Filter

A complementary filter is used to fuse accelerometer and gyroscope measurements:

- Accelerometer data provides long-term stability
- Gyroscope data provides short-term responsiveness
- A fixed blending factor balances both sources

This approach yields smooth, drift-resistant roll and pitch estimates while maintaining responsiveness to motion.

Yaw is obtained via gyroscope integration and is not corrected by an absolute reference. (plus a low pass filter)

---

## Orientation Computation

- **Roll & Pitch**
  - Derived from filtered accelerometer data
  - Stabilized using a complementary filter with gyroscope rates

- **Yaw**
  - Integrated from the gyroscope Z-axis rate
  - No magnetometer-based correction is applied

Orientation values are maintained in degrees and updated using time deltas computed from `micros()`.

---

## Timing and Rate Control

- Gyroscope integration uses precise time steps computed from microsecond timestamps
- Serial transmission is rate-limited to **50 Hz** using a non-blocking timing check
- This prevents serial flooding and ensures compatibility with real-time visualization systems

---

## Serial Data Format

Orientation data is transmitted as comma-separated values: roll,pitch,yaw

---

## Design Considerations

- Low-pass filtering reduces measurement noise
- Complementary filtering balances responsiveness and stability
- Non-blocking timing preserves sensor integration accuracy
- Fixed-rate telemetry prevents downstream buffering issues
- Register-level access provides transparency and control

---

## Limitations

- Yaw drift is expected due to lack of a magnetometer
- Complementary filter parameters are fixed
- No absolute heading correction is implemented

---

#FUTURE

Future work involves coupling the Arduino-based IMU orientation output with a VPython-powered real-time 3D visualization for live orientation rendering.



