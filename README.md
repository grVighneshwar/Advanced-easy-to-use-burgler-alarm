# Advanced-easy-to-use-burgler-alarm
## **Overview**
This project introduces a Smart Burglar Alarm designed to secure homes, offices, and other spaces through precise object detection using ultrasonic sensing technology. The system is user-friendly, featuring an automatic threshold-setting mechanism to detect intrusions accurately. It adjusts itself to monitor objects based on environmental conditions, making it adaptable and reducing false alarms.

At the heart of the system is the VSD Squadron Mini, powered by a RISC-V CH32V003 microcontroller, which processes sensor data and triggers the alarm. Additionally, the PCB was custom-designed and etched manually, ensuring a compact and reliable implementation.

### **Objectives**
Build a smart burglar alarm that automatically sets detection thresholds.
Utilize the VSD Squadron Mini for efficient signal processing and system control.
Design and fabricate a custom PCB to house all components, ensuring an optimized layout.

## **System Architecture**
- **Detection Mechanism**:
- **Ultrasonic Sensor**: Responsible for detecting objects within a defined range, emitting sound waves and calculating distances based on the echo.
Automatic Threshold Setting: The system sets its own threshold based on real-time environmental feedback, ensuring optimal sensitivity without manual calibration.
- **Core Controller**:
- **VSD Squadron Mini (RISC-V CH32V003)**: This serves as the primary processing unit, handling the data from the ultrasonic sensor and controlling the alarm system. The use of the RISC-V architecture allows for low-power, high-efficiency processing.
- **Custom PCB Design**:
The custom-designed and manually etched PCB houses the ultrasonic sensor, VSD Squadron Mini, and supporting components. This PCB reduces the need for complex wiring and enhances reliability.
- **User Interaction**:
The system is designed to be plug-and-play, with minimal setup required. Once installed, it automatically adjusts to its surroundings and triggers alarms when an object crosses the set threshold.

## **Technical Specifications**
- **Sensor**: Ultrasonic sensor for object detection.
- **Microcontroller**: VSD Squadron Mini (RISC-V CH32V003) for signal processing and alarm control.
- **Custom PCB**: Designed and manually etched for compact integration of components.
- **Power Supply**: Supports standard 5V or 12V power inputs.
- **Alarm System**: Activates when an object is detected within the preset distance threshold.

## **Features**
- **Automatic Threshold Setting**: The system dynamically adjusts its detection range based on environmental factors, ensuring high accuracy.
- **VSD Squadron Mini**: The RISC-V based microcontroller powers the system with efficient processing, ensuring responsive and reliable alarm triggering.
- **Custom PCB**: A manually etched PCB reduces wiring complexity and ensures a neat, reliable assembly.
- **Object Detection**: The ultrasonic sensor guarantees precise detection of even minor intrusions.

## **Applications**
- Home and office security
- Securing warehouses and storage spaces
- Industrial surveillance
- Portable security for temporary setups

## **Future Enhancements**
- Adding wireless communication for remote monitoring and alerts.
- Integrating multiple sensors (e.g., infrared or motion detectors) for enhanced detection.
- Developing a mobile app to allow remote control and notifications.
