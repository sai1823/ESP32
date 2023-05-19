#include <stdint.h>
#include "time.h"
#include <NTPClient.h>
#include <WiFiUdp.h>
#include <WiFi.h>
#include <ESPmDNS.h>

static const char file_name[] = "DiPoo";  //Dipoo 1.1 + 60s
const char* ssid = "Divya";
const char* password = "divya@1823";

String formattedDate;
String dayStamp;
String timeStamp;

int capture_interval = 600000; 
bool first_picture = true;
const char *post_url = "http://jckif.iitj.ac.in/test/upload.php";

bool internet_connected = false;
long current_millis;
long last_capture_millis = 0;

unsigned long check_wifi = 30000; 

#define TIMEZONE "MST7MDT,M3.2.0/2:00:00,M11.1.0/2:00:00"
#define BUFFSIZE 512

#define AVIOFFSET 240 

int  framesize = 7;   //framesize         
int  quality = 6;    //quality of images or videos             
int avi_length = 10;   //duration of video recording          

float most_recent_fps = 0;
int most_recent_avg_framesize = 0;

uint8_t* framebuffer;  
int framebuffer_len;

//include required libraries:
#include "esp_log.h"
#include "esp_http_server.h"
#include "esp_camera.h"
#include <WiFiClientSecure.h>
#include "esp_http_client.h"
#include "Arduino.h"

//define pins of AI Thinker 
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

//construct two pointers:
camera_fb_t * fb_curr = NULL;
camera_fb_t * fb_next = NULL;

#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "nvs_flash.h"
#include "nvs.h"
#include "soc/soc.h"
#include "soc/cpu.h"
#include "soc/rtc_cntl_reg.h"

static esp_err_t err;

//MIcro SD card
#include "driver/sdmmc_host.h"
#include "driver/sdmmc_defs.h"
#include "sdmmc_cmd.h"
#include "esp_vfs_fat.h"
#include <SD_MMC.h>

FILE *avifile = NULL;
FILE *idxfile = NULL;

long bp,bw;  //ap //aw
int diskspeed = 0;
char fname[100];  //char array 

static int i = 0;
unsigned long fileposition = 0;
uint16_t frame_cnt = 0,remnant = 0;;
uint32_t startms , elapsedms;
uint32_t uVideoLen = 0;

bool is_header = false;
int bad_jpg = 0 , extend_jpg = 0 , normal_jpg = 0 , file_number = 0 , file_group = 0;

long boot_time = 0;
long totalp , totalw;
uint8_t buf[BUFFSIZE];
unsigned long movi_size = 0, jpeg_size = 0, idx_offset = 0;
uint8_t zero_buf[4] = {0x00, 0x00, 0x00, 0x00};
uint8_t dc_buf[4] = {0x30, 0x30, 0x64, 0x63};    
uint8_t dc_and_zero_buf[8] = {0x30, 0x30, 0x64, 0x63, 0x00, 0x00, 0x00, 0x00};
uint8_t avi1_buf[4] = {0x41, 0x56, 0x49, 0x31};  
uint8_t idx1_buf[4] = {0x69, 0x64, 0x78, 0x31};    
uint8_t  vga_w[2] = {0x80, 0x02}; // 640
uint8_t  vga_h[2] = {0xE0, 0x01}; // 480
uint8_t  cif_w[2] = {0x90, 0x01}; // 400
uint8_t  cif_h[2] = {0x28, 0x01}; // 296
uint8_t svga_w[2] = {0x20, 0x03}; // 800
uint8_t svga_h[2] = {0x58, 0x02}; // 600
uint8_t sxga_w[2] = {0x00, 0x05}; // 1280
uint8_t sxga_h[2] = {0x00, 0x04}; // 1024
uint8_t uxga_w[2] = {0x40, 0x06}; // 1600
uint8_t uxga_h[2] = {0xB0, 0x04}; // 1200


