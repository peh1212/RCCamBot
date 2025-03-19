![header](https://capsule-render.vercel.app/api?type=waving&height=280&color=EECCCC&text=ESP32-CAM%20Project&fontColor=363636&fontAlignY=40&fontSize=60&textBg=false)

***
### 프로젝트 개요
:punch: **1인 프로젝트** <br>
안드로이드 앱으로 ESP32-CAM 기반의 자동차 2대를 원격 제어하는 프로젝트입니다. <br>
차량의 이동을 제어하고, LED 라이트를 ON/OFF하고, 카메라의 방향을 제어하고, 카메라의 영상을 스트리밍하는 기능을 구현합니다. <br><br>

:cloud: **프로젝트 내용** <br>
![아키텍쳐 크기 줄인것](https://github.com/user-attachments/assets/a717732b-9596-40f3-9bce-b749cc75c6c2) <br>
이 프로젝트는 ESP32-CAM RC카와 스프링부트 API와 안드로이드 앱을 개발하여 디바이스-서버-클라이언트 간의 HTTP 통신 시스템을 구축하는 것을 목표로 합니다. <br>
ESP32-CAM은 Wi-Fi 및 Bluetooth 통신 모듈이 내장된 ESP32 MCU 기반의 카메라 모듈로, 이 모듈을 활용하여 RC카를 제작하고, 안드로이드앱에서 원격제어와 실시간 카메라 스트리밍 기능을 구현합니다. <br>
ESP32-CAM RC카는 와이파이를 통해 스프링부트 서버와 통신하며 HTTP POST 요청을 수신하여, RC카의 이동 및 카메라 회전, LED 라이트 제어를 수행하고, HTTP GET 요청을 처리하여 실시간 영상 스트리밍을 제공하고 사진을 촬영하여 저장합니다. <br>
스프링부트는 안드로이드 앱과 ESP32-CAM RC카가 통신할 수 있도록 API를 제공하며, PostgreSQL DB를 사용하여 자동차의 ID, IP주소 등의 데이터를 처리하고 관리합니다. <br>
안드로이드 앱에서는 사용자의 입력을 받아 레트로핏을 이용하여 스프링부트 서버와 HTTP 통신할 수 있으며, ESP32-CAM RC카의 IP 주소를 스캔하여 고유 ID와 함께 저장할 수 있고, 이를 통해 2대 이상의 자동차를 각각 제어할 수 있습니다. <br><br>


:calendar: **프로젝트 기간** : <br>
`2025. 2 ~ 2025. 3` <br><br>

:books: **기술스택** : <br>
![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white) <br>
![Java](https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=coffeescript&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Retrofit](https://img.shields.io/badge/Retrofit-00B5A6?style=for-the-badge&logo=android&logoColor=white) <br>
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white) <br><br>

:hammer: **개발도구** : <br>
![Arduino IDE](https://img.shields.io/badge/Arduino%20IDE-00979D?style=for-the-badge&logo=arduino&logoColor=white) <br>
![Talend API Tester](https://img.shields.io/badge/Talend_API_Tester-FF6D70?style=for-the-badge&logo=talend&logoColor=white) <br>
![IntelliJ IDEA](https://img.shields.io/badge/IntelliJ%20IDEA-000000?style=for-the-badge&logo=intellij-idea&logoColor=white) <br>
![Android Studio](https://img.shields.io/badge/Android%20Studio-3DDC84?style=for-the-badge&logo=android-studio&logoColor=white) <br>
![Thonny](https://img.shields.io/badge/Thonny-3776AB?style=for-the-badge&logo=python&logoColor=white) <br><br>

:page_with_curl: **목차** : <br>
[1. 하드웨어 설계](#1-하드웨어-설계) <br>
[2. 펌웨어 개발](#2-펌웨어-개발) <br>
[3. 백엔드 개발](#3-백엔드-개발) <br>
[4. 안드로이드앱 개발](#4-안드로이드앱-개발) <br>
[5. 인공지능 자율주행](#5-인공지능-자율주행) <br>
<br>

***
### 1. 하드웨어 설계
:zap: **1. 전체 회로도 그리기** <br>
![전체회로도 크기 줄인것](https://github.com/user-attachments/assets/d6328f18-46c5-4bb7-a0d2-b7a5faa7cfac) <br>
위와 같이 전체 회로를 구성합니다. <br>
전원은 18650 배터리를 2S2P로 연결하여 사용합니다. <br>
스텝다운 컨버터를 사용하여 ESP32-CAM에 5V 전원을 공급합니다. <br>
서보모터 라인에 스텝다운 컨버터를 사용하여 5V 전원을 공급합니다. <br>
2채널 DC모터드라이버 모듈을 통해 DC모터 4개를 제어합니다. <br>
스텝업 컨버터를 사용하여 12V LED 라이트에 전원을 공급합니다. <br>
ESP32-CAM으로 서보모터의 PWM신호를 생성하고, DC모터 드라이버를 제어하고, LED 릴레이를 스위칭합니다. <br>
ESP32-CAM의 출력 핀은 아래와 같이 배정합니다.
| ESP32-CAM 핀 | 연결 부품 |
|-------|-------|
| GPIO 12 | 서보모터1 Pan(좌우 회전) |
| GPIO 13 | 서보모터2 Tilt(상하 회전) |
| GPIO 3 | 릴레이 |
| GPIO 14 | DC모터 드라이버 채널A(좌측 바퀴) IN 1 |
| GPIO 15 | DC모터 드라이버 채널A(좌측 바퀴) IN 2 |
| GPIO 2 | DC모터 드라이버 채널B(우측 바퀴) IN 1 |
| GPIO 4 | DC모터 드라이버 채널B(우측 바퀴) IN 2 |

<br>

:pushpin: **2. 자동차에 필요한 부품 준비하기 (1대분 기준)** <br>
`18650 배터리 (3.7V 2200mAh)` 4개 <br>
`전원 똑딱이 스위치` 1개 <br>
`DC-DC 스텝다운 컨버터(LM2596)` 2개 <br>
`MCU(ESP32-CAM)` 1개 <br>
`ESP32-CAM WiFi 안테나 (3dBi)` 1개 -> `6dBi로 교체` <br>
`팬틸트용 SG90 서보모터` 2개 <br>
`2채널 DC모터 드라이버(DRV8833)` 1개 <br>
`아두이노 바퀴용 DC모터` 4개 <br>
`DC-DC 스텝업 컨버터(XL6009)` 1개 <br>
`3.3V 릴레이` 1개 <br>
`12V LED 라이트` 1개 <br><br>

***
### 2. 펌웨어 개발
:memo: **1. 아두이노 IDE를 이용하여, HTTP 요청으로 카메라 스트리밍, 서보모터, DC모터, 릴레이를 제어하는 코드를 구현하고, ESP32-CAM에 업로드합니다.** <br>
:warning: `-와이파이 이름과 비밀번호, 서버의 IP 주소는 하드코딩하여 ESP32-CAM에 업로드합니다.` <br>
`-와이파이 환경이 바뀌거나, 서버를 다른 환경에서 구동한다면 수정해주어야 합니다.` <br>
`-와이파이 정보는 별도의 환경설정 파일을 사용하여 암호화할 수 있습니다.` <br>
`-클라우드 서버에서 구동하는 경우 서버의 도메인 주소를 사용하여 IP 주소를 직접 입력하지 않도록 자동으로 처리할 수 있습니다.` <br>
`-ESP32-CAM 부팅 시 ESP32-CAM의 ip주소와 MAC주소를 JSON으로 변환하여 스프링부트 서버로 전송합니다.` <br>
`-스트리밍 중 영상 캡쳐(사진으로 저장) 기능은 base64로 인코딩하여 스프링부트 서버로 전송하고, 스프링부트 서버의 서비스 로직에서 이미지로 디코딩하여 로컬에 저장합니다.` <br>
`-스트리밍 중 HTTP 요청을 처리하는 방식은 AsyncWebServer 객체를 생성하여 비동기 처리하는 대신 FreeRTOS를 이용하여 멀티스레드에서 처리합니다.` <br>
`-아두이노 IDE에서 보드는 ESP32 Wrover Module을 선택합니다.` <br>

#### ESP32CAM.ino
```C++
#include <WiFi.h>
#include "esp_camera.h"
#include <ESP32Servo.h>
#include <HTTPClient.h>
#include <WebServer.h>
#include <Base64.h>

// WiFi 정보와 스프링부트 서버 주소 설정
#define WIFI_SSID "**********"
#define WIFI_PASSWORD "**********"
#define SERVER_URL "**********"

// 카메라 모델 정의 및 핀 설정
#define CAMERA_MODEL_AI_THINKER
#define PWDN_GPIO_NUM  32
#define RESET_GPIO_NUM -1
#define XCLK_GPIO_NUM  0
#define SIOD_GPIO_NUM  26
#define SIOC_GPIO_NUM  27
#define Y9_GPIO_NUM    35
#define Y8_GPIO_NUM    34
#define Y7_GPIO_NUM    39
#define Y6_GPIO_NUM    36
#define Y5_GPIO_NUM    21
#define Y4_GPIO_NUM    19
#define Y3_GPIO_NUM    18
#define Y2_GPIO_NUM    5
#define VSYNC_GPIO_NUM 25
#define HREF_GPIO_NUM  23
#define PCLK_GPIO_NUM  22

// 서보모터 핀 설정
#define SERVO_PIN_1 12
#define SERVO_PIN_2 13

// DC모터 핀 설정
// 바퀴는 4개이지만 2개인것처럼 제어, 왼쪽 2개, 오른쪽 2개가 같이 움직임
#define LEFT_MOTOR_FORWARD_PIN 14
#define LEFT_MOTOR_BACKWARD_PIN 15
#define RIGHT_MOTOR_FORWARD_PIN 2
#define RIGHT_MOTOR_BACKWARD_PIN 4

// DC모터 PWM 설정
#define LEFT_MOTOR_PWM_CHANNEL_FORWARD 6
#define LEFT_MOTOR_PWM_CHANNEL_BACKWARD 7
#define RIGHT_MOTOR_PWM_CHANNEL_FORWARD 10
#define RIGHT_MOTOR_PWM_CHANNEL_BACKWARD 11
#define PWM_FREQUENCY 1000
#define PWM_RESOLUTION 8

// 릴레이 핀 설정
#define RELAY_PIN 3

// 서보모터 객체 생성
// 1:PAN 2:TILT
Servo servo1;
Servo servo2;

// 서보모터의 현재 각도를 저장할 변수
// 0~180도, 초기위치 90도
int currentAngle1 = 90;
int currentAngle2 = 90;

// DC모터 속도설정(0~255)
// 듀티비 50%
int motorSpeed = 127;

// 웹 서버 객체 생성
WebServer server(80);

// 서버 엔드포인트 정의
const char* stream_url = "/stream";
const char* servo_url = "/servo";
const char* motor_url = "/motor";
const char* relay_url = "/relay";
const char* capture_url = "/capture";
const char* register_url = "/register";
const char* image_upload_url = "/image/upload";

// 함수 선언
void handleStream(void* parameter);
void handleServo();
void handleMotor();
void handleRelay();
void handleCapture();
void sendDeviceInfo();
void startServer();


void setup() {
  Serial.begin(115200);
  // WiFi 연결
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected");

  // 카메라 설정
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
  config.pin_sccb_sda = SIOD_GPIO_NUM;
  config.pin_sccb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;

  // 카메라 해상도
  // (UXGA: 1600x1200, XGA: 1024x768, SVGA: 800x600, VGA: 640x480, CIF: 400x296, QVGA: 320x240, QQVGA: 160x120)
  if (psramFound()) {
    config.frame_size = FRAMESIZE_CIF;
    config.jpeg_quality = 10;
    config.fb_count = 2;
  } else {
    config.frame_size = FRAMESIZE_QVGA;
    config.jpeg_quality = 12;
    config.fb_count = 1;
  }

  // 카메라 초기화
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
  }

  // 서보모터 설정
  servo1.attach(SERVO_PIN_1);
  servo2.attach(SERVO_PIN_2);

  // DC모터 설정
  ledcSetup(LEFT_MOTOR_PWM_CHANNEL_FORWARD, PWM_FREQUENCY, PWM_RESOLUTION);
  ledcSetup(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, PWM_FREQUENCY, PWM_RESOLUTION);
  ledcSetup(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, PWM_FREQUENCY, PWM_RESOLUTION);
  ledcSetup(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, PWM_FREQUENCY, PWM_RESOLUTION);
  ledcAttachPin(LEFT_MOTOR_FORWARD_PIN, LEFT_MOTOR_PWM_CHANNEL_FORWARD);
  ledcAttachPin(LEFT_MOTOR_BACKWARD_PIN, LEFT_MOTOR_PWM_CHANNEL_BACKWARD);
  ledcAttachPin(RIGHT_MOTOR_FORWARD_PIN, RIGHT_MOTOR_PWM_CHANNEL_FORWARD);
  ledcAttachPin(RIGHT_MOTOR_BACKWARD_PIN, RIGHT_MOTOR_PWM_CHANNEL_BACKWARD);

  // 릴레이 설정
  pinMode(RELAY_PIN, OUTPUT);

  // 서보모터 현재위치 초기화
  servo1.write(currentAngle1);
  servo2.write(currentAngle2);

  // DC모터 초기상태 정지
  ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, 0);
  ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
  ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, 0);
  ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);

  // 릴레이 초기상태 OFF
  digitalWrite(RELAY_PIN, LOW);

  // 서버로 디바이스 정보 전송
  sendDeviceInfo();

  // HTTP 서버 시작
  startServer();
}

void loop() {
  server.handleClient();
}


// MJPEG 스트리밍 함수
void handleStream(void* parameter) {
  WebServer* server = (WebServer*) parameter;
  WiFiClient client = server->client();
  client.setTimeout(5); // 클라이언트 응답 대기시간 5초

  // 응답설정
  camera_fb_t * fb = NULL;
  String boundary = "frame";
  String response = "HTTP/1.1 200 OK\r\n";
  response += "Content-Type: multipart/x-mixed-replace; boundary=" + boundary + "\r\n\r\n";
  client.print(response);

  // 스트리밍 루프
  while (true) {
    fb = esp_camera_fb_get();
    if (!fb) {
      Serial.println("Camera capture failed");
      return;
    }

    response = "--" + boundary + "\r\n";
    response += "Content-Type: image/jpeg\r\n";
    response += "Content-Length: " + String(fb->len) + "\r\n\r\n";
    client.print(response);
    client.write((const uint8_t *)fb->buf, fb->len);
    client.print("\r\n");
    esp_camera_fb_return(fb);

    // 클라이언트 연결 해제시 메모리 누수 방지
    if (!client.connected()) {
      break;
    }
  }
}

// 서보모터 제어 함수
void handleServo() {
  if (!server.hasArg("plain")) {
    server.send(400, "application/json", "{\"error\":\"Body not received\"}");
    return;
  }

  String body = server.arg("plain");
  StaticJsonDocument<200> doc;
  DeserializationError error = deserializeJson(doc, body);

  if (error) {
    server.send(400, "application/json", "{\"error\":\"Invalid JSON format\"}");
    return;
  }

  int servoNum = doc["servoNum"];
  int angleIncrement = doc["angleIncrement"];

  if (servoNum == 1) {
    currentAngle1 += angleIncrement;
    currentAngle1 = constrain(currentAngle1, 0, 180);
    servo1.write(currentAngle1);
  } else if (servoNum == 2) {
    currentAngle2 += angleIncrement;
    currentAngle2 = constrain(currentAngle2, 0, 180);
    servo2.write(currentAngle2);
  }

  server.send(200, "application/json", "{\"message\":\"Servo moved\"}");
}

// DC모터 제어 함수
void handleMotor() {
  if (!server.hasArg("plain")) {
    server.send(400, "application/json", "{\"error\":\"Body not received\"}");
    return;
  }

  String body = server.arg("plain");
  StaticJsonDocument<200> doc;
  DeserializationError error = deserializeJson(doc, body);

  if (error) {
    server.send(400, "application/json", "{\"error\":\"Invalid JSON format\"}");
    return;
  }

  int motorState = doc["motorState"];

  switch (motorState) {
    case 0: // 정지
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      break;
    case 1: // 전진
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, motorSpeed);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, motorSpeed);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      break;
    case 2: // 후진
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, motorSpeed);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, motorSpeed);
      break;
    case 3: // 좌회전
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, motorSpeed);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      break;
    case 4: // 우회전
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, motorSpeed);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      break;
    default: // 정지
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(LEFT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_FORWARD, 0);
      ledcWrite(RIGHT_MOTOR_PWM_CHANNEL_BACKWARD, 0);
      break;
  }

  server.send(200, "application/json", "{\"message\":\"Motor state updated\"}");
}

// 릴레이 제어 함수
void handleRelay() {
  if (!server.hasArg("plain")) {
    server.send(400, "application/json", "{\"error\":\"Body not received\"}");
    return;
  }

  String body = server.arg("plain");
  StaticJsonDocument<200> doc;
  DeserializationError error = deserializeJson(doc, body);

  if (error) {
    server.send(400, "application/json", "{\"error\":\"Invalid JSON format\"}");
    return;
  }

  int relayState = doc["relayState"];
  digitalWrite(RELAY_PIN, relayState == 1 ? HIGH : LOW);
  server.send(200, "application/json", "{\"message\":\"Relay state updated\"}");
}

// 캡쳐 함수
void handleCapture() {
  camera_fb_t * fb = esp_camera_fb_get();
  if (!fb) {
    server.send(500, "text/plain", "Camera capture failed");
    return;
  }

  String base64Image = base64::encode(fb->buf, fb->len);
  esp_camera_fb_return(fb);

  HTTPClient http;
  String url = String(SERVER_URL) + image_upload_url;
  http.begin(url);
  http.addHeader("Content-Type", "application/json");

  String macAddress = WiFi.macAddress();

  String json = "{\"image\":\"" + base64Image + "\",\"macAddress\":\"" + macAddress + "\"}";
  int httpResponseCode = http.POST(json);

  if (httpResponseCode > 0) {
    String response = http.getString();
    server.send(200, "text/plain", response);
  } else {
    server.send(500, "text/plain", "Image upload failed");
  }

  http.end();
}

// 디바이스 정보 전송 함수
void sendDeviceInfo() {
  HTTPClient http;
  String url = String(SERVER_URL) + register_url;
  http.begin(url);
  http.addHeader("Content-Type", "application/json");

  String macAddress = WiFi.macAddress();
  String ipAddress = WiFi.localIP().toString();
  String deviceName = "MyRCCar"; // 안드로이드 앱에서 이름 설정 가능

  String json = "{\"macAddress\":\"" + macAddress + "\",\"deviceIp\":\"" + ipAddress + "\",\"deviceName\":\"" + deviceName + "\"}";

  int httpResponseCode = http.POST(json);

  if (httpResponseCode > 0) {
    String response = http.getString();
    Serial.println(httpResponseCode);
    Serial.println(response);
  } else {
    Serial.print("Error on sending POST: ");
    Serial.println(httpResponseCode);
    Serial.println(http.errorToString(httpResponseCode).c_str());
  }

  http.end();
}

// 엔드포인트 등록
void startServer() {
  server.on(stream_url, HTTP_GET, []() {
    xTaskCreatePinnedToCore(handleStream, "handleStream", 8192, &server, 1, NULL, 0);
  }); // 스트리밍은 FreeRTOS 멀티태스킹으로 실행하여 스트리밍중에도 다른 HTTP 요청을 처리할 수 있도록 구현
  server.on(servo_url, HTTP_POST, handleServo); // 서보모터 엔드포인트 등록
  server.on(motor_url, HTTP_POST, handleMotor); // DC모터 엔드포인트 등록
  server.on(relay_url, HTTP_POST, handleRelay); // 릴레이 엔드포인트 등록
  server.on(capture_url, HTTP_GET, handleCapture); // 캡쳐함수 엔드포인트 등록
  server.begin();
  Serial.println("HTTP server started");
}
```
| 기능 | HTTP 요청 | 엔드포인트 | 요청 데이터 | 응답 데이터 | 비고 |
|-------|-------|-------|-------|-------|-------|
| 스트리밍 | GET | /stream |  | MJPEG 스트리밍 | |
| 캡쳐 | GET | /capture |  | "이미지 저장 완료: 경로" 또는 "Camera capture failed" | 이미지를 캡쳐하여 base64로 변환하여 MAC주소와 함께 스프링부트서버에 전송 |
| 서보모터 제어 | POST | /servo | {"servoNum": 1, "angleIncrement": 10} | {"message": "Servo moved"} | (제어할 서보모터 번호, 1 : 카메라 좌우 회전, 2 : 카메라 상하 회전 ) (서보모터 각도 변화량) |
| DC모터 제어 | POST | /motor | {"motorState": 1} | {"message": "Motor state updated"} | 0:정지, 1:전진, 2:후진, 3:좌회전, 4:우회전 |
| 릴레이 제어 | POST | /relay | {"relayState": 1} | {"message": "Relay state updated"} | 0:Off, 1:On |


:bulb: **2. ESP32-CAM에 코드를 업로드한 후, 배터리를 연결하여 전체 회로 점검 및 작동테스트를 합니다.** <br>
테스트는 Talend API Tester를 이용하여 ESP32-CAM의 ip주소에 POST 요청을 보내고 각 부품들이 제대로 동작하는지 확인합니다. <br>

1. 카메라 웹 스트리밍 테스트 <br>
![cameratest](https://github.com/user-attachments/assets/d7080d2d-62c9-4913-bc05-ae7e466bfb21) <br><br>
2. 서보모터 제어 테스트 <br>
![servotest](https://github.com/user-attachments/assets/59318f70-2557-4b74-91ca-4afb1a7bbb75) <br><br>
3. DC모터 제어 테스트 <br>
![dcmotortest](https://github.com/user-attachments/assets/92a1e8cf-5550-4d74-b90c-69e64474db42) <br><br>
4. 릴레이 제어 테스트 <br>
![relaytest](https://github.com/user-attachments/assets/78b181ca-0f65-4672-826d-97b245f1c102) <br><br>

:nut_and_bolt: **3. 조립하기** <br>
1. 프레임을 설계하여 알루미늄 가공업체에 제작을 의뢰합니다. <br>
![프레임2](https://github.com/user-attachments/assets/075c931a-939c-4e1c-a381-135b272d2536) <br><br>
2. 2대 조립하기(약 4시간 소요) <br>
![조립중 및 2대조립완료](https://github.com/user-attachments/assets/2443ac34-37a4-4cfe-bd1c-d2d19f63c9cc) <br><br>
3. 조립 완료 후 잘 움직이는지 테스트합니다. <br>
Talend API Tester로 ESP32-CAM ip주소에 POST 요청을 보내고 각 부품들이 제대로 움직이는지 확인합니다. <br>
(왼쪽 : 핸드폰으로 촬영한 화면, 오른쪽 : RC카 카메라 화면) <br>
![조립테스트 영상 2개 합친것 용량압축](https://github.com/user-attachments/assets/b7a05f9c-ae1a-4dd6-bbd7-6c5992a32da3) <br>
<br><br>

***
### 3. 백엔드 개발
:seedling: **1. 프로젝트 생성하기** <br>
start.spring.io에서 스프링부트 프로젝트를 생성합니다. <br>
Project : `Gradle - Groovy` <br>
Language : `Java` <br>
Spring Boot : `3.4.3` <br>
Name : `SpringBootApi` <br>
Packaging : `Jar` <br>
Java : `23` <br>
Dependencies : `Spring Data JPA`, `Spring Web`, `Lombok`, `Mustache`, `H2 Database`, `PostgreSQL Driver` <br><br>

:blue_book: **2. Entity 정의하기** <br>
DB에 RC카 정보를 저장하기 위한 Entity를 정의합니다. <br>
#### ESP32-CAM Device Table
| id | macAddress | deviceName | deviceIp |
|-------|-------|-------|-------|
| 1 | MACADDRESS1 | RC CAR 1 | 192.168.0.0 |
| 2 | MACADDRESS2 | RC CAR 2 | 192.168.0.2 |

#### ESP32-CAM Image Table
| id | imagePath | createdAt | esp32cam device id |
|-------|-------|-------|-------|
| 1 | {image.storage.path} | 2025.02.03.12.50 | 1 |
| 2 | {image.storage.path} | 2025.02.03.12.51 | 1 |
| 3 | {image.storage.path} | 2025.02.03.12.56 | 2 |
| 4 | {image.storage.path} | 2025.02.03.12.57 | 2 |
| 5 | {image.storage.path} | 2025.02.03.12.58 | 2 |

#### Esp32CamInfo.java
```Java
@Builder
@ToString
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name="esp32_cam_device")
class Esp32CamDevice {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id; // DB 저장용 id (자동으로 증가)
    @Column(unique = true)
    private String macAddress; // ESP32-CAM의 고유 ID (MAC 주소)
    @Column
    private String deviceName; // 안드로이드 앱에서 설정한 이름
    @Column(nullable = false)
    private String deviceIp; // ESP32-CAM 부팅 시 IP주소 (접근하기 위한 주소)
}

@Builder
@ToString
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name = "esp32_cam_image")
class Esp32CamImage { // ESP32-CAM이 찍은 사진
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id; // 기본키
    @Column
    private String imagePath; // 사진 데이터(로컬 경로로 저장)
    @Column
    private LocalDateTime createdAt;  // 사진 촬영 시간
    @ManyToOne // 다대일 관계, RC카 1대가 여러 장의 사진을 찍어서 저장
    @JoinColumn(name="esp32_cam_device_id")
    private Esp32CamDevice esp32CamDevice; // 외래키 생성, Esp32CamDevice 기본키와 맵핑

    @PrePersist
    protected void onCreate() {
        this.createdAt = LocalDateTime.now();  // 자동으로 현재 시간 저장
    }
}
```
<br>

:books: **3. Repository 인터페이스 구현하기** <br>
Entity를 저장할 Repository 인터페이스를 생성합니다. <br>
Esp32CamDeviceRepo에는 MAC 주소로 ESP32-CAM 객체를 조회하는 쿼리 메서드를 추가합니다. <br>
Esp32CamImageRepo에는 몇 개의 이미지가 저장되어 있는지 카운트하는 메서드를 추가합니다. <br>
#### Esp32CamInfo.java
```Java
interface Esp32CamDeviceRepo extends JpaRepository<Esp32CamDevice, Long> {
    Optional<Esp32CamDevice> findByMacAddress(String macAddress);
}

interface Esp32CamImageRepo extends JpaRepository<Esp32CamImage, Long> {
    long countByEsp32CamDevice(Esp32CamDevice device);
}
```
<br>

:airplane: **4. DTO 구현하기** <br>
데이터를 주고받기 위한 DTO를 구현합니다. <br>
#### Esp32CamInfo.java
```Java
@AllArgsConstructor
@Getter
@Builder
@ToString
class Esp32CamDeviceDTO {
    private Long id;
    private String macAddress;
    private String deviceName;
    private String deviceIp;
}

@AllArgsConstructor
@Getter
@Builder
@ToString
class Esp32CamImageDTO {
    private Long id;
    private String imagePath;
    private LocalDateTime createdAt;
    private String deviceName;
    private Long deviceId;
}
```
<br>

:arrow_forward: **5. Service 구현하기** <br>
서비스 클래스를 생성하여 RC카 등록, 조회, 정보 수정, 특정 RC카 삭제, 서보모터, DC모터, 릴레이 제어, 사진 찍기, 전체 이미지 조회하기 기능을 처리하는 로직을 구현합니다. <br>

#### RCCarService.java
```Java
@Service
public class RCCarService {

    private final Esp32CamDeviceRepo deviceRepo;
    private final Esp32CamImageRepo imageRepo;

    @Value("${image.storage.path}")  // application.properties에 이미지 저장 경로 설정
    private String storagePath;

    public RCCarService(Esp32CamDeviceRepo deviceRepo, Esp32CamImageRepo imageRepo) {
        this.deviceRepo = deviceRepo;
        this.imageRepo = imageRepo;
    }

    // id로 장치 조회하기
    public Esp32CamDevice getDeviceById(Long id) {
        return deviceRepo.findById(id)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 장치가 없습니다."));
    }

    // RC카 등록하기 (IP, MAC 주소 저장)
    @Transactional
    public Esp32CamDevice registerDevice(Esp32CamDeviceDTO deviceDTO) {
        Esp32CamDevice device = deviceRepo.findByMacAddress(deviceDTO.getMacAddress())
                .orElse(Esp32CamDevice.builder()
                        .macAddress(deviceDTO.getMacAddress())
                        .deviceName(deviceDTO.getDeviceName())
                        .build());

        // IP 주소 업데이트 (항상 최신으로 갱신)
        device.setDeviceIp(deviceDTO.getDeviceIp());

        return deviceRepo.save(device);
    }

    // 전체 RC카 조회하기
    public List<Esp32CamDeviceDTO> getAllDevices() {
        return deviceRepo.findAll().stream()
                .map(device -> new Esp32CamDeviceDTO(device.getId(), device.getMacAddress(), device.getDeviceIp(), device.getDeviceName()))
                .collect(Collectors.toList());
    }


    // RC카 수정하기
    @Transactional
    public Esp32CamDevice updateDevice(Long id, Esp32CamDeviceDTO deviceDTO) {
        // 해당 ID의 장치가 존재하는지 확인
        Esp32CamDevice device = deviceRepo.findById(id)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 장치가 없습니다."));

        // 필드 업데이트
        device.setMacAddress(deviceDTO.getMacAddress());
        device.setDeviceIp(deviceDTO.getDeviceIp());
        device.setDeviceName(deviceDTO.getDeviceName());

        // 수정된 장치 저장
        return deviceRepo.save(device);
    }

    // RC카 삭제하기
    @Transactional
    public void deleteDevice(Long deviceId) {
        Esp32CamDevice device = deviceRepo.findById(deviceId)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 장치가 없습니다."));

        deviceRepo.delete(device);  // 장치 삭제
    }

    // 서보모터 제어
    public String controlServo(String macAddress, int servoNum, int angleIncrement) {
        // MAC 주소로 ESP32 조회
        Esp32CamDevice device = deviceRepo.findByMacAddress(macAddress)
                .orElseThrow(() -> new IllegalArgumentException("장치를 찾을 수 없습니다: " + macAddress));

        // ESP32-CAM IP 주소 가져오기
        String esp32Ip = device.getDeviceIp();

        // ESP32에 POST 요청 보내기
        try {
            String url = "http://" + esp32Ip + "/servo"; // ESP32 엔드포인트
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(url))
                    .header("Content-Type", "application/json")
                    .POST(HttpRequest.BodyPublishers.ofString("{\"servoNum\":" + servoNum + "," + "\"angleIncrement\":" + angleIncrement + "}"))
                    .build();

            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            if (response.statusCode() == 200) {
                return "ESP32 서보모터 제어 성공: " + response.body();
            } else {
                return "ESP32 서보모터 제어 실패: " + response.statusCode();
            }
        } catch (IOException | InterruptedException e) {
            throw new RuntimeException("ESP32에 요청 실패", e);
        }
    }

    // DC모터 제어
    public String controlMotor(int command) {
        String action;
        switch (command) {
            case 0 -> action = "정지";
            case 1 -> action = "전진";
            case 2 -> action = "후진";
            case 3 -> action = "좌회전";
            case 4 -> action = "우회전";
            default -> throw new IllegalArgumentException("잘못된 명령어");
        }
        return "RC카 " + action;
    }

    // 릴레이 제어
    public String controlRelay(String macAddress, int relayState) {
        // MAC 주소로 ESP32 조회
        Esp32CamDevice device = deviceRepo.findByMacAddress(macAddress)
                .orElseThrow(() -> new IllegalArgumentException("해당 MAC 주소의 장치가 없습니다: " + macAddress));

        // ESP32-CAM IP 주소 가져오기
        String deviceIp = device.getDeviceIp();

        // ESP32에 POST 요청 보내기
        try {
            String url = "http://" + deviceIp + "/relay"; // ESP32 엔드포인트
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(url))
                    .header("Content-Type", "application/json")
                    .POST(HttpRequest.BodyPublishers.ofString("{\"relayState\":" + relayState + "}"))
                    .build();

            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            if (response.statusCode() == 200) {
                return "ESP32 릴레이 제어 성공: " + response.body();
            } else {
                return "ESP32 릴레이 제어 실패: " + response.statusCode();
            }
        } catch (IOException | InterruptedException e) {
            throw new RuntimeException("ESP32에 요청 실패", e);
        }
    }

    // RC카가 찍은 사진 저장하기 (MAC 주소 기반)
    @Transactional
    public String saveImage(String macAddress, String base64Image) {
        // MAC 주소로 RC카 조회
        Esp32CamDevice device = deviceRepo.findByMacAddress(macAddress)
                .orElseThrow(() -> new IllegalArgumentException("해당 MAC 주소의 장치가 없습니다: " + macAddress));

        // 기존 저장된 이미지 개수 조회 (일련번호 부여)
        long imageCount = imageRepo.countByEsp32CamDevice(device);

        // 파일명: "1_MyRCCar_1.jpg" (ID + 이름 + 순번)
        String fileName = String.format("%d_%s_%d.jpg", device.getId(), device.getDeviceName(), imageCount + 1);
        String filePath = Paths.get(storagePath, fileName).toString();

        // Base64 디코딩 후 이미지 저장
        try {
            byte[] imageBytes = Base64.getDecoder().decode(base64Image);
            Files.write(Paths.get(filePath), imageBytes);
        } catch (IOException e) {
            throw new RuntimeException("이미지 저장 실패", e);
        }

        // 이미지 정보 DB에 저장
        Esp32CamImage image = Esp32CamImage.builder()
                .imagePath(filePath)
                .esp32CamDevice(device)
                .build();

        imageRepo.save(image);
        return filePath;
    }

    // 모든 이미지 조회
    public List<Map<String, String>> getAllImages() {
        List<Esp32CamImage> images = imageRepo.findAll();

        return images.stream().map(image -> {
            Map<String, String> imageData = new HashMap<>();
            imageData.put("id", String.valueOf(image.getId()));
            imageData.put("imagePath", image.getImagePath());
            imageData.put("deviceName", image.getEsp32CamDevice().getDeviceName());
            return imageData;
        }).collect(Collectors.toList());
    }
}
```

:twisted_rightwards_arrows: **6. RestController 구현하기** <br>
컨트롤러를 구현합니다. <br>

| 기능 | HTTP 요청 | 엔드포인트 | 요청 데이터 | 응답 데이터 |
|-------|-------|-------|-------|-------|
| id로 장치 조회하기 | GET | /api/rc/device/{id} | | Esp32CamDevice (JSON) |
| 전체 RC카 조회하기 | GET | /api/rc/devices | | `List<Esp32CamDeviceDTO>` |
| RC카 등록하기 | POST | /api/rc/register | {"macAddress": "xx:xx:xx:xx:xx:xx", "deviceIp": "192.168.x.x", "deviceName": "MyCar"} | Esp32CamDevice (JSON) |
| RC카 수정하기 | PATCH | /api/rc/device/{id} | {"macAddress": "xx:xx:xx:xx:xx:xx", "deviceIp": "192.168.x.x", "deviceName": "NewName"} | Esp32CamDevice (JSON) |
| RC카 삭제하기 | DELETE | /api/rc/device/{id} | | {"message": "장치 삭제 완료"} |
| 서보모터 제어 | POST | /api/rc/servo | {"macAddress": "xx:xx:xx:xx:xx:xx", "servoNum": 1, "angleIncrement": -10} | {"message": "Servo moved"} |
| DC모터 제어 | POST | /api/rc/motor | {"motorState": 1} | "RC카 전진" |
| 릴레이 제어 | POST | /api/rc/relay | {"macAddress": "xx:xx:xx:xx:xx:xx", "relayState": 1} | {"message": "Relay ON"} |
| 이미지 저장하기 | POST | /api/rc/image/upload | {"macAddress": "xx:xx:xx:xx:xx:xx", "image": "Base64String"} | {"message": "이미지 저장 완료", "path": "/path/to/image.jpg"} |
| 이미지 가져오기 | GET | /api/rc/image | | {"id": "1", "imagePath": "경로", "deviceName": "MyCar"} |

### RCCarController.java
```Java
@RestController
@RequestMapping("/api/rc")
public class RCCarController {

    private final RCCarService rcCarService;

    public RCCarController(RCCarService rcCarService) {
        this.rcCarService = rcCarService;
    }

    // id로 장치 조회하기
    @GetMapping("/device/{id}")
    public ResponseEntity<Esp32CamDevice> getDevice(@PathVariable Long id) {
        Esp32CamDevice device = rcCarService.getDeviceById(id);
        return ResponseEntity.ok(device);
    }

    // RC카 등록하기
    @PostMapping("/register")
    public ResponseEntity<?> registerDevice(@RequestBody Esp32CamDeviceDTO deviceDTO) {
        if (deviceDTO.getMacAddress() == null || deviceDTO.getDeviceIp() == null || deviceDTO.getDeviceName() == null) {
            return ResponseEntity.badRequest().body("macAddress, deviceIp, deviceName 필드가 필요합니다.");
        }

        Esp32CamDevice device = rcCarService.registerDevice(deviceDTO);
        return ResponseEntity.ok(device);
    }

    // 전체 RC카 조회하기
    @GetMapping("/devices")
    public ResponseEntity<List<Esp32CamDeviceDTO>> getAllDevices() {
        List<Esp32CamDeviceDTO> devices = rcCarService.getAllDevices();
        return ResponseEntity.ok(devices);
    }

    // RC카 수정하기
    @PatchMapping("/device/{id}")
    public ResponseEntity<?> updateDevice(@PathVariable Long id, @RequestBody Esp32CamDeviceDTO deviceDTO) {
        if (deviceDTO.getMacAddress() == null || deviceDTO.getDeviceIp() == null || deviceDTO.getDeviceName() == null) {
            return ResponseEntity.badRequest().body("macAddress, deviceIp, deviceName 필드가 필요합니다.");
        }

        Esp32CamDevice updatedDevice = rcCarService.updateDevice(id, deviceDTO);
        return ResponseEntity.ok(updatedDevice);
    }

    // RC카 삭제하기
    @DeleteMapping("/device/{id}")
    public ResponseEntity<Map<String, String>> deleteDevice(@PathVariable Long id) {
        try {
            rcCarService.deleteDevice(id);
            return ResponseEntity.ok(Map.of("message", "장치 삭제 완료"));
        } catch (IllegalArgumentException e) {
            return ResponseEntity.badRequest()
                    .body(Map.of("error", "해당 ID의 장치가 없습니다."));
        }
    }

    // 서보모터 제어
    @PostMapping("/servo")
    public ResponseEntity<String> controlServo(@RequestBody Map<String, Object> request) {
        String macAddress = (String) request.get("macAddress");
        Integer servoNum = (Integer) request.get("servoNum");
        Integer angleIncrement = (Integer) request.get("angleIncrement");

        // 서비스 호출
        String result = rcCarService.controlServo(macAddress, servoNum, angleIncrement);
        return ResponseEntity.ok(result);
    }

    // DC모터 제어
    @PostMapping("/motor")
    public ResponseEntity<String> controlMotor(@RequestBody Map<String, Integer> request) {
        int command = request.get("motorState");
        return ResponseEntity.ok(rcCarService.controlMotor(command));
    }

    // 릴레이 제어
    @PostMapping("/relay")
    public ResponseEntity<String> controlRelay(@RequestBody Map<String, Object> request) {
        String macAddress = (String) request.get("macAddress");
        Integer relayState = (Integer) request.get("relayState");

        // 서비스 호출
        String result = rcCarService.controlRelay(macAddress, relayState);
        return ResponseEntity.ok(result);
    }

    // 이미지 저장하기
    @PostMapping("/image/upload")
    public ResponseEntity<Map<String, String>> uploadImage(@RequestBody Map<String, String> requestData) {
        String macAddress = requestData.get("macAddress");
        String base64Image = requestData.get("image");

        if (macAddress == null || base64Image == null) {
            return ResponseEntity.badRequest()
                    .body(Map.of("error", "MAC 주소 또는 이미지 데이터가 누락되었습니다."));
        }

        try {
            String savedFilePath = rcCarService.saveImage(macAddress, base64Image);
            return ResponseEntity.ok(Map.of("message", "이미지 저장 완료", "path", savedFilePath));
        } catch (Exception e) {
            return ResponseEntity.status(500)
                    .body(Map.of("error", "이미지 저장 중 문제가 발생했습니다.", "details", e.getMessage()));
        }
    }

    // 모든 이미지 가져오기
    @GetMapping("/image")
    public ResponseEntity<List<Map<String, String>>> getAllImages() {
        List<Map<String, String>> images = rcCarService.getAllImages();
        return ResponseEntity.ok(images);
    }
}
```
<br>

:mag_right: **7. H2 DB 연동 및 CRUD 동작 확인하기** <br>
`application.properties`에 H2 DB 관련 설정들을 추가하고 CRUD 기능을 테스트합니다. <br>
```Java
spring.datasource.url=jdbc:h2:mem:testdb // h2 db 주소 고정
spring.h2.console.enabled=true // h2콘솔 활성화
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```
Talend API Tester로 컨트롤러에서 구현한 엔드포인트에 HTTP 요청들을 보내고, H2 Console로 제대로 처리되었는지 확인합니다. <br>
![h2db 테스트](https://github.com/user-attachments/assets/028480b7-872e-4a78-906b-17d1c2b7e211) <br>

:floppy_disk: **8. PostgreSQL DB 전환** <br>
PostgreSQL DB에서 프로젝트를 생성한 후, `application.properties`에 PostgreSQL 설정을 추가하여 H2 DB 대신 PostgreSQL DB를 연결합니다. <br>
```Java
spring.datasource.url=jdbc:postgresql://localhost:5432/RCCamBot
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.username=postgres
spring.datasource.password=p
spring.datasource.data=classpath:/data.sql
spring.datasource.initialization-mode=always
spring.jpa.hibernate.ddl-auto=update
```
![postgresqldb 전환](https://github.com/user-attachments/assets/f393c548-3284-43e2-8f41-1e11d0938d16) <br>

:satellite: **9. ESP32-CAM 통신 테스트** <br>
RC카 전원 On시(ESP32-CAM 부팅시) DB에 디바이스 정보가 등록되는지 확인합니다. <br>
`MAC주소는 고유하기 때문에 이후에 재부팅시 라우터에서 다른 ip주소를 할당받아도 ip주소만 새로 갱신됩니다.` <br>
`RC카 이름은 안드로이드 앱에서 사용자가 설정하여 저장할 수 있습니다.` <br>
![전원 온시 디비에 디바이스정보 등록 용량압축](https://github.com/user-attachments/assets/e8038c4f-2f74-4604-9466-a6a1b1d82846) <br><br>
RC카 2대 모두 등록한 후 사진을 찍고 로컬에 잘 저장되는지 확인합니다. <br>
![2대 등록](https://github.com/user-attachments/assets/1506fccd-5dc2-4464-8342-f3b601c9c0a5) <br>
![2대 사진찍기 테스트](https://github.com/user-attachments/assets/e7f08fa1-cac4-476a-a995-97f57c8115c5) <br>
![이미지저장테스트](https://github.com/user-attachments/assets/d670dcb8-43a1-4301-bf6b-26d10e787ef4) <br><br>


***
### 4. 안드로이드앱 개발
:pager: **1. UI 구성하기** <br>
### activity_main.xml
![메인화면](https://github.com/user-attachments/assets/a7792ca7-bd3b-4d7e-9aad-02559bf2cd5c) <br>
메인화면은 RC카의 영상을 스트리밍하는 웹뷰와, RC카의 이동 제어 및 카메라 방향 제어, 라이트 On/Off, 사진 촬영 버튼과 찍은 사진들을 조회하는 버튼으로 구성합니다. <br><br>

### activity_rccar.xml
![RC카 설정화면](https://github.com/user-attachments/assets/db5bf7f9-dd9c-428f-8bf4-6453d42642e0) <br>
`RC카 설정` 버튼을 누르면 RC카 리스트가 나오도록 합니다. <br>
이 화면에서는 `RC카 찾기` 버튼을 눌러 DB에 등록되어 있는 RC카 정보를 가져오고, RC카에 ID를 설정하여 저장하고 관리할 수 있습니다. <br><br>

### detected_rccar_item.xml
![감지된 RC카 리스트](https://github.com/user-attachments/assets/8962886b-1c21-4340-b50f-24221f544b6e) <br>
`등록 가능한 RC카` 리스트뷰에는 이 아이템이 모델링되며, `RC카 찾기` 버튼을 눌렀을 때 DB에 저장되어 있는 RC카가 나옵니다. <br><br>

### rccar_item.xml
![저장된 RC카 리스트](https://github.com/user-attachments/assets/8eaa5115-ed7a-4f8c-9d13-8e93379b5bc6) <br>
`저장된 RC카` 리스트뷰에는 이 아이템이 모델링되며, `등록 가능한 RC카` 리스트에서 저장한 RC카가 등록됩니다. <br><br>
<br>

intent를 이용하여 메인화면에서 `RC카 찾기`버튼을 누르면 RC카 설정 창이 뜨도록 합니다. <br>
### MainActivity.java
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        // RC카 설정 버튼
        Button buttonRCCarSetting = findViewById(R.id.buttonRCCarSetting);
        buttonRCCarSetting.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                FragmentManager fm = getSupportFragmentManager();
                RCCarSettingDialog dialog = new RCCarSettingDialog();
                dialog.show(fm, "activity_rccar");
            }
        });
    }
}
```
<br>

리스트뷰와 리스트아이템을 연결하기 위한 어댑터들을 작성하고, 더미데이터를 추가하여 제대로 모델링되는지 확인합니다. <br>
### RCCarSettingDialog.java
```java
public class RCCarSettingDialog extends AppCompatDialogFragment {
    @NonNull
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Dialog dialog = new Dialog(requireContext());

        dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        dialog.setContentView(R.layout.activity_rccar);

        if (dialog.getWindow() != null) {
            dialog.getWindow().setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.MATCH_PARENT);
        }

        ListView listViewSavedRCCar = dialog.findViewById(R.id.listViewSavedRCCar);
        ListView listViewDetectedRCCar = dialog.findViewById(R.id.listViewDetectedRCCar);

        // 리스트 아이템 데이터 추가
        List<String> itemList = new ArrayList<>();
        itemList.add("RC CAR NAME");
        itemList.add("RC CAR NAME");
        itemList.add("RC CAR NAME");
        // 리스트 아이템 데이터 추가
        List<String> itemList2 = new ArrayList<>();
        itemList2.add("RC CAR ADDRESS");
        itemList2.add("RC CAR ADDRESS");
        itemList2.add("RC CAR ADDRESS");

        // ArrayAdapter를 사용하여 리스트뷰와 연결
        SavedRCCarAdapter adapter = new SavedRCCarAdapter(requireContext(), itemList);
        listViewSavedRCCar.setAdapter(adapter);
        DetectedRCCarAdapter adapter2 = new DetectedRCCarAdapter(requireContext(), itemList2);
        listViewDetectedRCCar.setAdapter(adapter2);

        return dialog;
    }
}
```

### DetectedRCCarAdapter.java
```java
public class DetectedRCCarAdapter extends ArrayAdapter<String> {

    public DetectedRCCarAdapter(Context context, List<String> items) {
        super(context, 0, items);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(getContext()).inflate(R.layout.detected_rccar_item, parent, false);
        }

        TextView textViewRCCarAddress = convertView.findViewById(R.id.textViewRCCarAddress);

        // 현재 아이템 값 가져오기
        String item = getItem(position);
        textViewRCCarAddress.setText(item);

        return convertView;
    }
}
```

### SavedRCCarAdapter.java
```java
public class SavedRCCarAdapter extends ArrayAdapter<String> {

    public SavedRCCarAdapter(Context context, List<String> items) {
        super(context, 0, items);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(getContext()).inflate(R.layout.rccar_item, parent, false);
        }

        TextView textViewRCCarName = convertView.findViewById(R.id.textViewRCCarName);

        // 현재 아이템 값 가져오기
        String item = getItem(position);
        textViewRCCarName.setText(item);

        return convertView;
    }
}
```
![UI디자인](https://github.com/user-attachments/assets/d370929c-353d-4b9c-91e4-26042203bd78) <br><br>


:ticket: **2. Retrofit 설정하기** <br>
스프링부트 서버와 통신하기 위해 Retrofit을 사용합니다. <br><br>

Retrofit을 사용하기 위해서 `build.gradle.kts`의 dependencies에 레트로핏 의존성을 추가하고 설치합니다. <br>
### build.gradle.kts
```java
implementation("com.squareup.retrofit2:retrofit:2.9.0")
implementation("com.squareup.retrofit2:converter-gson:2.9.0")
```

<br>

`AndroidManifest.xml`에 설정을 추가합니다. <br>
### AndroidManifest.xml
```java
<uses-permission android:name="android.permission.INTERNET" />

<application
    ...
    android:usesCleartextTraffic="true"
    ... >
```

`<uses-permission android:name="android.permission.INTERNET" />` : 앱이 인터넷에 연결할 수 있게 허용하는 권한으로 GET, POST 등의 API 요청을 보내기 위해 필요합니다. <br>
`android:usesCleartextTraffic="true"` : 안드로이드 9.0 이상에서는 기본적으로 HTTPS만 허용하고 HTTP를 차단하는데, 이 속성을 true로 설정하면 HTTP 요청을 보낼 수 있습니다. <br><br>

:artificial_satellite: **3. Retrofit API Service와 Retrofit Client 작성하기** <br>
스프링부트와 데이터를 주고받기 위한 DTO를 작성합니다. <br>
Retrofit에는 Lombok이 없으므로 생성자와 Getter, Setter 등 필요한 메서드를 직접 작성합니다. <br>
### Esp32CamDeviceDTO.java
```java
public class Esp32CamDeviceDTO {

    private Long id;
    private String macAddress;
    private String deviceIp;
    private String deviceName;
    private boolean isSaved;

    // 기본 생성자
    public Esp32CamDeviceDTO() {}

    // 매개변수를 받는 생성자
    public Esp32CamDeviceDTO(Long id, String macAddress, String deviceIp, String deviceName, boolean isSaved) {
        this.id = id;
        this.macAddress = macAddress;
        this.deviceIp = deviceIp;
        this.deviceName = deviceName;
        this.isSaved = isSaved;
    }

    // @Getter, @Setter
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getMacAddress() {
        return macAddress;
    }

    public void setMacAddress(String macAddress) {
        this.macAddress = macAddress;
    }

    public String getDeviceIp() {
        return deviceIp;
    }

    public void setDeviceIp(String deviceIp) {
        this.deviceIp = deviceIp;
    }

    public String getDeviceName() {
        return deviceName;
    }

    public void setDeviceName(String deviceName) {
        this.deviceName = deviceName;
    }

    public boolean isSaved() {
        return isSaved;
    }

    public void setSaved(boolean saved) {
        isSaved = saved;
    }

    // @toString
    @Override
    public String toString() {
        return "Esp32CamDeviceDTO{" +
                "id=" + id +
                ", macAddress='" + macAddress + '\'' +
                ", deviceIp='" + deviceIp + '\'' +
                ", deviceName='" + deviceName + '\'' +
                '}';
    }
}
```

<br>

스프링부트의 컨트롤러에 대응되는 레트로핏 서비스 인터페이스를 작성합니다. <br>
### RCCarApiService.java
```java
public interface RCCarApiService {

    // 특정 ID의 RC카 조회하기
    @GET("/api/rc/device/{id}")
    Call<Esp32CamDeviceDTO> getDeviceById(@Path("id") Long id);

    // 모든 RC카 조회하기
    @GET("/api/rc/devices")
    Call<List<Esp32CamDeviceDTO>> getAllDevices();

    // RC카 정보 수정하기
    @PATCH("/api/rc/device/{id}")
    Call<Esp32CamDeviceDTO> updateDevice(@Path("id") Long id, @Body Esp32CamDeviceDTO deviceDTO);

    // 서보모터 제어
    @POST("/api/rc/servo")
    Call<String> controlServo(@Body Map<String, Object> request);

    // DC모터 제어
    @POST("/api/rc/motor")
    Call<String> controlMotor(@Body Map<String, Integer> request);

    // 릴레이 제어
    @POST("/api/rc/relay")
    Call<String> controlRelay(@Body Map<String, Object> request);

    // 모든 이미지 조회하기
    @GET("/api/rc/image")
    Call<List<Map<String, String>>> getAllImages();
}
```

<br>

레트로핏 객체를 생성하는 레트로핏 클라이언트를 작성합니다. <br>
### RetrofitClient.java
```java
public class RetrofitClient {

    // 스프링부트 서버 URL
    private static final String BASE_URL = "**********";

    private static Retrofit retrofit = null;

    public static Retrofit getClient(String baseUrl) {
        if (retrofit == null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}

```

<br>

:repeat: **4. 스프링부트 서버 DB에서 RC카 정보 가져오고(GET) DB에 RC카 정보 저장하기(POST)** <br>

RC카 설정창에서 `RC카 찾기`버튼을 눌렀을 시 레트로핏을 이용하여 스프링부트 DB에서 RC카 정보를 가져옵니다. <br>
### RCCarSettingDialog.java
```java
public class RCCarSettingDialog extends AppCompatDialogFragment {

    private List<Esp32CamDeviceDTO> detectedList = new ArrayList<>();
    private RCCarApiService apiService;
    private DetectedRCCarAdapter detectedAdapter;

    @NonNull
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Dialog dialog = new Dialog(requireContext());

        dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        dialog.setContentView(R.layout.activity_rccar);

        if (dialog.getWindow() != null) {
            dialog.getWindow().setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.MATCH_PARENT);
        }

        // 저장된 RC카 리스트뷰
        ListView listViewSavedRCCar = dialog.findViewById(R.id.listViewSavedRCCar);

        // 등록 가능한 RC카 리스트뷰
        ListView listViewDetectedRCCar = dialog.findViewById(R.id.listViewDetectedRCCar);
        // Retrofit 클라이언트에서 API 서비스 가져오기
        apiService = RetrofitClient.getClient("").create(RCCarApiService.class);
        // 빈 리스트로 어댑터 초기화
        detectedAdapter = new DetectedRCCarAdapter(requireContext(), detectedList);
        listViewDetectedRCCar.setAdapter(detectedAdapter);

        // RC카 찾기 버튼
        Button buttonFindRCCar = dialog.findViewById(R.id.buttonFindRCCar);
        // RC카 찾기 버튼 클릭 이벤트
        buttonFindRCCar.setOnClickListener(v -> fetchDetectedRCCars());

        return dialog;
    }

    // Retrofit을 사용해 RC카 목록 가져오기
    private void fetchDetectedRCCars() {
        apiService.getAllDevices().enqueue(new Callback<List<Esp32CamDeviceDTO>>() {
            @Override
            public void onResponse(Call<List<Esp32CamDeviceDTO>> call, Response<List<Esp32CamDeviceDTO>> response) {
                if (response.isSuccessful() && response.body() != null) {
                    detectedList.clear();  // 기존 리스트 초기화
                    detectedList.addAll(response.body());  // 새로운 데이터 추가
                    detectedAdapter.notifyDataSetChanged();  // UI 갱신
                }
            }

            @Override
            public void onFailure(Call<List<Esp32CamDeviceDTO>> call, Throwable t) {
                Toast.makeText(requireContext(), "RC카 목록 불러오기 실패", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

<br>

`등록 가능한 RC카` 리스트뷰에 표시할 어댑터가 Esp32CamDeviceDTO 객체를 사용하도록 수정하고, DTO의 Getter 메서드를 통해 MAC주소를 가져와 getView()에서 표시합니다. <br>
### DetectedRCCarAdapter.java
```java
public class DetectedRCCarAdapter extends ArrayAdapter<Esp32CamDeviceDTO> {

    public DetectedRCCarAdapter(Context context, List<Esp32CamDeviceDTO> items) {
        super(context, 0, items);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(getContext()).inflate(R.layout.detected_rccar_item, parent, false);
        }

        // RC카의 MAC주소를 표시할 텍스트뷰
        TextView textViewRCCarAddress = convertView.findViewById(R.id.textViewRCCarAddress);

        Esp32CamDeviceDTO item = getItem(position);
        if (item != null) {
            textViewRCCarAddress.setText(item.getMacAddress());
        }

        return convertView;
    }
}
```

<br>


`RC카 찾기`버튼을 눌렀을 시 스프링부트 DB에 있는 RC카의 MAC주소가 불러와지는지 확인합니다. <br><br>

![데이터 가져오기](https://github.com/user-attachments/assets/a86ef1a4-1ebe-4b18-afd6-9bf92b547729) <br>
![데이터 가져오기 성공](https://github.com/user-attachments/assets/21983c43-8934-43ec-ab07-9d76fff65b7b) <br><br>

`등록 가능한 RC카` 리스트에 불러와진 아이템의 `저장` 버튼을 눌렀을 시, 다이얼로그 창을 띄워 사용자에게 RC카 이름을 입력받아서 이를 DB에 저장하고, 이 이름을 `저장된 RC카` 리스트에 MAC주소와 함께 표시합니다. <br>
`저장된 RC카` 리스트에 있는 RC카 정보들은 SharedPreferences 객체를 사용하여 로컬에 저장하여 앱을 종료해도 유지됩니다. <br>
### DetectedRCCarAdapter.java
```java
public class DetectedRCCarAdapter extends ArrayAdapter<Esp32CamDeviceDTO> {
    private Context context;
    private List<Esp32CamDeviceDTO> items;
    private SavedRCCarAdapter savedRCCarAdapter;
    private RCCarSettingDialog dialog; // UI 갱신을 위한 RCCarSettingDialog 참조

    public DetectedRCCarAdapter(Context context, List<Esp32CamDeviceDTO> items, RCCarSettingDialog dialog) {
        super(context, 0, items);
        this.context = context;
        this.dialog = dialog;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(getContext()).inflate(R.layout.detected_rccar_item, parent, false);
        }

        // RC카의 MAC주소를 표시할 텍스트뷰
        TextView textViewRCCarAddress = convertView.findViewById(R.id.textViewRCCarAddress);

        Esp32CamDeviceDTO item = getItem(position);
        if (item != null) {
            textViewRCCarAddress.setText(item.getMacAddress());
        }

        // RC카 저장 버튼
        Button buttonSave = convertView.findViewById(R.id.buttonSave);

        // RC카 저장 버튼 클릭 이벤트
        buttonSave.setOnClickListener(v -> {
            // 사용자 입력 다이얼로그
            AlertDialog.Builder builder = new AlertDialog.Builder(getContext());
            builder.setTitle("RC카 이름 설정");

            final EditText input = new EditText(getContext());
            input.setInputType(InputType.TYPE_CLASS_TEXT);
            input.setHint("이름을 입력하세요");
            builder.setView(input);

            Esp32CamDeviceDTO device = getItem(position);  // 현재 클릭된 RC카 정보

            builder.setPositiveButton("저장", (dialog, which) -> {
                String newDeviceName = input.getText().toString();
                if (newDeviceName.isEmpty()) {
                    Toast.makeText(getContext(), "이름을 입력해주세요", Toast.LENGTH_SHORT).show();
                } else {
                    // 이름이 입력된 경우 이름 업데이트를 위한 서버 요청
                    device.setDeviceName(newDeviceName);  // RC카 이름 업데이트
                    device.setSaved(true);  // isSaved 업데이트
                    updateDeviceInBackend(device);  // DB에 저장
                    saveRCCar(device);  // 앱 로컬에 저장

                    // RCCarSettingDialog에서 UI 갱신
                    this.dialog.updateSavedRCCarList();

                    // `등록 가능한 RC카` 리스트에서 해당 RC카 제거
                    remove(device);
                    notifyDataSetChanged();
                }
            });

            builder.setNegativeButton("취소", (dialog, which) -> dialog.cancel());
            builder.show();
        });

        return convertView;
    }

    // SharedPreferences에 저장
    private void saveRCCar(Esp32CamDeviceDTO device) {
        device.setSaved(true);

        // SharedPreferences에 저장된 RC카 리스트 추가
        SharedPreferences sharedPreferences = context.getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        String savedRCCarsJson = sharedPreferences.getString("saved_rccars", "[]");

        Type listType = new TypeToken<List<Esp32CamDeviceDTO>>(){}.getType();
        List<Esp32CamDeviceDTO> savedList = new Gson().fromJson(savedRCCarsJson, listType);

        // SharedPreferences에 중복된 MAC 주소가 있으면 최신 정보로 덮어쓰기
        boolean isUpdated = false;
        for (int i = 0; i < savedList.size(); i++) {
            if (savedList.get(i).getMacAddress().equals(device.getMacAddress())) {
                savedList.set(i, device); // 덮어쓰기
                isUpdated = true;
                break;
            }
        }

        // 중복이 없으면 새로 추가
        if (!isUpdated) {
            savedList.add(device);
        }

        // 저장된 RC카 리스트를 SharedPreferences에 다시 저장
        SharedPreferences.Editor editor = sharedPreferences.edit();
        String updatedSavedRCCarsJson = new Gson().toJson(savedList);
        editor.putString("saved_rccars", updatedSavedRCCarsJson);
        editor.apply();
    }

    // Retrofit을 사용하여 RC카 이름 업데이트
    private void updateDeviceInBackend(Esp32CamDeviceDTO device) {
        RCCarApiService apiService = RetrofitClient.getClient("").create(RCCarApiService.class);

        // device_name만 포함된 새로운 객체 생성
        Esp32CamDeviceDTO updateData = new Esp32CamDeviceDTO();
        updateData.setDeviceName(device.getDeviceName());  // device_name만 업데이트
        updateData.setMacAddress(device.getMacAddress()); // macAddress는 그대로 보내기
        updateData.setDeviceIp(device.getDeviceIp()); // deviceIp는 그대로 보내기

        // 서버에 PATCH 요청 보내기
        apiService.updateDevice(device.getId(), updateData).enqueue(new Callback<Esp32CamDeviceDTO>() {
            @Override
            public void onResponse(Call<Esp32CamDeviceDTO> call, Response<Esp32CamDeviceDTO> response) {
                if (response.isSuccessful()) {
                    // 서버에서 받은 갱신된 데이터
                    Esp32CamDeviceDTO updatedDevice = response.body();
                    if (updatedDevice != null) {
                        // 이름만 갱신된 항목으로 리스트 업데이트
                        updateListItem(updatedDevice);
                        Toast.makeText(getContext(), "이름 설정 성공!", Toast.LENGTH_SHORT).show();
                    }
                } else {
                    Toast.makeText(getContext(), "이름 설정 실패", Toast.LENGTH_SHORT).show();
                }
            }

            @Override
            public void onFailure(Call<Esp32CamDeviceDTO> call, Throwable t) {
                Toast.makeText(getContext(), "네트워크 오류: " + t.getMessage(), Toast.LENGTH_SHORT).show();
            }
        });
    }

    // 리스트에서 해당 아이템을 갱신
    private void updateListItem(Esp32CamDeviceDTO updatedDevice) {
        for (int i = 0; i < getCount(); i++) {
            Esp32CamDeviceDTO device = getItem(i);
            if (device != null && device.getId().equals(updatedDevice.getId())) {
                device.setDeviceName(updatedDevice.getDeviceName());  // 이름만 갱신
                notifyDataSetChanged();  // 리스트 갱신
                break;
            }
        }
    }

    private List<Esp32CamDeviceDTO> getItemsFromPreferences() {
        SharedPreferences sharedPreferences = context.getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        String savedRCCarsJson = sharedPreferences.getString("saved_rccars", "[]");

        Type listType = new TypeToken<List<Esp32CamDeviceDTO>>() {}.getType();
        return new Gson().fromJson(savedRCCarsJson, listType);
    }

    public void setSavedRCCarAdapter(SavedRCCarAdapter adapter) {
        this.savedRCCarAdapter = adapter;
    }
}
```

### RCCarSettingDialog.java
```java
public class RCCarSettingDialog extends AppCompatDialogFragment {

    private List<Esp32CamDeviceDTO> detectedList = new ArrayList<>();
    private List<Esp32CamDeviceDTO> savedList = new ArrayList<>();
    private RCCarApiService apiService;
    private DetectedRCCarAdapter detectedAdapter;
    private SavedRCCarAdapter savedAdapter;

    @NonNull
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Dialog dialog = new Dialog(requireContext());

        dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        dialog.setContentView(R.layout.activity_rccar);

        if (dialog.getWindow() != null) {
            dialog.getWindow().setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.MATCH_PARENT);
        }

        apiService = RetrofitClient.getClient("").create(RCCarApiService.class);

        // RC카 찾기 버튼
        Button buttonFindRCCar = dialog.findViewById(R.id.buttonFindRCCar);
        buttonFindRCCar.setOnClickListener(v -> fetchDetectedRCCars());

        // 등록 가능한 RC카 리스트뷰 (RC카 찾기 버튼을 눌렀을 때만 갱신됨)
        ListView listViewDetectedRCCar = dialog.findViewById(R.id.listViewDetectedRCCar);
        detectedAdapter = new DetectedRCCarAdapter(requireContext(), detectedList, this);
        detectedAdapter.setSavedRCCarAdapter(savedAdapter);
        listViewDetectedRCCar.setAdapter(detectedAdapter);

        // 저장된 RC카 리스트뷰 (앱 실행 시 자동 불러오기)
        ListView listViewSavedRCCar = dialog.findViewById(R.id.listViewSavedRCCar);
        savedAdapter = new SavedRCCarAdapter(requireContext(), savedList);
        listViewSavedRCCar.setAdapter(savedAdapter);

        fetchSavedRCCars();

        return dialog;
    }

    // 앱 실행 시 저장된 RC카만 불러오기
    private void fetchSavedRCCars() {
        apiService.getAllDevices().enqueue(new Callback<List<Esp32CamDeviceDTO>>() {
            @Override
            public void onResponse(Call<List<Esp32CamDeviceDTO>> call, Response<List<Esp32CamDeviceDTO>> response) {
                // SharedPreferences에서 저장된 RC카 리스트 불러오기
                savedList.clear();
                savedList.addAll(loadSavedRCCarsFromPreferences());
                savedAdapter.updateList(savedList);
            }

            @Override
            public void onFailure(Call<List<Esp32CamDeviceDTO>> call, Throwable t) {
                Toast.makeText(requireContext(), "저장된 RC카 불러오기 실패", Toast.LENGTH_SHORT).show();
            }
        });
    }

    // RC카 찾기 버튼을 눌렀을 시 "등록 가능한 RC카" 불러오기
    private void fetchDetectedRCCars() {
        apiService.getAllDevices().enqueue(new Callback<List<Esp32CamDeviceDTO>>() {
            @Override
            public void onResponse(Call<List<Esp32CamDeviceDTO>> call, Response<List<Esp32CamDeviceDTO>> response) {
                if (response.isSuccessful() && response.body() != null) {
                    detectedList.clear();

                    for (Esp32CamDeviceDTO device : response.body()) {
                        if (!device.isSaved()) {
                            // 새로운 객체를 생성하여 기존 데이터 복사(원본 보호)
                            Esp32CamDeviceDTO newDevice = new Esp32CamDeviceDTO(
                                    device.getId(),
                                    device.getMacAddress(),
                                    device.getDeviceName(),
                                    device.getDeviceIp(),
                                    device.isSaved()
                            );
                            detectedList.add(newDevice);
                        }
                    }
                    detectedAdapter.notifyDataSetChanged();
                }
            }

            @Override
            public void onFailure(Call<List<Esp32CamDeviceDTO>> call, Throwable t) {
                Toast.makeText(requireContext(), "RC카 찾기 실패", Toast.LENGTH_SHORT).show();
            }
        });
    }

    // SharedPreferences에서 저장된 RC카 불러오기
    private List<Esp32CamDeviceDTO> loadSavedRCCarsFromPreferences() {
        SharedPreferences sharedPreferences = requireContext().getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        String savedRCCarsJson = sharedPreferences.getString("saved_rccars", "[]");

        Type listType = new TypeToken<List<Esp32CamDeviceDTO>>() {}.getType();
        return new Gson().fromJson(savedRCCarsJson, listType);
    }

    public void updateSavedRCCarList() {
        // SharedPreferences에서 최신 데이터를 로드
        List<Esp32CamDeviceDTO> updatedList = loadSavedRCCarsFromPreferences();

        // SavedRCCarAdapter 갱신
        savedAdapter.updateList(updatedList);
    }
}
```

### SavedRCCarAdapter.java
```java
public class SavedRCCarAdapter extends ArrayAdapter<Esp32CamDeviceDTO> {
    private List<Esp32CamDeviceDTO> savedItems;

    public SavedRCCarAdapter(Context context, List<Esp32CamDeviceDTO> items) {
        super(context, 0, new ArrayList<>());
        this.savedItems = new ArrayList<>();

        // SharedPreferences에서 저장된 RC카 불러오기
        loadSavedRCCarsFromPreferences();

        // isSaved가 true인것만 리스트에 추가
        for (Esp32CamDeviceDTO device : items) {
            if (device.isSaved()) {
                savedItems.add(device);
            }
        }
        addAll(savedItems);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(getContext()).inflate(R.layout.rccar_item, parent, false);
        }

        // RC카 이름
        TextView textViewSavedRCCarName = convertView.findViewById(R.id.textViewSavedRCCarName);
        // RC카 MAC주소
        TextView textViewSavedRCCarAddress = convertView.findViewById(R.id.textViewSavedRCCarAddress);
        // RC카 삭제버튼
        Button buttonSavedRCCarDelete = convertView.findViewById(R.id.buttonSavedRCCarDelete);

        Esp32CamDeviceDTO device = getItem(position);
        textViewSavedRCCarName.setText(device.getDeviceName());
        textViewSavedRCCarAddress.setText(device.getMacAddress());

        // 삭제 버튼 클릭 이벤트
        buttonSavedRCCarDelete.setOnClickListener(v -> {
            if (device != null) {
                deleteRCCarFromPreferences(device); // SharedPreferences에서 삭제
                remove(device); // 리스트뷰에서 제거
                notifyDataSetChanged(); // UI 갱신
                Toast.makeText(getContext(), "삭제 완료: " + device.getDeviceName(), Toast.LENGTH_SHORT).show();
            }
        });

        return convertView;
    }

    // 저장된 RC카 리스트 업데이트
    public void updateList(List<Esp32CamDeviceDTO> newItems) {
        savedItems.clear();
        for (Esp32CamDeviceDTO device : newItems) {
            if (device.isSaved()) {
                savedItems.add(device);
            }
        }
        clear();
        addAll(savedItems);
        notifyDataSetChanged();

        // 업데이트된 리스트를 SharedPreferences에 저장
        saveSavedRCCarsToPreferences();
    }

    // SharedPreferences에서 저장된 RC카 리스트 불러오기
    private void loadSavedRCCarsFromPreferences() {
        SharedPreferences sharedPreferences = getContext().getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        String savedRCCarsJson = sharedPreferences.getString("saved_rccars", "[]");

        // savedRCCarsJson을 List로 변환하여 저장
        Type listType = new TypeToken<List<Esp32CamDeviceDTO>>(){}.getType();
        savedItems = new Gson().fromJson(savedRCCarsJson, listType);
    }

    // SharedPreferences에 저장된 RC카 리스트 저장
    private void saveSavedRCCarsToPreferences() {
        SharedPreferences sharedPreferences = getContext().getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        SharedPreferences.Editor editor = sharedPreferences.edit();

        // List를 JSON으로 변환하여 저장
        String savedRCCarsJson = new Gson().toJson(savedItems);
        editor.putString("saved_rccars", savedRCCarsJson);
        editor.apply();
    }

    // RC카 삭제하기
    private void deleteRCCarFromPreferences(Esp32CamDeviceDTO device) {
        SharedPreferences sharedPreferences = getContext().getSharedPreferences("RC_CAR_PREFS", Context.MODE_PRIVATE);
        String savedRCCarsJson = sharedPreferences.getString("saved_rccars", "[]");

        // 기존 리스트를 가져오기
        Type listType = new TypeToken<List<Esp32CamDeviceDTO>>() {}.getType();
        List<Esp32CamDeviceDTO> savedList = new Gson().fromJson(savedRCCarsJson, listType);

        // 삭제할 아이템을 제외한 새로운 리스트 생성
        List<Esp32CamDeviceDTO> updatedList = new ArrayList<>();
        for (Esp32CamDeviceDTO item : savedList) {
            if (!item.getMacAddress().equals(device.getMacAddress())) {
                updatedList.add(item); // MAC 주소가 일치하지 않는 경우만 추가
            }
        }

        // 업데이트된 리스트를 SharedPreferences에 저장
        SharedPreferences.Editor editor = sharedPreferences.edit();
        String updatedSavedRCCarsJson = new Gson().toJson(updatedList);
        editor.putString("saved_rccars", updatedSavedRCCarsJson);
        editor.apply();
    }
}
```

<br>
RC카의 이름을 설정하고 앱과 DB에 저장되는지 확인합니다. <br><br>

![앱에서 DB에 RC카 이름저장 용량압축](https://github.com/user-attachments/assets/6267cf43-d6a4-4c74-9c45-0a221f04843b) <br>
![앱에서 DB 저장성공](https://github.com/user-attachments/assets/84b86cba-171a-45a2-bf2b-f9b03afdbba4) <br><br>

:joystick: **5. 선택한 RC카 제어하기** <br>
저장된 RC카 리스트에서 `선택`버튼을 누르면 해당 아이템에 해당하는 DTO를 메인화면으로 가져와서 해당 RC카의 이름을 표시하고, 해당 RC카의 Ip주소에 GET요청을 보내서 웹뷰에 스트리밍을 합니다. <br>
### SavedRCCarAdapter.java
```java
public class SavedRCCarAdapter extends ArrayAdapter<Esp32CamDeviceDTO> {

    ...

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {

        ...

        // RC카 선택 버튼
        Button buttonSavedRCCarSelect = convertView.findViewById(R.id.buttonSavedRCCarSelect);
        // RC카 선택 버튼 클릭 이벤트
        buttonSavedRCCarSelect.setOnClickListener(v -> {
            // 선택한 RC카 DTO 가져오기
            Esp32CamDeviceDTO selectedDevice = getItem(position);

            // Retrofit을 이용하여 최신 ip주소 가져오기
            // RC카 재부팅시 ip주소가 변경될 수 있으므로 선택 버튼 누를때 최신으로 갱신시켜서 가져오기
            RCCarApiService apiService = RetrofitClient.getClient(BASE_URL).create(RCCarApiService.class);
            apiService.getDeviceById(selectedDevice.getId()).enqueue(new Callback<Esp32CamDeviceDTO>() {
                @Override
                public void onResponse(Call<Esp32CamDeviceDTO> call, Response<Esp32CamDeviceDTO> response) {
                    if (response.isSuccessful()) {
                        Esp32CamDeviceDTO updatedDevice = response.body();
                        String updatedMac = updatedDevice.getMacAddress();
                        String updatedIp = updatedDevice.getDeviceIp();

                        // 최신 ip주소로 SharedPreferences에 저장
                        SharedPreferences sharedPreferences = getContext().getSharedPreferences("RC_CAR_PREFS", MODE_PRIVATE);
                        SharedPreferences.Editor editor = sharedPreferences.edit();
                        editor.putString("selectedDeviceName", updatedDevice.getDeviceName());
                        editor.putString("selectedDeviceMac", updatedMac);
                        editor.putString("selectedDeviceIp", updatedIp);
                        editor.apply();

                        // 메인화면으로 이동할때 RC카 이름과 ip주소를 intent로 전달
                        Intent intent = new Intent(getContext(), MainActivity.class);
                        intent.putExtra("selectedDeviceName", updatedDevice.getDeviceName());
                        intent.putExtra("selectedDeviceMac", updatedMac);
                        intent.putExtra("selectedDeviceIp", updatedIp);
                        getContext().startActivity(intent);
                    }
                }

                @Override
                public void onFailure(Call<Esp32CamDeviceDTO> call, Throwable t) {
                    // 실패 처리
                    Toast.makeText(getContext(), "IP 정보 가져오기 실패", Toast.LENGTH_SHORT).show();
                }
            });
        });

        return convertView;
    }

    ...

}
```

### MainActivity.java
```java
public class MainActivity extends AppCompatActivity {

    private WebView webView;
    private TextView textViewSelectedRCCar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        ...

        // SharedPreferences에서 선택한 RC카 이름과 ip주소 가져오기
        SharedPreferences sharedPreferences = getSharedPreferences("RC_CAR_PREFS", MODE_PRIVATE);
        String selectedDeviceName = sharedPreferences.getString("selectedDeviceName", "선택된 RC카 없음");
        String selectedDeviceMac = sharedPreferences.getString("selectedDeviceMac", "");
        String selectedDeviceIp = sharedPreferences.getString("selectedDeviceIp", "");

        // 선택한 RC카 이름 표시
        if (selectedDeviceName != null && selectedDeviceIp != null) {
            textViewSelectedRCCar = findViewById(R.id.textViewSelectedRCCar);
            textViewSelectedRCCar.setText(selectedDeviceName);
        }

        // 선택한 RC카 웹뷰 스트리밍
        if (!selectedDeviceIp.isEmpty()) {
            WebView webView = findViewById(R.id.webView);
            webView.loadUrl("http://" + selectedDeviceIp + "/stream");
        }
    }
}
```

<br>

선택한 RC카 스트리밍 테스트 <br>

![앱 스트리밍 성공 용량압축](https://github.com/user-attachments/assets/87457830-abab-4770-8d32-7b9f23f2c827) <br><br>

<br>

캡쳐버튼 구현하기 <br>
### MainActivity.java
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

      ...

      // 캡쳐버튼
      buttonCapture = findViewById(R.id.buttonCapture);
      // 캡쳐버튼 클릭 리스너
      buttonCapture.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View view) {
              // SharedPreferences에서 선택된 RC카의 ip주소 가져오기
              SharedPreferences sharedPreferences = getSharedPreferences("RC_CAR_PREFS", MODE_PRIVATE);
              String selectedDeviceIp = sharedPreferences.getString("selectedDeviceIp", "");

              // ip주소가 없으면 처리 중단
              if (selectedDeviceIp.isEmpty()) {
                  Toast.makeText(MainActivity.this, "선택된 RC카가 없습니다.", Toast.LENGTH_SHORT).show();
                  return;
              }

              // 캡처 요청 보내기 (백그라운드에서 실행)
              new Thread(new Runnable() {
                  @Override
                  public void run() {
                      try {
                          // GET 요청 보낼 URL 생성
                          URL url = new URL("http://" + selectedDeviceIp + "/capture");
                          HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                          connection.setRequestMethod("GET");

                          // 응답 코드 확인
                          int responseCode = connection.getResponseCode();
                          connection.disconnect();

                          // UI 업데이트 (메인 스레드에서 실행)
                          runOnUiThread(new Runnable() {
                              @Override
                              public void run() {
                                  if (responseCode == 200) {
                                      Toast.makeText(MainActivity.this, "캡처 요청 완료!", Toast.LENGTH_SHORT).show();
                                  } else {
                                      Toast.makeText(MainActivity.this, "캡처 요청 실패: " + responseCode, Toast.LENGTH_SHORT).show();
                                  }
                              }
                          });

                      } catch (Exception e) {
                          e.printStackTrace();
                          runOnUiThread(new Runnable() {
                              @Override
                              public void run() {
                                  Toast.makeText(MainActivity.this, "캡처 요청 중 오류 발생", Toast.LENGTH_SHORT).show();
                              }
                          });
                      }
                  }
              }).start();
          }
      });
```

캡쳐버튼 테스트 <br>

![캡쳐기능 구현](https://github.com/user-attachments/assets/404ae8ef-3e8a-4d73-a5b5-8bd9da24ca8b) <br>
![캡쳐기능 구현 DB](https://github.com/user-attachments/assets/dd61cbbc-b721-48cc-9652-06f1bdbf48c2) <br><br>

<br>

LED 라이트 On/Off 스위치 구현하기 <br>
### MainActivity.java
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

      ...

      // 릴레이 스위치
        switchRelay = findViewById(R.id.switchRelay);
        // 릴레이 스위치 상태 변경 리스너
        switchRelay.setOnCheckedChangeListener((buttonView, isChecked) -> {
            int relayState = isChecked ? 1 : 0;  // 0 : OFF, 1 : ON

            if (!selectedDeviceMac.isEmpty()) { // MAC 주소 확인
                // 레트로핏을 사용하여 POST 요청
                RCCarApiService apiService = RetrofitClient.getClient("").create(RCCarApiService.class);

                // JSON으로 전송할 데이터 Map에 담기
                Map<String, Object> request = new HashMap<>();
                request.put("macAddress", selectedDeviceMac); // SharedPreferences에서 가져온 MAC 주소
                request.put("relayState", relayState);       // 스위치 상태

                // 릴레이 제어 API 호출
                apiService.controlRelay(request).enqueue(new Callback<String>() {
                    @Override
                    public void onResponse(Call<String> call, Response<String> response) {
                        if (response.isSuccessful()) {
                            // 릴레이 제어 성공
                            Log.d("Relay Control", "Relay control successful : " + response.body());
                        } else {
                            // 릴레이 제어 실패
                            Log.e("Relay Control", "Relay control failed : " + response.code());
                            Log.e("Relay Control", "Response message : " + response.message());
                        }
                    }

                    @Override
                    public void onFailure(Call<String> call, Throwable t) {
                        // 통신 실패
                        Log.e("Relay Control", "Request failed: " + t.getMessage());
                    }
                });
            } else {
                // MAC 주소가 없을 경우
                Toast.makeText(this, "선택된 RC카의 MAC 주소가 없습니다.", Toast.LENGTH_SHORT).show();
            }
        });
```

LED 라이트 On/Off 스위치 테스트 <br>
![릴레이제어 성공 용량압축](https://github.com/user-attachments/assets/76636eea-c07f-40a9-a116-0813abfa38ce) <br><br>

<br>

서보모터(카메라 방향) 제어하기 <br>
### MainActivity.java
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

      ...

        // 카메라 각도회전 버튼
        buttonServoLeft = findViewById(R.id.buttonServoLeft);
        buttonServoRight = findViewById(R.id.buttonServoRight);
        buttonServoTop = findViewById(R.id.buttonServoTop);
        buttonServoBottom = findViewById(R.id.buttonServoBottom);
        // 카메라 각도회전 버튼 클릭 리스너
        buttonServoLeft.setOnClickListener(v -> sendServoRequest(selectedDeviceMac, 1, 10)); // 1번 서보모터(좌우) +10도(왼쪽) 이동
        buttonServoRight.setOnClickListener(v -> sendServoRequest(selectedDeviceMac, 1, -10)); // 1번 서보모터(좌우) -10도(오른쪽) 이동
        buttonServoTop.setOnClickListener(v -> sendServoRequest(selectedDeviceMac, 2, -10)); // 2번 서보모터(상하) -10도(위) 이동
        buttonServoBottom.setOnClickListener(v -> sendServoRequest(selectedDeviceMac, 2, 10)); // 2번 서보모터(상하) +10도(아래) 이동
      }

    // Retrofit을 사용해 Spring Boot에 서보모터 제어 요청 보내기
    private void sendServoRequest(String macAddress, int servoNum, int angleIncrement) {
        // 레트로핏을 사용하여 POST 요청
        RCCarApiService apiService = RetrofitClient.getClient("").create(RCCarApiService.class);

        // JSON으로 전송할 데이터 Map에 담기
        Map<String, Object> request = new HashMap<>();
        request.put("macAddress", macAddress);
        request.put("servoNum", servoNum);
        request.put("angleIncrement", angleIncrement);

        // 서보모터 제어 API 호출
        apiService.controlServo(request).enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                if (response.isSuccessful()) {
                    // 서보모터 제어 성공
                    Log.d("Servo Control", "Servo control successful : " + response.body());
                } else {
                    // 서보모터 제어 실패
                    Log.e("Servo Control", "Servo control failed : " + response.code());
                    Log.e("Servo Control", "Response message : " + response.message());
                }
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                // 통신 실패
                Log.e("Servo Control", "Request failed: " + t.getMessage());
            }
        });
    }
```

서보모터(카메라 방향) 제어 테스트 <br>
![서보모터 제어 성공 용량압축](https://github.com/user-attachments/assets/e7a435ca-12c4-481f-8ee1-4576d57861af) <br><br>

<br>

DC모터(바퀴) 제어하기 <br>


<br><br>

***
### 5. 인공지능 자율주행
