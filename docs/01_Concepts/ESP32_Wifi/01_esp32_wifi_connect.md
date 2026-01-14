# ESP32 Wifi Connect

```cpp title="esp32_wifi_webserver.inl" linenums="1" hl_lines="1"
#include <WiFi.h>

#define LED_PIN 2

const char* ssid     = "ss_id";
const char* password = "********";

//WiFiServer server(80);

void setup() {
  Serial.begin(115200);
  delay(500);
  pinMode(LED_PIN, OUTPUT);

  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    digitalWrite(LED_PIN, LOW);
    delay(250);
    Serial.print(".");
    digitalWrite(LED_PIN, HIGH);
    delay(250);
  }
  digitalWrite(LED_PIN, HIGH);
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  Serial.println(WiFi.localIP());
  delay(300);
}
```