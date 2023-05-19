# ESP32
Esp32- Cam Module

This code is intended for the ESP-32 cam module project, version 1.1, which captures images using an ESP32-based camera and saves them to an SD card. The captured images can also be uploaded to a server using HTTP POST requests. The code provides the necessary functionalities for configuring the camera, capturing images, and managing the SD card.

## Hardware Requirements
- ESP32-based development board
- Camera module compatible with ESP32
- SD card module compatible with ESP32

## Libraries
The code relies on several libraries that need to be installed in your development environment:
- NTPClient: Provides NTP (Network Time Protocol) client functionality for time synchronization.
- WiFiUdp: Enables UDP communication over Wi-Fi.
- WiFi: Enables Wi-Fi connectivity for the ESP32 board.
- ESPmDNS: Enables mDNS (Multicast DNS) functionality for domain name resolution.
- ESP32 Camera: Provides camera functionality for ESP32-based boards.
- WiFiClientSecure: Enables secure Wi-Fi client functionality.
- ESP HTTP Server: Implements an HTTP server for handling HTTP requests.
- ESP HTTP Client: Implements an HTTP client for making HTTP requests.

## Configuration
Before running the code, make sure to configure the following variables:

- `ssid`: Set the SSID (name) of your Wi-Fi network.
- `password`: Set the password for your Wi-Fi network.
- `capture_interval`: Set the interval (in milliseconds) between image captures.
- `post_url`: Set the URL for uploading images to a server.

## Pin Definitions
The code utilizes specific GPIO pin assignments for the camera module. Ensure that the following pins are correctly connected between the ESP32 board and the camera module:

```
#define PWDN_GPIO_NUM     32
#define RESET_GPIO_NUM    -1
#define XCLK_GPIO_NUM      0
#define SIOD_GPIO_NUM     26
#define SIOC_GPIO_NUM     27
#define Y9_GPIO_NUM       35
#define Y8_GPIO_NUM       34
#define Y7_GPIO_NUM       39
#define Y6_GPIO_NUM       36
#define Y5_GPIO_NUM       21
#define Y4_GPIO_NUM       19
#define Y3_GPIO_NUM       18
#define Y2_GPIO_NUM        5
#define VSYNC_GPIO_NUM    25
#define HREF_GPIO_NUM     23
#define PCLK_GPIO_NUM     22
```

Please refer to the documentation of your specific camera module for pin connections.

## Camera Configuration
The `config_camera()` function is responsible for configuring the camera settings, such as resolution, quality, brightness, and saturation. Adjust these settings according to your requirements by modifying the respective function calls in the `config_camera()` function.

## SD Card Configuration
The code uses the SDMMC library to interface with the SD card module. Ensure that the SD card module is connected to the appropriate pins on the ESP32 board. By default, the code assumes that the SD card module is connected to GPIO pin 13. Modify the pin assignment in the `init_sdcard()` function if necessary.

## Usage
1. Set up the hardware connections according to the pin definitions and ensure that the required libraries are installed in your development environment.
2. Configure the necessary variables, such as Wi-Fi credentials, capture interval, and upload URL.
3. Compile and upload the code to your ESP32-based development board.
4. Monitor the serial output to observe the behavior of the code.
5. The ESP32 will capture images at the specified interval and save them to the SD card.
6. If internet connectivity is available, the ESP32 will attempt to upload the captured images to the specified server using HTTP POST requests.

## Troubleshooting
-

 If the code fails to compile or upload, verify that all the required libraries are correctly installed in your development environment.
- Double-check the hardware connections and ensure that the camera module and SD card module are correctly connected to the ESP32 board.
- If the Wi-Fi connection fails, ensure that the SSID and password are correct and that the Wi-Fi network is accessible.
- If image capture or SD card functionality does not work, review the pin assignments and ensure that the camera module and SD card module are properly connected.

## License
This code is released under the [MIT License](LICENSE).

## Acknowledgments
The code is based on the DiPoo project by [Author's Name]. Special thanks to the contributors and the open-source community for their valuable resources and support.

## References
- ESP32 Camera Library: [Link to the library documentation]
- NTPClient Library: [Link to the library documentation]
- ESP HTTP Server Library: [Link to the library documentation]
- ESP HTTP Client Library: [Link to the library documentation]