const int avi_header[AVIOFFSET] PROGMEM = {
  0x52, 0x49, 0x46, 0x46, 0xD8, 0x01, 0x0E, 0x00, 0x41, 0x56, 0x49, 0x20, 0x4C, 0x49, 0x53, 0x54,
  0xD0, 0x00, 0x00, 0x00, 0x68, 0x64, 0x72, 0x6C, 0x61, 0x76, 0x69, 0x68, 0x38, 0x00, 0x00, 0x00,
  0xA0, 0x86, 0x01, 0x00, 0x80, 0x66, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x10, 0x00, 0x00, 0x00,
  0x64, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x80, 0x02, 0x00, 0x00, 0xe0, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x4C, 0x49, 0x53, 0x54, 0x84, 0x00, 0x00, 0x00,
  0x73, 0x74, 0x72, 0x6C, 0x73, 0x74, 0x72, 0x68, 0x30, 0x00, 0x00, 0x00, 0x76, 0x69, 0x64, 0x73,
  0x4D, 0x4A, 0x50, 0x47, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x01, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x0A, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x73, 0x74, 0x72, 0x66,
  0x28, 0x00, 0x00, 0x00, 0x28, 0x00, 0x00, 0x00, 0x80, 0x02, 0x00, 0x00, 0xe0, 0x01, 0x00, 0x00,
  0x01, 0x00, 0x18, 0x00, 0x4D, 0x4A, 0x50, 0x47, 0x00, 0x84, 0x03, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x49, 0x4E, 0x46, 0x4F,
  0x10, 0x00, 0x00, 0x00, 0x6A, 0x61, 0x6D, 0x65, 0x73, 0x7A, 0x61, 0x68, 0x61, 0x72, 0x79, 0x20,
  0x76, 0x34, 0x61, 0x20, 0x4C, 0x49, 0x53, 0x54, 0x00, 0x01, 0x0E, 0x00, 0x6D, 0x6F, 0x76, 0x69,
};

static void inline print_quartet(unsigned long i, FILE * fd){
  uint8_t y[4];
  y[0] = i % 0x100;
  y[1] = (i >> 8) % 0x100;
  y[2] = (i >> 16) % 0x100;
  y[3] = (i >> 24) % 0x100;

  size_t i1_err = fwrite(y , 1, 4, fd);
}

static void inline print_2quartet(unsigned long i, unsigned long j, FILE * fd){
  uint8_t y[8];
  y[0] = i % 0x100;
  y[1] = (i >> 8) % 0x100;
  y[2] = (i >> 16) % 0x100;
  y[3] = (i >> 24) % 0x100;
  y[4] = j % 0x100;
  y[5] = (j >> 8) % 0x100;
  y[6] = (j >> 16) % 0x100;
  y[7] = (j >> 24) % 0x100;

  size_t i1_err = fwrite(y , 1, 8, fd);
}

static esp_err_t config_camera() {
  camera_config_t config;

  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer = LEDC_TIMER_0;
  config.pin_d0 = Y2_GPIO_NUM;
  config.pin_d1 = Y3_GPIO_NUM;
  config.pin_d2 = Y4_GPIO_NUM;
  config.pin_d3 = Y5_GPIO_NUM;
  config.pin_d4 = Y6_GPIO_NUM;
  config.pin_d5 = Y7_GPIO_NUM;
  config.pin_d6 = Y8_GPIO_NUM;
  config.pin_d7 = Y9_GPIO_NUM;
  config.pin_xclk = XCLK_GPIO_NUM;
  config.pin_pclk = PCLK_GPIO_NUM;
  config.pin_vsync = VSYNC_GPIO_NUM;
  config.pin_href = HREF_GPIO_NUM;
  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 10000000; 
  config.pixel_format = PIXFORMAT_JPEG;
  config.frame_size = FRAMESIZE_UXGA; 
  config.jpeg_quality = 6;  
  config.fb_count = 7;

  //camera init
  err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed");
  }

  
  sensor_t * ss = esp_camera_sensor_get();
  ss->set_quality(ss, quality);
  ss->set_framesize(ss, (framesize_t)framesize);
  ss->set_brightness(ss, 1);  
  ss->set_saturation(ss, -2); 

  delay(500);
  for (int j = 0; j < 5; j++) {
    camera_fb_t * fb = esp_camera_fb_get();
    esp_camera_fb_return(fb);
    delay(50);
  }
}

