# ESP32
Esp32- Cam Module

Description
The code provided is an example implementation for capturing images using an AI Thinker camera module and saving them to an SD card. The code utilizes the ESP32 board and various libraries to control the camera module, establish an internet connection, and perform image capturing and storage. The captured images can also be uploaded to a server using HTTP POST requests. The code provides the necessary functionalities for configuring the camera, capturing images, and managing the SD card.

## Prerequisites
Before running the code, ensure that you have the following:

-ESP32 board
-AI Thinker camera module
-SD card module
-Arduino IDE with ESP32 board support
-Required libraries: NTPClient, WiFiUdp, WiFi, ESPmDNS, esp_camera, ESP_LOG, esp_http_server, esp_http_client, Arduino, driver/sdmmc_host, driver/sdmmc_defs, sdmmc_cmd, esp_vfs_fat, SD_MMC

## Libraries
- NTPClient: Provides NTP (Network Time Protocol) client functionality for time synchronization.
- WiFiUdp: Enables UDP communication over Wi-Fi.
- WiFi: Enables Wi-Fi connectivity for the ESP32 board.
- ESPmDNS: Enables mDNS (Multicast DNS) functionality for domain name resolution.
- ESP32 Camera: Provides camera functionality for ESP32-based boards.
- WiFiClientSecure: Enables secure Wi-Fi client functionality.
- ESP HTTP Server: Implements an HTTP server for handling HTTP requests.
- ESP HTTP Client: Implements an HTTP client for making HTTP requests.

## Configuration
To configure the code for your specific setup, make the following changes:

Set the appropriate values for Wi-Fi network credentials:

- `ssid`: Set the SSID (name) of your Wi-Fi network.
- `password`: Set the password for your Wi-Fi network.
cpp
Copy code
const char* ssid = "Your_WiFi_SSID";
const char* password = "Your_WiFi_Password";
Adjust the capture interval (in milliseconds) according to your requirements:

- `capture_interval`: Set the interval (in milliseconds) between image captures.
cpp
Copy code
int capture_interval = 600000; // 10 minutes
Specify the URL for uploading the captured images:

- `post_url`: Set the URL for uploading images to a server.
cpp
Copy code
const char *post_url = "http://example.com/upload.php";
Modify the pins assigned to the AI Thinker camera module according to your pin configuration:


cpp
Copy code
// Define pins of AI Thinker
#define PWDN_GPIO_NUM     32
#define RESET_GPIO_NUM    -1
#define XCLK_GPIO_NUM       0
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
Before running the code, make sure to configure the following variables:

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
- If the code fails to compile or upload, verify that all the required libraries are correctly installed in your development environment.
- Double-check the hardware connections and ensure that the camera module and SD card module are correctly connected to the ESP32 board.
- If the Wi-Fi connection fails, ensure that the SSID and password are correct and that the Wi-Fi network is accessible.
- If image capture or SD card functionality does not work, review the pin assignments and ensure that the camera module and SD card module are properly connected.

## License
This code is provided under the MIT License. Feel free to modify, distribute, and use it according to your needs.

## References
1. ESP32 Camera Library:
   - GitHub Repository: [https://github.com/espressif/esp32-camera](https://github.com/espressif/esp32-camera)
   - ESP32 Camera Documentation: [https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/camera.html](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/camera.html)

2. NTPClient Library:
   - Arduino Library Manager: [https://www.arduino.cc/reference/en/libraries/ntpclient/](https://www.arduino.cc/reference/en/libraries/ntpclient/)
   - GitHub Repository: [https://github.com/arduino-libraries/NTPClient](https://github.com/arduino-libraries/NTPClient)

3. ESP HTTP Server Library:
   - GitHub Repository: [https://github.com/espressif/esp-http-server](https://github.com/espressif/esp-http-server)
   - ESP HTTP Server Documentation: [https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/protocols/esp_http_server.html](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/protocols/esp_http_server.html)

4. ESP HTTP Client Library:
   - GitHub Repository: [https://github.com/espressif/esp-http-client](https://github.com/espressif/esp-http-client)
   - ESP HTTP Client Documentation: [https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/protocols/esp_http_client.html](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/protocols/esp_http_client.html)
