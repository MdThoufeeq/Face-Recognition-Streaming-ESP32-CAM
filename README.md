# ESP32-CAM Face Recognition and Access Status System

An ESP32-CAM based face recognition and video streaming system with simple LED-based access status indication.

## Project Overview

This project uses an **ESP32-CAM AI Thinker** module for camera capture, face detection, face recognition, Wi-Fi connectivity, and browser-based monitoring.

A registered face match turns the **green LED ON** to indicate access granted. The **red LED** represents the default/reset access state.

A physical door lock is not used in this prototype. LEDs are used to demonstrate the access decision.

## Features

- Face detection using ESP32-CAM
- Face enrollment through the web interface
- Face recognition using stored face IDs
- Live camera streaming through a browser
- Browser-based camera controls
- Image capture
- Green LED for access granted
- Red LED for default/reset access state
- Automatic access-status reset after 6 seconds

## Hardware

| Component | Purpose |
|---|---|
| ESP32-CAM AI Thinker | Main controller, camera, Wi-Fi and face-processing platform |
| Camera sensor | Captures frames for detection and recognition |
| Green LED | Access granted indication |
| Red LED | Default/reset access-status indication |
| Breadboard and jumper wires | Prototype connections |
| USB / regulated 5 V supply | Power during development and testing |

## GPIO Connections

| GPIO | Function | Behavior |
|---|---|---|
| GPIO 13 | Red LED | HIGH by default/reset, LOW when access is granted |
| GPIO 14 | Green LED | LOW by default/reset, HIGH when access is granted |
| Camera GPIOs | Camera data and control | Defined in `camera_pins.h` |

## System Architecture

```text
                 ESP32-CAM AI Thinker
                         |
        +----------------+----------------+
        |                |                |
        v                v                v
   Camera Capture   Face Recognition   Wi-Fi Web
   & Face Detection    & ID Matching    Interface
                           |
                    +------+------+
                    |             |
                    v             v
               Green LED      Red LED
             Access Granted   Reset / Denied
```

## Face Recognition Workflow

```text
Start / Camera Ready
        |
        v
Capture Camera Frame
        |
        v
Detect Face
        |
        v
Recognize Face / Compare ID
        |
     +--+--+
     |     |
   Match  No Match
     |     |
     v     v
 Green    Red
  LED      LED
   |       |
   +---+---+
       |
       v
   Reset after
     6 seconds
```

## Face Enrollment and Recognition

The web interface provides an enrollment mode for registering a face.

During recognition:

1. A frame is captured from the camera.
2. A face is detected.
3. The detected face is compared with stored face IDs.
4. A successful match sets the recognition state.
5. The green LED is turned ON.
6. A non-match does not grant access and the red LED remains the default indication.

## Access Status Logic

| Condition | Green LED | Red LED |
|---|---|---|
| Idle / Reset | OFF | ON |
| Registered face recognized | ON | OFF |
| 6 seconds after access grant | OFF | ON |

The access indication uses a **6000 ms timer**. After the timer expires, the system returns to the default state.

## Web Interface

The ESP32-CAM hosts a browser-based interface on the local Wi-Fi network.

| Endpoint | Purpose |
|---|---|
| `/` | Main camera web interface |
| `/status` | Camera and recognition-control status |
| `/control` | Camera and recognition control commands |
| `/capture` | Still image capture |
| `/stream` | Live MJPEG camera stream |

The browser interface allows camera monitoring, face detection, enrollment, recognition, and live streaming without requiring a separate desktop application.

## Software Structure

```text
src/
├── face_recognition_esp32_cam.ino
├── app_httpd.cpp
├── camera_index.h
└── camera_pins.h
```

### File Description

**`face_recognition_esp32_cam.ino`**

Main application file containing:

- Camera initialization
- Wi-Fi connection
- GPIO configuration
- Web-server startup
- Access-status LED logic

**`app_httpd.cpp`**

Contains:

- Web server functions
- Live streaming
- Image capture
- Face detection
- Face recognition
- Face enrollment
- Camera and recognition controls

**`camera_index.h`**

Contains the embedded browser interface used by the camera server.

**`camera_pins.h`**

Contains the camera pin definitions for supported ESP32-CAM configurations.

## Setup

### 1. Arduino IDE

Install ESP32 board support in Arduino IDE.

### 2. Camera Model

Select:

```text
AI Thinker ESP32-CAM
```

### 3. Project Files

Keep the four source files in the required project structure.

### 4. Wi-Fi

In your private local copy of `face_recognition_esp32_cam.ino`, enter:

```cpp
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";
```

Do not commit real Wi-Fi credentials to GitHub.

### 5. Upload

Compile and upload the project to the ESP32-CAM.

### 6. Serial Monitor

Open the Serial Monitor at:

```text
115200 baud
```

After Wi-Fi connection, the ESP32-CAM prints its local IP address.

### 7. Browser

Open the displayed IP address in a browser connected to the same Wi-Fi network.

## Demonstration

A typical demonstration follows these steps:

1. Power the ESP32-CAM.
2. Wait for Wi-Fi connection.
3. Open the camera interface.
4. Enable face detection and recognition.
5. Enroll a face.
6. Present the registered face.
7. Observe the green LED when the face is recognized.
8. Present an unregistered face.
9. Observe that access is not granted.
10. Wait for the six-second timer to return the system to the reset state.

## Testing

| Test | Expected / Observed Behavior |
|---|---|
| Camera start-up | Camera initializes and the local web server starts |
| Wi-Fi connection | ESP32-CAM waits until the configured network is connected |
| Registered face | Face match is detected and green LED turns ON |
| Unknown face | No match and access is not granted |
| Access timeout | After 6 seconds, green LED turns OFF and red LED turns ON |
| Browser stream | Live camera view is available through the stream interface |

## Limitations

- The prototype uses LED feedback instead of a physical locking actuator.
- No dedicated anti-spoofing or liveness detection is included.
- Recognition performance depends on lighting, camera position, and image quality.
- The system is demonstrated on a local Wi-Fi network.
- Face enrollment and stored face IDs are intended for prototype use.

## Future Improvements

Possible extensions include:

- Add a buzzer for audio feedback
- Improve lighting conditions
- Add mobile or cloud notifications
- Add a dedicated enrollment/reset button
- Improve power stability
- Add event logging
- Add stronger authentication and anti-spoofing
- Integrate a physical lock or relay with suitable electrical isolation and safety design

## Reference

The implementation was developed with reference to:

**Tech StudyCell - ESP32-CAM Face Recognition Door Lock System**

https://youtu.be/PBZ-EiVIUc4

The final prototype adapts the access indication to two LEDs, using green for a recognized face and red for the default/reset state.

## Documentation

Detailed project documentation is available here:

[ESP32-CAM Face Recognition and Access Status System](documentation/ESP32_CAM_Face_Recognition_Access_Status_System.pdf)

## Author

**Mohammed Thoufeeq Ali S M**

B.S. Abdur Rahman Crescent Institute of Science and Technology