static esp_err_t init_sdcard(){
  pinMode(13, PULLUP);
  esp_err_t ret = ESP_FAIL;
  sdmmc_host_t host = SDMMC_HOST_DEFAULT();
  host.flags = SDMMC_HOST_FLAG_1BIT;                     
  host.max_freq_khz = SDMMC_FREQ_HIGHSPEED;
  diskspeed = host.max_freq_khz;
  sdmmc_slot_config_t slot_config = SDMMC_SLOT_CONFIG_DEFAULT();
  slot_config.width = 1;                                  
  esp_vfs_fat_sdmmc_mount_config_t mount_config = {
    .format_if_mount_failed = false,
    .max_files = 8,
  };

  sdmmc_card_t *card;
  ret = esp_vfs_fat_sdmmc_mount("/sdcard", &host, &slot_config, &mount_config, &card);

  if (ret == ESP_OK) {
    Serial.println("SD card mount successfully!");
  }  else  {
    Serial.printf("Failed to mount SD card "); 
  }
  sdmmc_card_print_info(stdout, card);
}

camera_fb_t *  get_good_jpeg() {
  camera_fb_t * fb;
  do {
    bp = millis();
    fb = esp_camera_fb_get();
    totalp = totalp + millis() - bp;

    int x = fb->len;
    int foundffd9 = 0;

    for (int j = 1; j <= 1025; j++) {
      if (fb->buf[x - j] != 0xD9) {
        // no d9, try next for
      } else {

        if (fb->buf[x - j - 1] == 0xFF ) {
          if (j == 1) {
            normal_jpg++;
          } else {
            extend_jpg++;
          }
          if (j > 1000) { 
            Serial.print("Frame "); Serial.print(frame_cnt);
            Serial.print(", Len = "); Serial.print(x);
            Serial.print(", Corrent Len = "); Serial.print(x - j + 1);
            Serial.print(", Extra Bytes = "); Serial.println( j - 1);
          }
          foundffd9 = 1;
          break;
        }
      }
    }

    if (!foundffd9) {
      bad_jpg++;
      Serial.print("Bad jpeg, Len = "); Serial.println(x);
      esp_camera_fb_return(fb);

    } else {
      break;
    }

  } while (1);

  return fb;
}

#include <EEPROM.h>
struct eprom_data {
  int eprom_good;
  int file_group;
};

void do_eprom_read() {
  eprom_data ed;
  EEPROM.begin(200);
  EEPROM.get(0, ed);

  do_eprom_write();
  file_number = 1;
}

void do_eprom_write() {
  eprom_data ed;
  ed.file_group  = file_group;
  file_group++;

  Serial.println("Writing to EPROM ...");

  EEPROM.begin(200);
  EEPROM.put(0, ed);
  EEPROM.commit();
  EEPROM.end();
}

