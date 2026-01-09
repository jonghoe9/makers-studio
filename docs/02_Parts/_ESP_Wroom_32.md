# ESP Wroom 32

## Pinout
ESP Wroom 32 Board

![ESP32 Wroom 32 Pinout](../img/esp32-pinout.png)

- 내장 LED는 GPIO-2

## Arduino Setup
![ESP32 Wroom 32 Arduino Set](../img/esp32-arduino.png)

## Test Code (Blink)

```cpp title="esp32-blink.ino" linenums="1"
#define LED_PIN 2
void setup() {
    pinMode(LED_PIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_PIN, HIGH);
    delay(300);
    digitalWrite(LED_PIN, LOW);
    delay(300);
}
```