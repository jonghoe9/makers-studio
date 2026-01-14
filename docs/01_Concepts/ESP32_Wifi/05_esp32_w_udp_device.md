# ESP32 WIFI UDP Device

- 장치에서 Wifi 접속, DHCP로 IP 받음
- 브로드캐스트 주소로 장치 정보 보내기 (1초 마다 보냄)
- 서버에서 장치를 등록하고, 서버의 IP 주소와 서버의 Listen 포트를 장치의 Listen 포트로 전송함

```cpp title="esp32_wifi_udp_device.ino" linenums="1" hl_lines="13"
#include <WiFi.h>

// ============================================ Setup Start
// WIFI 정보 입력
const char* ssid = "ssid";
const char* password = "********";

// 장치 정보와 포트 설정
const unsigned int device_id = 9;
const unsigned int listen_port = 29998;
const unsigned int dest_port = 29999;
const unsigned int broad_port = 30000;
const String device_name = "ESP32 Wifi bt9 Style Device #1";
// ============================================ Setup End

WiFiUDP udp;
String device_ip = "";
String broad_ip = "";

void setup() {
  Serial.begin(115200);
  delay(500);
  Serial.printf("\n\nConnecting to Wifi %s ", ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println(" CONNECTED");
  
  device_ip = WiFi.localIP().toString();
  broad_ip = device_ip.substring(0, device_ip.lastIndexOf('.'));
  broad_ip += ".255";

  Serial.print("Device ID: ");
  Serial.println(device_id);
  Serial.print("Device: ");
  Serial.println(device_name);
  Serial.print("Device IP: ");
  Serial.println(device_ip);
  Serial.print("Device Listen Port: ");
  Serial.println(listen_port);
  Serial.print("Broadcast IP: ");
  Serial.println(broad_ip);
  Serial.print("Broadcast Port: ");
  Serial.println(broad_port);
  Serial.print("Destination IP: ");
  Serial.println();
  Serial.print("Destination Port: ");
  Serial.println(dest_port);


  //udp.begin(port_listen);
  //Serial.printf("UDP server started at %s:%i\n",
  //              WiFi.localIP().toString().c_str(),
  //              port_listen);
}

void loop() {
  /*
  int packetSize = udp.parsePacket();
  if (packetSize) {
    Serial.printf("Received packet of size %i from %s:%i\n",
                    packetSize,
                    udp.remoteIP().toString().c_str(), 
                    udp.remotePort());

    int len = udp.read(packetBuffer, BUFF_SIZE);
    if (len > 0) {
      packetBuffer[len] = 0;
    }
    Serial.printf("Data: %s\n", packetBuffer);
    
  
    */
  // Broadcast Send, Device Info
  udp.beginPacket(broad_ip.c_str(), broad_port);
  udp.printf("%d, %s, %s, %d\n",
            device_id, device_name, device_ip, listen_port);
  udp.endPacket();
  delay(1000);
}
```