static esp_err_t start_avi() {
  Serial.println("Starting an avi hello");
  
  
 // sprintf(fname, "/sdcard/%s %s.%d + %ds.avi",  timeStamp, file_group, file_number, (millis() - boot_time) / 1000);
  sprintf(fname, "/sdcard/%s%s.%d.avi",  file_name, file_group, file_number);
  file_number++;
  

  avifile = fopen(fname, "w");
  idxfile = fopen("/sdcard/idx.tmp", "w");

  if (avifile != NULL)  {
    Serial.printf("File open: %s\n", fname);
  }  else  {
    Serial.println("Could not open file");
  }

  if (idxfile != NULL)  {
    Serial.printf("File open: %s\n", "/sdcard/idx.tmp");
  }  else  {
    Serial.println("Could not open file");

  }

  for ( i = 0; i < AVIOFFSET; i++)
  {
    char ch = pgm_read_byte(&avi_header[i]);
    buf[i] = ch;
  }

  size_t err = fwrite(buf, 1, AVIOFFSET, avifile);

  if (framesize == 6) {
    fseek(avifile, 0x40, SEEK_SET);
    err = fwrite(vga_w, 1, 2, avifile);
    fseek(avifile, 0xA8, SEEK_SET);
    err = fwrite(vga_w, 1, 2, avifile);
    fseek(avifile, 0x44, SEEK_SET);
    err = fwrite(vga_h, 1, 2, avifile);
    fseek(avifile, 0xAC, SEEK_SET);
    err = fwrite(vga_h, 1, 2, avifile);

  } else if (framesize == 10) {

    fseek(avifile, 0x40, SEEK_SET);
    err = fwrite(uxga_w, 1, 2, avifile);
    fseek(avifile, 0xA8, SEEK_SET);
    err = fwrite(uxga_w, 1, 2, avifile);
    fseek(avifile, 0x44, SEEK_SET);
    err = fwrite(uxga_h, 1, 2, avifile);
    fseek(avifile, 0xAC, SEEK_SET);
    err = fwrite(uxga_h, 1, 2, avifile);

  } else if (framesize == 9) {

    fseek(avifile, 0x40, SEEK_SET);
    err = fwrite(sxga_w, 1, 2, avifile);
    fseek(avifile, 0xA8, SEEK_SET);
    err = fwrite(sxga_w, 1, 2, avifile);
    fseek(avifile, 0x44, SEEK_SET);
    err = fwrite(sxga_h, 1, 2, avifile);
    fseek(avifile, 0xAC, SEEK_SET);
    err = fwrite(sxga_h, 1, 2, avifile);

  } else if (framesize == 7) {

    fseek(avifile, 0x40, SEEK_SET);
    err = fwrite(svga_w, 1, 2, avifile);
    fseek(avifile, 0xA8, SEEK_SET);
    err = fwrite(svga_w, 1, 2, avifile);
    fseek(avifile, 0x44, SEEK_SET);
    err = fwrite(svga_h, 1, 2, avifile);
    fseek(avifile, 0xAC, SEEK_SET);
    err = fwrite(svga_h, 1, 2, avifile);

  }  else if (framesize == 5) {

    fseek(avifile, 0x40, SEEK_SET);
    err = fwrite(cif_w, 1, 2, avifile);
    fseek(avifile, 0xA8, SEEK_SET);
    err = fwrite(cif_w, 1, 2, avifile);
    fseek(avifile, 0x44, SEEK_SET);
    err = fwrite(cif_h, 1, 2, avifile);
    fseek(avifile, 0xAC, SEEK_SET);
    err = fwrite(cif_h, 1, 2, avifile);
  }

  fseek(avifile, AVIOFFSET, SEEK_SET);

  Serial.print(F("\nRecording "));
  Serial.print(avi_length);
  Serial.println(" seconds.");

  startms = millis();

  totalp = 0;
  totalw = 0;

  jpeg_size = 0;
  movi_size = 0;
  uVideoLen = 0;
  idx_offset = 4;

  frame_cnt = 0;

  bad_jpg = 0;
  extend_jpg = 0;
  normal_jpg = 0;

} 

static esp_err_t another_save_avi(camera_fb_t * fb ) {
  int fblen;
  fblen = fb->len;
  jpeg_size = fblen;
  movi_size += jpeg_size;
  uVideoLen += jpeg_size;

  bw = millis();

  size_t dc_err = fwrite(dc_and_zero_buf, 1, 8, avifile);
  size_t err = fwrite(fb->buf, 1, fb->len, avifile);
  if (err != fb->len) {
    Serial.print("Error on avi write: err = "); Serial.print(err);
    Serial.print(" len = "); Serial.println(fb->len);
  }

  remnant = (4 - (jpeg_size & 0x00000003)) & 0x00000003;

  print_2quartet(idx_offset, jpeg_size, idxfile);

  idx_offset = idx_offset + jpeg_size + remnant + 8;

  jpeg_size = jpeg_size + remnant;
  movi_size = movi_size + remnant;
  if (remnant > 0) {
    size_t rem_err = fwrite(zero_buf, 1, remnant, avifile);
  }

  fileposition = ftell (avifile);     
  fseek(avifile, fileposition - jpeg_size - 4, SEEK_SET);   

  print_quartet(jpeg_size, avifile);   
  fileposition = ftell (avifile);

  fseek(avifile, fileposition + jpeg_size  , SEEK_SET);

  totalw = totalw + millis() - bw;

} 


static esp_err_t take_send_photo() {
  Serial.println("Taking picture...");
  camera_fb_t * fb = NULL;
  esp_err_t res = ESP_OK;

  fb = esp_camera_fb_get();
  if (!fb) {
    Serial.println("Camera capture failed");
    return ESP_FAIL;
  }

  esp_http_client_handle_t http_client;
  
  esp_http_client_config_t config_client = {0};
  config_client.url = post_url;
  config_client.event_handler = _http_event_handler;
  config_client.method = HTTP_METHOD_POST;

  http_client = esp_http_client_init(&config_client);

  esp_http_client_set_post_field(http_client, (const char *)fb->buf, fb->len);

  esp_http_client_set_header(http_client, "Content-Type", "video/avi");

  esp_err_t err = esp_http_client_perform(http_client);
  if (err == ESP_OK) {
    Serial.print("esp_http_client_get_status_code: ");
    Serial.println(esp_http_client_get_status_code(http_client));
  }

  esp_http_client_cleanup(http_client);

  esp_camera_fb_return(fb);
}


