![header](https://capsule-render.vercel.app/api?type=waving&height=250&color=2196f3&text=ESP32-CAM%20자동차%20원격제어%20스트리밍%20시스템&fontSize=42&strokeWidth=0&fontColor=ffffff&fontAlignY=38)

***
### 0. 프로젝트 개요
:punch: **1인 프로젝트** <br>
&emsp; 안드로이드 앱으로 ESP32-CAM 기반의 자동차 2대를 원격 제어하는 프로젝트입니다. <br>
&emsp; 차량의 이동을 제어하고, LED 라이트를 ON/OFF하고, 카메라의 방향을 제어하고, 카메라의 영상을 스트리밍하는 기능을 구현합니다. <br><br>

:calendar: **프로젝트 기간** : <br>
&emsp; `2025. 2 ~ 2025. 3` <br><br>
:books: **기술스택** : <br>
&emsp; ![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![Java](https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=java&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Retrofit](https://img.shields.io/badge/Retrofit-00B5A6?style=for-the-badge&logo=retrofit&logoColor=white) <br><br>
:hammer: **개발도구** : <br>
&emsp; ![Arduino IDE](https://img.shields.io/badge/Arduino%20IDE-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![IntelliJ IDEA](https://img.shields.io/badge/IntelliJ%20IDEA-000000?style=for-the-badge&logo=intellij-idea&logoColor=white)
![Android Studio](https://img.shields.io/badge/Android%20Studio-3DDC84?style=for-the-badge&logo=android-studio&logoColor=white) <br><br>

:page_with_curl: **목차** : <br>
&emsp; [1. 시스템 기획](#1-시스템-기획) <br>
&emsp; [2. 하드웨어 설계](#2-하드웨어-설계) <br>
&emsp; [3. 펌웨어 개발](#3-펌웨어-개발) <br>
&emsp; [4. 백엔드 개발](#4-백엔드-개발) <br>
&emsp; [5. 안드로이드앱 개발](#5-안드로이드앱-개발) <br>
<br>

***
### 1. 시스템 기획
&emsp; ![시스템기획](https://github.com/user-attachments/assets/aec19f8a-0219-44e0-80bf-7f8e623c7a8b) <br>
&emsp; ESP32-CAM은 Wi-Fi 및 Bluetooth 통신 모듈이 내장된 ESP32 MCU 기반의 카메라 모듈입니다. <br>
&emsp; 이 프로젝트에서는 ESP32-CAM을 활용하여 RC카를 제작하고, 원격 제어 및 실시간 카메라 스트리밍 기능을 구현합니다. <br>
&emsp; ESP32-CAM은 Wi-Fi를 통해 Spring Boot 서버와 통신하며, HTTP POST 요청을 수신하여 RC카의 이동 및 카메라 회전, LED 라이트 제어를 수행하고, HTTP GET 요청을 처리하여 실시간 영상 스트리밍을 제공합니다. <br>
&emsp; 안드로이드 앱에서 사용자의 입력을 받아 Retrofit을 이용하여 Spring Boot 서버로 HTTP 요청을 전송합니다. <br>
&emsp; Spring Boot는 안드로이드 앱과 ESP32-CAM 자동차가 통신할 수 있도록 API를 제공하며, PostgreSQL DB를 사용하여 자동차의 ID, IP주소 등의 데이터를 관리합니다. <br>
&emsp; 안드로이드 앱에서는 ESP32-CAM 자동차의 IP 주소를 스캔하여 고유 ID와 함께 저장할 수 있고, 이를 통해 2대 이상의 자동차를 각각 제어할 수 있습니다. <br>

***
### 2. 하드웨어 설계
:zap: **1. 전체 회로도 그리기** <br>
&emsp; ![전체 회로](https://github.com/user-attachments/assets/fecb2289-c9cc-4c74-9f7a-d6c053ef4e7b) <br>
&emsp; 위와 같이 전체 회로를 구성합니다. <br>
&emsp; 전원은 18650 배터리를 2S2P로 연결하여 사용합니다. <br>
&emsp; 스텝다운 컨버터를 사용하여 ESP32-CAM에 5V 전원을 공급합니다. <br>
&emsp; 서보모터 라인에 스텝다운 컨버터를 사용하여 5V 전원을 공급합니다. <br>
&emsp; 2채널 DC모터드라이버 모듈을 통해 DC모터 4개를 제어합니다. <br>
&emsp; 스텝업 컨버터를 사용하여 12V LED 라이트에 전원을 공급합니다. <br>
&emsp; ESP32-CAM으로 서보모터의 PWM신호를 생성하고, DC모터 드라이버를 제어하고, LED 릴레이를 스위칭합니다. <br>
&emsp; ESP32-CAM의 출력 핀은 아래와 같이 배정합니다.
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
&emsp; `18650 배터리 (3.7V 2200mAh)` 4개 <br>
&emsp; `전원 똑딱이 스위치` 1개 <br>
&emsp; `DC-DC 스텝다운 컨버터(LM2596)` 2개 <br>
&emsp; `MCU(ESP32-CAM)` 1개 <br>
&emsp; `ESP32-CAM WiFi 안테나 (3dBi)` 1개 -> `6dBi로 교체` <br>
&emsp; `팬틸트용 SG90 서보모터` 2개 <br>
&emsp; `2채널 DC모터 드라이버(DRV8833)` 1개 <br>
&emsp; `아두이노 바퀴용 DC모터` 4개 <br>
&emsp; `DC-DC 스텝업 컨버터(XL6009)` 1개 <br>
&emsp; `3.3V 릴레이` 1개 <br>
&emsp; `12V LED 라이트` 1개 <br><br>

***
### 3. 펌웨어 개발
:memo: **1. 아두이노 IDE를 이용하여, HTTP 요청으로 카메라 스트리밍, 서보모터, DC모터, 릴레이를 제어하는 코드를 구현하고, ESP32-CAM에 업로드합니다.** <br>
:warning: `-와이파이 이름과 비밀번호, 서버의 IP 주소는 하드코딩하여 ESP32-CAM에 업로드합니다.` <br>
&emsp; `-와이파이 환경이 바뀌거나, 서버를 다른 환경에서 구동한다면 수정해주어야 합니다.` <br>
&emsp; `-와이파이 정보는 별도의 환경설정 파일을 사용하여 암호화할 수 있습니다.` <br>
&emsp; `-클라우드 서버에서 구동하는 경우 서버의 도메인 주소를 사용하여 IP 주소를 직접 입력하지 않도록 자동으로 처리할 수 있습니다.` <br>
&emsp; `-ESP32-CAM 부팅 시 ESP32-CAM의 ip주소와 MAC주소를 JSON으로 변환하여 스프링부트 서버로 전송합니다.` <br>
&emsp; `-스트리밍 중 영상 캡쳐(사진으로 저장) 기능은 base64로 인코딩하여 스프링부트 서버로 전송하고, 스프링부트 서버의 서비스 로직에서 이미지로 디코딩하여 로컬에 저장합니다.` <br>
&emsp; `-스트리밍 중 HTTP 요청을 처리하는 방식은 AsyncWebServer 객체를 생성하여 비동기 처리하는 대신 FreeRTOS를 이용하여 멀티스레드에서 처리합니다.` <br>
&emsp; `-아두이노 IDE에서 보드는 ESP32 Wrover Module을 선택합니다.` <br>

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
    config.frame_size = FRAMESIZE_VGA;
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
  pinMode(LEFT_MOTOR_FORWARD_PIN, OUTPUT);
  pinMode(LEFT_MOTOR_BACKWARD_PIN, OUTPUT);
  pinMode(RIGHT_MOTOR_FORWARD_PIN, OUTPUT);
  pinMode(RIGHT_MOTOR_BACKWARD_PIN, OUTPUT);

  // 릴레이 설정
  pinMode(RELAY_PIN, OUTPUT);

  // 서보모터 현재위치 초기화
  servo1.write(currentAngle1);
  servo2.write(currentAngle2);

  // DC모터 초기상태 정지
  digitalWrite(LEFT_MOTOR_FORWARD_PIN, LOW);
  digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
  digitalWrite(RIGHT_MOTOR_FORWARD_PIN, LOW);
  digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);

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
  if (server.hasArg("plain") == false) {
    server.send(400, "text/plain", "Body not received");
    return;
  }
  String body = server.arg("plain");
  int servoNum, angleIncrement;
  sscanf(body.c_str(), "%d %d", &servoNum, &angleIncrement);

  if (servoNum == 1) {
    currentAngle1 += angleIncrement;
    if (currentAngle1 < 0) currentAngle1 = 0;
    if (currentAngle1 > 180) currentAngle1 = 180;
    servo1.write(currentAngle1);
    Serial.printf("Servo %d moved to %d degrees\n", servoNum, currentAngle1);
  } else if (servoNum == 2) {
    currentAngle2 += angleIncrement;
    if (currentAngle2 < 0) currentAngle2 = 0;
    if (currentAngle2 > 180) currentAngle2 = 180;
    servo2.write(currentAngle2);
    Serial.printf("Servo %d moved to %d degrees\n", servoNum, currentAngle2);
  }

  server.send(200, "text/plain", "Servo moved");
}

// DC모터 제어 함수
void handleMotor() {
  if (server.hasArg("plain") == false) {
    server.send(400, "text/plain", "Body not received");
    return;
  }
  String body = server.arg("plain");
  int motorState;
  sscanf(body.c_str(), "%d", &motorState);

  switch (motorState) {
    case 0: // 정지
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);
      break;
    case 1: // 전진
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, HIGH);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, HIGH);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);
      break;
    case 2: // 후진
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, HIGH);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, HIGH);
      break;
    case 3: // 좌회전
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, HIGH);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);
      break;
    case 4: // 우회전
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, HIGH);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);
      break;
    default: // 정지
      digitalWrite(LEFT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(LEFT_MOTOR_BACKWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_FORWARD_PIN, LOW);
      digitalWrite(RIGHT_MOTOR_BACKWARD_PIN, LOW);
      break;
  }

  server.send(200, "text/plain", "Motor state updated");
}

// 릴레이 제어 함수
void handleRelay() {
  if (server.hasArg("plain") == false) {
    server.send(400, "text/plain", "Body not received");
    return;
  }
  String body = server.arg("plain");
  int relayState;
  sscanf(body.c_str(), "%d", &relayState);

  if (relayState == 1) {
    digitalWrite(RELAY_PIN, HIGH);
  } else {
    digitalWrite(RELAY_PIN, LOW);
  }

  server.send(200, "text/plain", "Relay state updated");
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
  String deviceName = "MyRCCar"; // 안드로이드 앱에서 설정 가능

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

| 기능 | HTTP 요청 | Headers | 엔드포인트 | Body |  |
|-------|-------|-------|-------|-------|-------|
| 스트리밍 | GET |  | /stream |  |  |
| 이미지를 캡쳐하여 base64로 변환하여 MAC주소와 함께 스프링부트서버에 전송 | GET |  | /capture |  |  |
| 서보모터 제어 | POST | Content-Type : text/plain | /servo | ex) 1 5  (1번 서보모터 각도 5도 이동) | (제어할 서보모터 번호) (서보모터 각도 변화량) |
| DC모터 제어 | POST | Content-Type : text/plain | /motor | 0~4 | 0:정지, 1:전진, 2:후진, 3:좌회전, 4:우회전 |
| 릴레이 제어 | POST | Content-Type : text/plain | /relay | 0~1 | 0:Off, 1:On |


:bulb: **2. ESP32-CAM에 코드를 업로드한 후, 배터리를 연결하여 전체 회로 점검 및 작동테스트를 합니다.** <br>
&emsp; 테스트는 Talend API Tester를 이용하여 ESP32-CAM의 ip주소에 POST 요청을 보내고 각 부품들이 제대로 동작하는지 확인합니다. <br>

&emsp; 1. 카메라 웹 스트리밍 테스트 <br>
&emsp; ![cameratest](https://github.com/user-attachments/assets/d7080d2d-62c9-4913-bc05-ae7e466bfb21) <br><br>
&emsp; 2. 서보모터 제어 테스트 <br>
&emsp; ![servotest](https://github.com/user-attachments/assets/59318f70-2557-4b74-91ca-4afb1a7bbb75) <br><br>
&emsp; 3. DC모터 제어 테스트 <br>
&emsp; ![dcmotortest](https://github.com/user-attachments/assets/92a1e8cf-5550-4d74-b90c-69e64474db42) <br><br>
&emsp; 4. 릴레이 제어 테스트 <br>
&emsp; ![relaytest](https://github.com/user-attachments/assets/78b181ca-0f65-4672-826d-97b245f1c102) <br><br>

:nut_and_bolt: **3. 조립하기** <br>
&emsp; 1. 프레임을 설계하여 알루미늄 가공업체에 제작을 의뢰합니다. <br>
&emsp; ![프레임2](https://github.com/user-attachments/assets/075c931a-939c-4e1c-a381-135b272d2536) <br><br>
&emsp; 2. 2대 조립하기(약 4시간 소요) <br>
&emsp; ![조립중 및 2대조립완료](https://github.com/user-attachments/assets/2443ac34-37a4-4cfe-bd1c-d2d19f63c9cc) <br><br>
&emsp; 3. 조립 완료 후 잘 움직이는지 테스트합니다. <br>
&emsp; Talend API Tester로 ESP32-CAM ip주소에 POST 요청을 보내고 각 부품들이 제대로 움직이는지 확인합니다. <br>
&emsp; (왼쪽 : 핸드폰으로 촬영한 화면, 오른쪽 : RC카 카메라 화면) <br>
&emsp; ![조립테스트 영상 2개 합친것 용량압축](https://github.com/user-attachments/assets/b7a05f9c-ae1a-4dd6-bbd7-6c5992a32da3) <br>
<br><br>

***
### 4. 백엔드 개발
:seedling: **1. 프로젝트 생성하기** <br>
&emsp; start.spring.io에서 스프링부트 프로젝트를 생성합니다. <br>
&emsp; Project : `Gradle - Groovy` <br>
&emsp; Language : `Java` <br>
&emsp; Spring Boot : `3.4.3` <br>
&emsp; Name : `SpringBootApi` <br>
&emsp; Packaging : `Jar` <br>
&emsp; Java : `23` <br>
&emsp; Dependencies : `Spring Data JPA`, `Spring Web`, `Lombok`, `Mustache`, `H2 Database`, `PostgreSQL Driver` <br><br>

:blue_book: **2. Entity 정의하기** <br>
&emsp; DB에 RC카 정보를 저장하기 위한 Entity를 정의합니다. <br>
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
&emsp; Entity를 저장할 Repository 인터페이스를 생성합니다. <br>
&emsp; Esp32CamDeviceRepo에는 MAC 주소로 ESP32-CAM 객체를 조회하는 쿼리 메서드를 추가합니다. <br>
&emsp; Esp32CamImageRepo에는 몇 개의 이미지가 저장되어 있는지 카운트하는 메서드를 추가합니다. <br>
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
&emsp; 데이터를 주고받기 위한 DTO를 구현합니다. <br>
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
&emsp; 서비스 클래스를 생성하여 RC카 등록, 조회, 정보 수정, 특정 RC카 삭제, 서보모터, DC모터, 릴레이 제어, 사진 찍기, 전체 이미지 조회하기 기능을 처리하는 로직을 구현합니다. <br>

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
    public String controlServo(int servoId, int angle) {
        return servoId + "번 서보모터 " + angle + "도로 이동";
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

    // Light 제어
    public String controlRelay(int state) {
        return state == 1 ? "Relay ON" : "Relay OFF";
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
&emsp; 컨트롤러를 구현합니다. <br>
| 기능 | HTTP 메서드 | URL | 요청 데이터 |
|-------|-------|-------|-------|
| id로 장치 조회하기 | GET | /api/rc/device/{id} | X |
| RC카 등록하기 | POST | /api/rc/register | macAddress, ipAddress, deviceName |
| 전체 RC카 조회하기 | GET | /api/rc/devices | X |
| RC카 수정하기 | PATCH | /api/rc/device/{id} | macAddress, ipAddress, deviceName |
| RC카 삭제하기 | DELETE | /api/rc/device/{id} | X |
| 서보모터 제어 | POST | /api/rc/servo | servoId, angle |
| DC모터 제어 | POST | /api/rc/motor | 0~4 |
| 릴레이 스위치 제어 | POST | /api/rc/relay | 0~1 |
| 이미지 저장하기 | POST | /api/rc/image/upload | imageData |
| 이미지 가져오기 | GET | /api/rc/image | X |

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
    public ResponseEntity<String> deleteDevice(@PathVariable Long id) {
        rcCarService.deleteDevice(id);
        return ResponseEntity.ok("장치 삭제 완료");
    }

    // 서보모터 제어
    @PostMapping("/servo")
    public ResponseEntity<String> controlServo(@RequestBody Map<String, Integer> request) {
        int servoId = request.get("servoId");
        int angle = request.get("angle");
        return ResponseEntity.ok(rcCarService.controlServo(servoId, angle));
    }

    // DC모터 제어
    @PostMapping("/motor")
    public ResponseEntity<String> controlMotor(@RequestBody Map<String, Integer> request) {
        int command = request.get("command");
        return ResponseEntity.ok(rcCarService.controlMotor(command));
    }

    // LED 스위치 제어
    @PostMapping("/relay")
    public ResponseEntity<String> controlRelay(@RequestBody Map<String, Integer> request) {
        int state = request.get("state");
        return ResponseEntity.ok(rcCarService.controlRelay(state));
    }

    // 이미지 저장하기
    @PostMapping("/image/upload")
    public ResponseEntity<String> uploadImage(@RequestBody Map<String, String> requestData) {
        String macAddress = requestData.get("macAddress");
        String base64Image = requestData.get("image");

        if (macAddress == null || base64Image == null) {
            return ResponseEntity.badRequest().body("MAC 주소 또는 이미지 데이터가 누락되었습니다.");
        }

        String savedFilePath = rcCarService.saveImage(macAddress, base64Image);
        return ResponseEntity.ok("이미지 저장 완료: " + savedFilePath);
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
&emsp; `application.properties`에 H2 DB 관련 설정들을 추가하고 CRUD 기능을 테스트합니다. <br>
```Java
spring.datasource.url=jdbc:h2:mem:testdb // h2 db 주소 고정
spring.h2.console.enabled=true // h2콘솔 활성화
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```
&emsp; Talend API Tester로 컨트롤러에서 구현한 엔드포인트에 HTTP 요청들을 보내고, H2 Console로 제대로 처리되었는지 확인합니다. <br>
&emsp; ![h2db 테스트](https://github.com/user-attachments/assets/028480b7-872e-4a78-906b-17d1c2b7e211) <br>

:floppy_disk: **8. PostgreSQL DB 전환** <br>
&emsp; PostgreSQL DB에서 프로젝트를 생성한 후, `application.properties`에 PostgreSQL 설정을 추가하여 H2 DB 대신 PostgreSQL DB를 연결합니다. <br>
```Java
spring.datasource.url=jdbc:postgresql://localhost:5432/RCCamBot
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.username=postgres
spring.datasource.password=p
spring.datasource.data=classpath:/data.sql
spring.datasource.initialization-mode=always
spring.jpa.hibernate.ddl-auto=update
```
&emsp; ![postgresqldb 전환](https://github.com/user-attachments/assets/f393c548-3284-43e2-8f41-1e11d0938d16) <br>

:satellite: **9. ESP32-CAM 통신 테스트** <br>
&emsp; RC카 전원 On시(ESP32-CAM 부팅시) DB에 디바이스 정보가 등록되는지 확인합니다. <br>
&emsp; `MAC주소는 고유하기 때문에 이후에 재부팅시 라우터에서 다른 ip주소를 할당받아도 ip주소만 새로 갱신됩니다.` <br>
&emsp; `RC카 이름은 안드로이드 앱에서 사용자가 설정하여 저장할 수 있습니다.` <br>
&emsp; ![전원 온시 디비에 디바이스정보 등록 용량압축](https://github.com/user-attachments/assets/e8038c4f-2f74-4604-9466-a6a1b1d82846) <br><br>
&emsp; RC카 2대 모두 등록한 후 사진을 찍고 로컬에 잘 저장되는지 확인합니다. <br>
&emsp; ![2대 등록](https://github.com/user-attachments/assets/1506fccd-5dc2-4464-8342-f3b601c9c0a5) <br>
&emsp; ![2대 사진찍기 테스트](https://github.com/user-attachments/assets/e7f08fa1-cac4-476a-a995-97f57c8115c5) <br>
&emsp; ![이미지저장테스트](https://github.com/user-attachments/assets/d670dcb8-43a1-4301-bf6b-26d10e787ef4) <br><br>


***
### 5. 안드로이드앱 개발
:pager: **1. UI 화면 구성하기** <br>
&emsp; 안드로이드 앱의 UI를 디자인합니다. <br>
&emsp; 메인화면은 RC카의 영상을 스트리밍하는 웹뷰와, RC카의 이동 제어 및 카메라 방향 제어, 라이트 On/Off, 사진 촬영 버튼으로 구성합니다. <br>
&emsp; RC카 설정 화면에서는 전원이 켜져있는 RC카를 탐색하여 고유 ID를 저장하고 관리할 수 있습니다. <br>
&emsp; ![android ui](https://github.com/user-attachments/assets/d29bb7d1-cd11-4348-b9a9-4658915dd3c8) <br>

