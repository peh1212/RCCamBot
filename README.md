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
![Java](https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=java&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white) <br><br>
:hammer: **개발도구** : <br>
&emsp; ![Arduino IDE](https://img.shields.io/badge/Arduino%20IDE-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![Android Studio](https://img.shields.io/badge/Android%20Studio-3DDC84?style=for-the-badge&logo=android-studio&logoColor=white)
![IntelliJ IDEA](https://img.shields.io/badge/IntelliJ%20IDEA-000000?style=for-the-badge&logo=intellij-idea&logoColor=white) <br><br>

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
&emsp; `ESP32-CAM WiFi 안테나 (3dBi)` 1개 <br>
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
&emsp; `-아두이노 IDE에서 보드는 ESP32 Wrover Module을 선택합니다.` <br>

#### ESP32CAM.ino
```C++
#include "esp_camera.h"
#include <WiFi.h>
#include "esp_http_server.h"
#include <ESP32Servo.h>

// 카메라모듈 핀 설정
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

// WiFi 설정
const char* ssid = "**********";
const char* password = "**********";

httpd_handle_t server = NULL;

// 서보모터 핀 설정
#define SERVO_PIN1 12
#define SERVO_PIN2 13

Servo servo1; // 서보모터 객체 생성 (1: PAN, 2:TILT)
Servo servo2;

int currentAngle1 = 90; // 서보모터 초기각도 설정 (0~180)
int currentAngle2 = 90;

// DC모터 드라이버 핀 설정
// (바퀴는 4개이지만 2개인것처럼 제어, 왼쪽 2개, 오른쪽 2개가 같이 움직임)
#define MOTOR_L1 14
#define MOTOR_L2 15
#define MOTOR_R1 2
#define MOTOR_R2 4

// 릴레이 핀 설정
#define RELAY_PIN 3

void startCameraServer();


void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  // WiFi 연결
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
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
  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;

  // 카메라 해상도
  // (UXGA: 1600x1200, XGA: 1024x768, SVGA: 800x600, VGA: 640x480, CIF: 400x296, QVGA: 320x240, QQVGA: 160x120)
  if (psramFound()) {
    config.frame_size = FRAMESIZE_UXGA;
    config.jpeg_quality = 10;
    config.fb_count = 2;
  } else {
    config.frame_size = FRAMESIZE_SVGA;
    config.jpeg_quality = 12;
    config.fb_count = 1;
  }

  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
  }

  startCameraServer();

  Serial.print("Camera Ready! Use 'http://");
  Serial.print(WiFi.localIP());
  Serial.println("' to connect");

  // 서보모터 설정
  servo1.attach(SERVO_PIN1);
  servo2.attach(SERVO_PIN2);

  servo1.write(currentAngle1);
  servo2.write(currentAngle2);
  
  // DC모터 드라이버 설정
  pinMode(MOTOR_L1, OUTPUT);
  pinMode(MOTOR_L2, OUTPUT);
  pinMode(MOTOR_R1, OUTPUT);
  pinMode(MOTOR_R2, OUTPUT);

  // DC모터 초기상태 (정지)
  digitalWrite(MOTOR_L1, LOW);
  digitalWrite(MOTOR_L2, LOW);
  digitalWrite(MOTOR_R1, LOW);
  digitalWrite(MOTOR_R2, LOW);

  // 릴레이 핀 설정 (초기상태 OFF)
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW);
}


void loop() {
  // 여기서는 아무것도 하지 않는다.
  // 웹서버에 의해서 동작된다.
  delay(10000);
}


// HTTP 요청을 처리하는 핸들러들

// 카메라 웹 스트리밍 핸들러
static esp_err_t stream_handler(httpd_req_t *req){
  camera_fb_t * fb = NULL;
  esp_err_t res = ESP_OK;
  size_t _jpg_buf_len = 0;
  uint8_t * _jpg_buf = NULL;
  char * part_buf[64];

  res = httpd_resp_set_type(req, "multipart/x-mixed-replace;boundary=frame");
  if(res != ESP_OK){
    return res;
  }

  while(true){
    fb = esp_camera_fb_get();
    if (!fb) {
      Serial.println("Camera capture failed");
      res = ESP_FAIL;
    } else {
      if(fb->width > 400){
        if(fb->format != PIXFORMAT_JPEG){
          bool jpeg_converted = frame2jpg(fb, 80, &_jpg_buf, &_jpg_buf_len);
          esp_camera_fb_return(fb);
          fb = NULL;
          if(!jpeg_converted){
            Serial.println("JPEG compression failed");
            res = ESP_FAIL;
          }
        } else {
          _jpg_buf_len = fb->len;
          _jpg_buf = fb->buf;
        }
      }
    }
    if(res == ESP_OK){
      size_t hlen = snprintf((char *)part_buf, 64, "Content-Type: image/jpeg\r\nContent-Length: %u\r\n\r\n", _jpg_buf_len);
      res = httpd_resp_send_chunk(req, (const char *)part_buf, hlen);
    }
    if(res == ESP_OK){
      res = httpd_resp_send_chunk(req, (const char *)_jpg_buf, _jpg_buf_len);
    }
    if(res == ESP_OK){
      res = httpd_resp_send_chunk(req, "\r\n--frame\r\n", 14);
    }
    if(fb){
      esp_camera_fb_return(fb);
      fb = NULL;
      _jpg_buf = NULL;
    } else if(_jpg_buf){
      free(_jpg_buf);
      _jpg_buf = NULL;
    }
    if(res != ESP_OK){
      break;
    }
  }
  return res;
}

// 서보모터 제어 핸들러
static esp_err_t servo_handler(httpd_req_t *req){
  char buf[100];
  int ret = httpd_req_recv(req, buf, req->content_len);
  if (ret <= 0) {
    return ESP_FAIL;
  }

  buf[ret] = '\0';
  int servo_num = 0;
  int direction = 0;
  sscanf(buf, "%d %d", &servo_num, &direction);

  if (servo_num == 1) {
    currentAngle1 += direction;
    currentAngle1 = constrain(currentAngle1, 0, 180); // 각도제한 0~180
    servo1.write(currentAngle1);
  } else if (servo_num == 2) {
    currentAngle2 += direction;
    currentAngle2 = constrain(currentAngle2, 0, 180); // 각도제한 0~180
    servo2.write(currentAngle2);
  } else {
    return ESP_FAIL;
  }

  httpd_resp_send(req, "Servo control OK", HTTPD_RESP_USE_STRLEN);
  return ESP_OK;
}

// DC모터 제어함수
void motorControl(int command) {
  switch (command) {
    case 0: // 정지
      digitalWrite(MOTOR_L1, LOW);
      digitalWrite(MOTOR_L2, LOW);
      digitalWrite(MOTOR_R1, LOW);
      digitalWrite(MOTOR_R2, LOW);
      break;
    case 1: // 전진
      digitalWrite(MOTOR_L1, HIGH);
      digitalWrite(MOTOR_L2, LOW);
      digitalWrite(MOTOR_R1, HIGH);
      digitalWrite(MOTOR_R2, LOW);
      break;
    case 2: // 후진
      digitalWrite(MOTOR_L1, LOW);
      digitalWrite(MOTOR_L2, HIGH);
      digitalWrite(MOTOR_R1, LOW);
      digitalWrite(MOTOR_R2, HIGH);
      break;
    case 3: // 좌회전
      digitalWrite(MOTOR_L1, LOW);
      digitalWrite(MOTOR_L2, LOW);
      digitalWrite(MOTOR_R1, HIGH);
      digitalWrite(MOTOR_R2, LOW);
      break;
    case 4: // 우회전
      digitalWrite(MOTOR_L1, HIGH);
      digitalWrite(MOTOR_L2, LOW);
      digitalWrite(MOTOR_R1, LOW);
      digitalWrite(MOTOR_R2, LOW);
      break;
  }
}

// DC모터 제어 핸들러
static esp_err_t motor_handler(httpd_req_t *req) {
  char buf[10];
  int ret = httpd_req_recv(req, buf, req->content_len);
  if (ret <= 0) return ESP_FAIL;
  buf[ret] = '\0';
  int command = atoi(buf);
  motorControl(command);
  httpd_resp_send(req, "Motor control OK", HTTPD_RESP_USE_STRLEN);
  return ESP_OK;
}

// 릴레이 제어 핸들러
static esp_err_t relay_handler(httpd_req_t *req) {
  char buf[10];
  int ret = httpd_req_recv(req, buf, req->content_len);
  if (ret <= 0) return ESP_FAIL;
  buf[ret] = '\0';
  int state = atoi(buf);
  digitalWrite(RELAY_PIN, state ? HIGH : LOW);
  httpd_resp_send(req, "Relay control OK", HTTPD_RESP_USE_STRLEN);
  return ESP_OK;
}

// 카메라 서버 시작 함수
void startCameraServer() {
  // 웹 서버 설정 기본값 초기화
  httpd_config_t config = HTTPD_DEFAULT_CONFIG();

  // 카메라 웹 스트리밍 제어 URI 설정
  httpd_uri_t index_uri = {
    .uri       = "/",
    .method    = HTTP_GET,
    .handler   = stream_handler,
    .user_ctx  = NULL
  };

  // 서보모터 제어 URI 설정
  httpd_uri_t servo_uri = {
    .uri       = "/servo",
    .method    = HTTP_POST,
    .handler   = servo_handler,
    .user_ctx  = NULL
  };

  // DC모터 제어 URI 설정
  httpd_uri_t motor_uri = {
    .uri = "/motor",
    .method = HTTP_POST,
    .handler = motor_handler,
    .user_ctx = NULL
  };

  // 릴레이 제어 URI 설정
  httpd_uri_t relay_uri = {
    .uri = "/relay",
    .method = HTTP_POST,
    .handler = relay_handler,
    .user_ctx = NULL
  };

  // HTTP 서버 시작
  if (httpd_start(&server, &config) == ESP_OK) {
    // URI 핸들러 등록
    httpd_register_uri_handler(server, &index_uri);
    httpd_register_uri_handler(server, &servo_uri);
    httpd_register_uri_handler(server, &motor_uri);
    httpd_register_uri_handler(server, &relay_uri);
  }
}

// HTTP 요청
// Method : POST
// Header : Content-Type : text/plain
// 1. 서보모터 제어
// 엔드포인트 : http://(ESP32-CAM IP주소)/servo
// Body : 서보모터 번호와 변화량 (예: 1 -10 -> 1번 서보모터 각도 -10도 증가)
// 2. DC모터 제어
// 엔드포인트 : http://(ESP32-CAM IP주소)/motor
// Body : 0~4 (0: 정지, 1: 전진, 2: 후진, 3: 좌회전, 4: 우회전)
// 3. 릴레이 제어
// 엔드포인트 : http://(ESP32-CAM IP주소)/relay
// Body : 0~1 (0: OFF, 1: ON)
```
<br>

:bulb: **2. ESP32-CAM에 코드를 업로드한 후, 배터리를 연결하여 전체 회로 점검 및 작동테스트를 합니다.** <br>
<br>
&emsp; :white_check_mark: **ESP32-CAM에 핀을 연결하고 테스트하기** <br>
&emsp; ![esp32cam 업로드보드](https://github.com/user-attachments/assets/1ae8f680-7176-4a1e-9641-5206aaf35128) <br>
&emsp; ESP32-CAM에 업로드보드를 연결한 상태에서는 핀을 꽂을 수 없게 되어있습니다. <br>
&emsp; (업로드 모드와 실행 모드에서 일부 핀이 다르게 동작하기 때문에 이렇게 만들었다고 합니다) <br>
&emsp; 하지만 업로드보드를 빼는 순간 전원이 끊기기 때문에 ESP32-CAM의 전원이 꺼지게 됩니다. <br>
&emsp; ESP32는 DHCP(Dynamic Host Configuration Protocol)를 사용하여 전원을 켰을 때 라우터에서 IP 주소를 자동으로 할당해줍니다. <br>
&emsp; 따라서 핀을 연결하기 위해 업로드보드를 빼고 외부 전원을 연결하면 ESP32-CAM이 재부팅되면서 IP 주소가 달라지고, 시리얼모니터와도 연결이 끊겨서 IP 주소를 알 수 없게 됩니다. <br><br>
&emsp; 고정 IP를 설정하여 전원을 껐다 켜도 동일한 IP 주소를 유지하도록 하는 방법도 많이 사용되지만, 이후에 ESP32-CAM이 부팅될 때 IP 주소를 서버로 전송하는 방식으로 구현할 것이기 때문에 여기서는 일단 동적 IP 주소를 사용하고 IP 스캐너로 찾아내서 테스트를 진행합니다. <br>
&emsp; 테스트는 Talend API Tester를 이용하여 ESP32-CAM에 POST 요청을 보내고 각 부품들이 제대로 동작하는지 확인합니다. <br>

&emsp; 1. 카메라 웹 스트리밍 테스트 <br>
&emsp; ![cameratest](https://github.com/user-attachments/assets/d7080d2d-62c9-4913-bc05-ae7e466bfb21) <br><br>
&emsp; 2. 서보모터 제어 테스트 <br>
&emsp; ![servotest](https://github.com/user-attachments/assets/59318f70-2557-4b74-91ca-4afb1a7bbb75) <br><br>
&emsp; 3. DC모터 제어 테스트 <br>
&emsp; ![dcmotortest](https://github.com/user-attachments/assets/92a1e8cf-5550-4d74-b90c-69e64474db42) <br><br>
&emsp; 4. 릴레이 제어 테스트 <br>
&emsp; ![relaytest](https://github.com/user-attachments/assets/78b181ca-0f65-4672-826d-97b245f1c102) <br><br>

:nut_and_bolt: **3. 조립하기** <br>
&emsp; ![조립 후 테스트](https://github.com/user-attachments/assets/c1880c53-5f9b-4607-bfc6-78e77f13b42f) <br>
&emsp; 알루미늄 프레임을 제작하여 2대를 조립한 후 테스트합니다. <br><br>

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
#### Esp32CamInfo.java
```Java
interface Esp32CamDeviceRepo extends JpaRepository<Esp32CamDevice, Long> {
    Optional<Esp32CamDevice> findByMacAddress(String macAddress);
}
interface Esp32CamImageRepo extends JpaRepository<Esp32CamImage, Long> {
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
    public Esp32CamDevice registerDevice(String macAddress, String ipAddress, String deviceName) {
        Esp32CamDevice device = deviceRepo.findByMacAddress(macAddress)
                .orElse(Esp32CamDevice.builder()
                        .macAddress(macAddress)
                        .deviceIp(ipAddress)
                        .deviceName(deviceName)
                        .build());
        device.setDeviceIp(ipAddress);
        return deviceRepo.save(device);
    }

    // 전체 RC카 조회하기
    public List<Esp32CamDevice> getAllDevices() {
        return deviceRepo.findAll();
    }

    // RC카 수정하기
    @Transactional
    public Esp32CamDevice updateDevice(Long deviceId, String deviceName, String ipAddress) {
        // 해당 ID의 장치가 존재하는지 확인
        Esp32CamDevice device = deviceRepo.findById(deviceId)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 장치가 없습니다."));

        // 수정할 필드 값 설정
        if (deviceName != null && !deviceName.isEmpty()) {
            device.setDeviceName(deviceName);
        }
        if (ipAddress != null && !ipAddress.isEmpty()) {
            device.setDeviceIp(ipAddress);
        }

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

    // RC카가 찍은 사진 저장하기
    @Transactional
    public Esp32CamImage saveImage(Long deviceId, String imageData) {
        Esp32CamDevice device = deviceRepo.findById(deviceId)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 장치가 없습니다."));

        String fileName = UUID.randomUUID() + ".jpg";
        String filePath = Paths.get(storagePath, fileName).toString();

        Path sourcePath = Path.of(imageData);
        Path destinationPath = Path.of(filePath);

        try {
            Files.copy(sourcePath, destinationPath, StandardCopyOption.REPLACE_EXISTING);
        } catch (IOException e) {
            throw new RuntimeException("이미지 저장 실패", e);
        }

        Esp32CamImage image = Esp32CamImage.builder()
                .imagePath(filePath)
                .esp32CamDevice(device)
                .build();

        return imageRepo.save(image);
    }

    // RC카가 찍은 사진 조회하기
    public Resource getImageById(Long imageId) {
        Esp32CamImage image = imageRepo.findById(imageId)
                .orElseThrow(() -> new IllegalArgumentException("해당 ID의 이미지가 없습니다."));
        return new FileSystemResource(new File(image.getImagePath()));
    }
}
```

:twisted_rightwards_arrows: **6. RestController 구현하기** <br>
&emsp; 컨트롤러를 구현합니다. <br>
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
    public ResponseEntity<?> registerDevice(@RequestBody Map<String, String> request) {
        String macAddress = request.get("macAddress");
        String deviceIp = request.get("deviceIp");
        String deviceName = request.get("deviceName");
        Esp32CamDevice device = rcCarService.registerDevice(macAddress, deviceIp, deviceName);
        return ResponseEntity.ok(device);
    }

    // 전체 RC카 조회하기
    @GetMapping("/devices")
    public ResponseEntity<List<Esp32CamDevice>> getDevices() {
        return ResponseEntity.ok(rcCarService.getAllDevices());
    }

    // RC카 수정하기
    @PatchMapping("/device/{id}")
    public ResponseEntity<Esp32CamDevice> updateDevice(@PathVariable Long id, @RequestBody Map<String, String> request) {
        String deviceName = request.get("deviceName");
        String deviceIp = request.get("deviceIp");

        Esp32CamDevice updatedDevice = rcCarService.updateDevice(id, deviceName, deviceIp);
        return ResponseEntity.ok(updatedDevice);
    }


    // RC카 삭제하기
    @DeleteMapping("/device/{deviceId}")
    public ResponseEntity<String> deleteDevice(@PathVariable Long deviceId) {
        rcCarService.deleteDevice(deviceId);
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
    @PostMapping("/image/upload/{deviceId}")
    public ResponseEntity<String> uploadImage(@PathVariable Long deviceId, @RequestBody String imageData) {
        Esp32CamImage savedImage = rcCarService.saveImage(deviceId, imageData);
        return ResponseEntity.ok("이미지 저장 완료: " + savedImage.getImagePath());
    }

    // 이미지 가져오기
    @GetMapping("/image/{imageId}")
    public ResponseEntity<Resource> getImage(@PathVariable Long imageId) {
        Resource imageResource = rcCarService.getImageById(imageId);
        return ResponseEntity.ok()
                .contentType(MediaType.IMAGE_JPEG)
                .body(imageResource);
    }
}
```
<br>

:mag_right: **7. H2 DB 연동 및 CRUD 동작 확인하기** <br>
&emsp; `application.properties`에 H2 DB 관련 설정들을 추가합니다. <br>
```Java
spring.datasource.url=jdbc:h2:mem:testdb // h2 db 주소 고정
spring.h2.console.enabled=true // h2콘솔 활성화
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```
&emsp; Talend API Tester로 컨트롤러에서 구현한 엔드포인트에 HTTP 요청들을 보내고, H2 Console로 잘 처리되었는지 확인합니다. <br>
&emsp; ![h2db 테스트](https://github.com/user-attachments/assets/028480b7-872e-4a78-906b-17d1c2b7e211) <br>


:floppy_disk: **8. PostgreSQL DB 전환** <br>

:satellite: **9. ESP32-CAM 통신 테스트** <br>

***
### 5. 안드로이드앱 개발