static esp_err_t end_avi() {

  unsigned long current_end = 0;

  current_end = ftell (avifile);

  Serial.println("End of avi - closing the files");

  elapsedms = millis() - startms;

  float fRealFPS = (1000.0f * (float)frame_cnt) / ((float)elapsedms);

  float fmicroseconds_per_frame = 1000000.0f / fRealFPS;
  uint8_t iAttainedFPS = round(fRealFPS);
  uint32_t us_per_frame = round(fmicroseconds_per_frame);

  fseek(avifile, 4 , SEEK_SET);
  print_quartet(movi_size + 240 + 16 * frame_cnt + 8 * frame_cnt, avifile);

  fseek(avifile, 0x20 , SEEK_SET);
  print_quartet(us_per_frame, avifile);

  unsigned long max_bytes_per_sec = movi_size * iAttainedFPS / frame_cnt;

  fseek(avifile, 0x24 , SEEK_SET);
  print_quartet(max_bytes_per_sec, avifile);

  fseek(avifile, 0x30 , SEEK_SET);
  print_quartet(frame_cnt, avifile);

  fseek(avifile, 0x8c , SEEK_SET);
  print_quartet(frame_cnt, avifile);

  fseek(avifile, 0x84 , SEEK_SET);
  print_quartet((int)iAttainedFPS, avifile);

  fseek(avifile, 0xe8 , SEEK_SET);
  print_quartet(movi_size + frame_cnt * 8 + 4, avifile);

  Serial.println(F("\n** Video recorded and saved **\n"));
  Serial.print(F("Recorded "));
  Serial.print(elapsedms / 1000);
  Serial.print(F("s in "));
  Serial.print(frame_cnt);
  Serial.print(F(" frames\nFile size is "));
  Serial.print(movi_size + 12 * frame_cnt + 4);
  
  Serial.printf("Writng the index, %d frames\n", frame_cnt);
  fseek(avifile, current_end, SEEK_SET);

  fclose(idxfile);
  take_send_photo();

  size_t i1_err = fwrite(idx1_buf, 1, 4, avifile);

  print_quartet(frame_cnt * 16, avifile);

  idxfile = fopen("/sdcard/idx.tmp", "r");

  if (idxfile != NULL)  {
    Serial.printf("File open: %s\n", "/sdcard/idx.tmp");
  }  else  {
    Serial.println("Could not open index file");
  }

  char * AteBytes;
  AteBytes = (char*) malloc (8);

  for (int i = 0; i < frame_cnt; i++) {
    size_t res = fread ( AteBytes, 1, 8, idxfile);
    size_t i1_err = fwrite(dc_buf, 1, 4, avifile);
    size_t i2_err = fwrite(zero_buf, 1, 4, avifile);
    size_t i3_err = fwrite(AteBytes, 1, 8, avifile);
  }

  free(AteBytes);
  fclose(idxfile);
  fclose(avifile);
  int xx = remove("/sdcard/idx.tmp");
  //take_send_photo();
}

// Time


time_t now;
struct tm timeinfo;
char localip[20];

#include <HTTPClient.h>
httpd_handle_t camera_httpd = NULL;
char the_page[4000];

#define PART_BOUNDARY "123456789000000000000987654321"

static const char* _STREAM_CONTENT_TYPE = "multipart/x-mixed-replace;boundary=" PART_BOUNDARY;
static const char* _STREAM_BOUNDARY = "\r\n--" PART_BOUNDARY "\r\n";
static const char* _STREAM_PART = "Content-Type: video/avi\r\nContent-Length: %u\r\n\r\n";

