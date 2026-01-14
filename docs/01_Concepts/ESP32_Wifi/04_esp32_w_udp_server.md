# ESP32 Wifi UDP Server


```cpp title="esp32_wifi_udp_server.ino" linenums="1" hl_lines="18"
#include <WiFi.h>
//#include <WiFiUdp.h>

#define BUFF_SIZE 255

const char* ssid = "df_stage24";
const char* password = "dfdf8993";
unsigned int port_listen = 50000;

char packetBuffer[BUFF_SIZE];

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

  udp.begin(port_listen);
  Serial.printf("UDP server started at %s:%i\n",
                WiFi.localIP().toString().c_str(),
                port_listen);
}

void loop() {
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

    udp.beginPacket(udp.remoteIP(), udp.remotePort());
    udp.printf("Packet received OK by ESP32 server\n");
    udp.endPacket();
  }
}
```