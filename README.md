# Raspberry Pi Camera Application

This is a Python-based application for controlling a Raspberry Pi camera system with web interface capabilities. The application provides live video streaming, photo capture, LED control, and remote management features.

## Features

### Core Functionality
- **Live Video Streaming**: Real-time video feed accessible via web browser
- **Photo Capture**: High-quality image capture with autofocus capability
- **Web Interface**: RESTful API for remote control and monitoring
- **LED Control**: Manage UV LEDs for various applications
- **Automatic Power Management**: Inactivity-based shutdown for battery operation

### Hardware Controls
- Physical button controls for LED toggling and photo capture
- GPIO-based LED indicators (LED1, LED2, Capture LED)
- Hardware autofocus support for better image quality

### Remote Management
- Device status monitoring
- Image gallery with pagination
- Client connectivity tracking with timeout detection
- Remote power-off capability

### Power Management
- Configurable battery operation mode
- Automatic shutdown after 15 minutes of inactivity
- Idle mode to conserve power after 5 minutes
- LED indicators for system status

## Installation Guide

### Prerequisites
- Raspberry Pi (4B recommended)
- Raspberry Pi Camera Module
- GPIO buttons and LEDs (connected to specified pins)
- Python 3.7+
- Raspberry Pi OS (Bullseye recommended)

### Hardware Setup
1. Connect the camera module to the CSI port
2. Connect buttons to GPIO pins:
   - LED1 Button: GPIO 16
   - LED2 Button: GPIO 20
   - Capture Button: GPIO 21
3. Connect LEDs to GPIO pins:
   - LED1: GPIO 18
   - LED2: GPIO 23
   - Capture LED: GPIO 15

### Software Installation
1. Update your system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install required packages:
   ```bash
   sudo apt install python3-pip libcamera-dev libcap-dev
   ```

3. Install Python dependencies:
   ```bash
   pip3 install flask picamera2 opencv-python gpiozero
   ```

4. Configure permissions for power management:
   ```bash
   sudo chmod +s $(which poweroff)
   ```

### Configuration
1. Edit `final.py` to configure:
   - Set `BATTERY_OPERATION = True` for battery-powered use
   - Adjust camera settings (resolution, rotation)
   - Modify GPIO pin assignments if needed

2. Create a systemd service for auto-start (optional):
   ```bash
   sudo nano /etc/systemd/system/camera.service
   ```

   Add the following content:
   ```ini
   [Unit]
   Description=Raspberry Pi Camera Service
   After=network.target

   [Service]
   ExecStart=/usr/bin/python3 /path/to/final.py
   WorkingDirectory=/path/to/
   StandardOutput=journal
   StandardError=journal
   Restart=always
   User=pi

   [Install]
   WantedBy=multi-user.target
   ```

   Enable the service:
   ```bash
   sudo systemctl enable camera.service
   sudo systemctl start camera.service
   ```

## Usage

### Starting the Application
Run the application directly:
```bash
python3 final.py
```

The application will start a Flask web server on port 5000.

### Web Interface Endpoints
- `/video_feed`: Live video stream
- `/capture`: Trigger photo capture
- `/led1_toggle`: Toggle LED1
- `/led2_toggle`: Toggle LED2
- `/device_status`: Check device status
- `/list_files`: List captured images (supports pagination)
- `/images/<filename>`: Access captured images
- `/ping`: Client connectivity check
- `/poweroff`: Remote shutdown

### Physical Controls
- **Short Press Capture Button**: Update inactivity timer
- **Long Hold Capture Button** (2+ seconds): Capture photo
- **LED Buttons**: Toggle respective LEDs

## API Documentation

### GET Endpoints
- `GET /device_status`: Returns device online status
- `GET /led1_status`: Returns LED1 status
- `GET /led2_status`: Returns LED2 status
- `GET /uv_status`: Returns status of both UV LEDs
- `GET /ping`: Updates client status and returns pong
- `GET /list_files?page=1&per_page=10`: Lists images with pagination
- `GET /images/<filename>`: Serves a specific image

### POST Endpoints
- `POST /capture`: Triggers photo capture

### Control Endpoints
- `GET /led1_toggle`: Toggles LED1
- `GET /led2_toggle`: Toggles LED2
- `GET /poweroff`: Initiates system shutdown

## Project Structure
```
.
├── final.py              # Main application file
├── img/                  # Directory for captured images
├── README.md             # This file
└── requirements.txt      # Python dependencies (to be created)
```

## Contributing
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a pull request

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Support
For support, please open an issue on the repository or contact the maintainers.