void deleteFolderOrFile(const char * val) {
  Serial.printf("Deleting : %s\n", val);
  File f = SD_MMC.open(val);
  if (!f) {
    Serial.printf("Failed to open %s\n", val);
    return;
  }

  if (f.isDirectory()) {  
    File file = f.openNextFile();
    while (file) {
      if (file.isDirectory()) {
        Serial.print("  DIR : ");
        Serial.println(file.name());
      } else {
        Serial.print("  FILE: ");
        Serial.print(file.name());
        Serial.print("  SIZE: ");
        Serial.print(file.size());
        if (SD_MMC.remove(file.name())) {
          Serial.println(" deleted.");
        } else {
          Serial.println(" FAILED.");
        }
      }
      file = f.openNextFile();
    }
    f.close();
    //Remove the dir
    if (SD_MMC.rmdir(val)) {
      Serial.printf("Dir %s removed\n", val);
    } else {
      Serial.println("Remove dir failed");
    }

  } else {
    //Remove the file
    if (SD_MMC.remove(val)) {
      Serial.printf("File %s deleted\n", val);
    } else {
      Serial.println("Delete failed");
    }
  }
}

void delete_old_stuff() {

  Serial.printf("Total space: %lluMB\n", SD_MMC.totalBytes() / (1024 * 1024));
  Serial.printf("Used space: %lluMB\n", SD_MMC.usedBytes() / (1024 * 1024));

  float full = 1.0 * SD_MMC.usedBytes() / SD_MMC.totalBytes();;
  if (full  < 0.5) {
    Serial.printf("Nothing deleted, %.1f%% disk full\n", 100.0 * full);
  } else {
    Serial.printf("memory full");
    Serial.printf("Disk is %.1f%% full ... deleting oldest\n", 100.0 * full);
    int oldest = 22222222;
    char oldestname[50];

    File f = SD_MMC.open("/");
    if (f.isDirectory()) {
      
      File file = f.openNextFile();
      while (file) {
        if (file.isDirectory()) {
 
          char foldname[50];
          strcpy(foldname, file.name());
          foldname[0] = 0x20;
          int i = atoi(foldname);
          if (i > 20200000 && i < oldest) {
            strcpy (oldestname, file.name());
            oldest = i;
          }
          
        } 
        file = f.openNextFile();
     
      deleteFolderOrFile(oldestname);
      f.close();
    }
  }
}

}

void setup() {
  Serial.begin(115200);
  pinMode(33, OUTPUT);             // little red led on back of chip
  digitalWrite(33, LOW);           // turn on the red LED on the back of chip

  pinMode(4, OUTPUT);               // Blinding Disk-Avtive Light
  digitalWrite(4, LOW);             // turn off

  pinMode(12, INPUT_PULLUP);        // pull this down to stop recording
  Serial.println("Setting up the camera ...");

  if (init_wifi()) {
    internet_connected = true;
    Serial.println("Internet connected");
  }

  config_camera();
  
  // SD camera init
  esp_err_t card_err = init_sdcard();
  if (card_err != ESP_OK) {
    Serial.printf("SD Card init failed with error 0x%x", card_err);
    
    return;
  }

  digitalWrite(33, HIGH);         // red light turns off when setup is complete
  Serial.println("Warming up the camera ");
  do_eprom_read();
  framebuffer = (uint8_t*)ps_malloc(512 * 1024); // buffer to store a jpg in motion
  
  Serial.println("  End of setup()\n\n");

  boot_time = millis();
}

bool init_wifi() {
  int connAttempts = 0;
  Serial.println("\r\nConnecting to: " + String(ssid));
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED ) {
    delay(500);
    Serial.print(".");
    if (connAttempts > 10) return false;
    connAttempts++;
  }
  return true;
}
int first = 1;
int frames = 0;
long frame_start = 0;
long frame_end = 0;
long frame_total = 0;
long frame_average = 0;
long loop_average = 0;
long loop_total = 0;
long total_frame_data = 0;
long last_frame_length = 0;
int done = 0;
long avi_start_time = 0;
long avi_end_time = 0;
int stop = 0;
int we_are_already_stopped = 0;
long total_delay = 0;
long bytes_before_last_100_frames = 0;
long time_before_last_100_frames = 0;

