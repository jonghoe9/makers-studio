# ESP32 Wifi UDP Client

- ESP32에서 Wifi 접속 후 데이터 Send
- 송신 데이터 확인은 tcpdump 명령어로 확인 가능하다

```
sudo tcpdump -i en0 -X -v 'udp port 8765'
```

- 특정 IP와 Port에 문자열 전송하기 

```
$ nc -u 192.168.1.111 8765
Hello
$
```

```cpp title="esp32_wifi_udp_client1.ino" linenums="1" hl_lines="15"
#include <WiFi.h>

const char* ssid = "ssid";
const char* password = "*******";

const char* dest_ip = "192.168.6.255";
unsigned int dest_port = 50000;

WiFiUDP udp;

void setup() {
  Serial.begin(115200);
  delay(500);
  Serial.printf("Connecting to %s ", ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println(" CONNECTED");
}

void loop() {
  udp.beginPacket(dest_ip, dest_port);
  udp.printf("Packet received OK by ESP32 server\n");
  udp.endPacket();
  delay(1000);
}
```

```cpp title="esp32_wifi_udp_client2.ino" linenums="1" hl_lines="18"
#include <WiFi.h>

const char* ssid     = "ssid";
const char* password = "********";

const char* udp_dest = "192.168.6.255";
const int udp_port = 50000;

boolean chk_wifi = false;

WiFiUDP udp;

void setup() {
  Serial.begin(115200);
  Serial.println("\n\nConnecting to " + String(ssid));
  delay(200);
  WiFi.disconnect(true);
  WiFi.onEvent(WiFiEvent);
  WiFi.begin(ssid, password);
}

void loop() {
  char* msg = "Seconds since boot";
  udp_send(msg);
  delay(1000);
}

void WiFiEvent(WiFiEvent_t event) {
  switch(event) {
    case ARDUINO_EVENT_WIFI_STA_GOT_IP:
      Serial.print("MyIP: ");
      Serial.println(WiFi.localIP());
      chk_wifi = true;
      break;
    case ARDUINO_EVENT_WIFI_STA_DISCONNECTED:
      Serial.println("Wifi lost connection.");
      chk_wifi = false;
      break;
    default:
      break;
  }
}

void udp_send(char* message) {
  if(chk_wifi) {
    udp.beginPacket(udp_dest, udp_port);
    udp.printf("%s : %lu", message, millis()/1000);
    udp.endPacket();
  }
}
```