esp_err_t _http_event_handler(esp_http_client_event_t *evt) {
  switch (evt->event_id) {
    case HTTP_EVENT_ERROR:
      Serial.println("HTTP_EVENT_ERROR");
      break;
    case HTTP_EVENT_ON_CONNECTED:
      Serial.println("HTTP_EVENT_ON_CONNECTED");
      break;
    case HTTP_EVENT_HEADER_SENT:
      Serial.println("HTTP_EVENT_HEADER_SENT");
      break;
    case HTTP_EVENT_ON_HEADER:
      Serial.println();
      Serial.printf("HTTP_EVENT_ON_HEADER, key=%s, value=%s", evt->header_key, evt->header_value);
      break;
    case HTTP_EVENT_ON_DATA:
      Serial.println();
      Serial.printf("HTTP_EVENT_ON_DATA, len=%d", evt->data_len);
      if (!esp_http_client_is_chunked_response(evt->client)) {
        // Write out data
        // printf("%.*s", evt->data_len, (char*)evt->data);
      }
      break;
    case HTTP_EVENT_ON_FINISH:
      Serial.println("");
      Serial.println("HTTP_EVENT_ON_FINISH");
      break;
    case HTTP_EVENT_DISCONNECTED:
      Serial.println("HTTP_EVENT_DISCONNECTED");
      break;
  }
  return ESP_OK;
}



void loop() {
  if (first) {
    //take_send_photo();
    first = 0;
  }
  current_millis = millis();
  frames++;
  frame_cnt = frames;

  stop = digitalRead(12);
  if ((WiFi.status() != WL_CONNECTED) && (current_millis > check_wifi)) {
    Serial.println("Reconnecting to WiFi...");
    WiFi.disconnect();
    WiFi.begin(ssid, password);
    check_wifi = millis() + 30000;
  }

  if (frames == 1 ) {                              // start the avi

    if (stop == 0) {

      if (we_are_already_stopped == 0) Serial.println("\n\nCannot start video, as Pin 12 is connected to GND\n\n");
      frames--;
      we_are_already_stopped = 1;
      delay(1000);

    } else {
      we_are_already_stopped = 0;

      avi_start_time = millis();
      Serial.printf("Start the avi ... at %d\n", avi_start_time);

      fb_curr = get_good_jpeg();                     // should take zero time

      if (deleteFolderOrFile) delete_old_stuff();

      start_avi();

      fb_next = get_good_jpeg();                    // should take nearly zero time due to time spent writing header

      another_save_avi( fb_curr);                  // put first frame in avi

      digitalWrite(33, frames % 2);                // blink

      esp_camera_fb_return(fb_curr);               // get rid of first frame
      fb_curr = NULL;

    }
  } else if ( stop == 0 ||  millis() > (avi_start_time + avi_length * 1000)) { // end the avi

    fb_curr = fb_next;
    fb_next = NULL;

    another_save_avi(fb_curr);                 // save final frame of avi
    digitalWrite(33, frames % 2);
    esp_camera_fb_return(fb_curr);
    fb_curr = NULL;

    end_avi();                                // end the movie

    digitalWrite(33, HIGH);          // light off
    avi_end_time = millis();

    float fps = frames / ((avi_end_time - avi_start_time) / 1000) ;
    Serial.printf("End the avi at %d.  It was %d frames, %d ms at %.1f fps...\n", millis(), frames, avi_end_time, avi_end_time - avi_start_time, fps);

    frames = 0;             // start recording again on the next loop

  } else {  
    
    fb_curr = fb_next;           // we will write a frame, and get the camera preparing a new one
    fb_next = get_good_jpeg();    // should take near zero, unless the sd is faster than the camera, when we will have to wait for the camera
    another_save_avi(fb_curr);

    digitalWrite(33, frames % 2);

    esp_camera_fb_return(fb_curr);
    fb_curr = NULL;

    if (frames % 100 == 0 ) {     // print some status every 100 frames
      if (frames == 100) {
        Serial.printf("\n\nframesize %d, quality %d, avi length %d\n\n", framesize, quality, avi_length);
        bytes_before_last_100_frames = movi_size;
        time_before_last_100_frames = millis();
      }
      most_recent_fps = 100.0 / ((millis() - time_before_last_100_frames) / 1000.0) ;
      most_recent_avg_framesize = (movi_size - bytes_before_last_100_frames) / 100;
      total_delay = 0;
      bytes_before_last_100_frames = movi_size;
      time_before_last_100_frames = millis();
    }
    
  if (current_millis - last_capture_millis > capture_interval) {
    last_capture_millis = millis();
    take_send_photo();
  }
  }
}
