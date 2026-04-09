### Week 1: Platform Initialization & Persistence of Vision (POV)
**Date:** April 8, 2026

**Hardware:** Arduino UNO R4 WIFI
---

### Initial steps & Handshake
  * **Environment:** Installed the Arduino IDE and the board managers for the UNO R4.
  * **Troubleshooting:** I learned that it is important to double check you are on the correct **board** and **port**; this ensures a successful data "Handshake".
  * **Code Structure:** 'voidsetup()': Runs once at startup.
    * 'voidLoop()':Executes repeatedly (The "Brain" of the operation).
    * 'pinMode(pin, mode)': Tells the board if a pin is an input(listening) or output(talking).

### The Blink Experiment(Hardware Logic)
  * **Command Syntax:** used 'digitalWrite(13, High)' for 5V(On) and 'LOW' for 0v (Off).
  * **The "Delay" variables:** Learned that 'delay(x)' is measured in milliseconds.
  * **POV Test(The HomeWork):** I tested the limit of how fast the LED can flicker before the human eye sees a solid light.
      * **My Data:** I found that the threshold was 14ms.
      * **Reflection:** Anything lower than 14ms looked solid but dimmer. This shows how "Digital" signals(On/Off) can trick "Analog" human eye.

```cpp
// The code that proved my 14ms POV threshold

void setup() {
  pinMode(13, OUTPUT);
}

void loop() {
  digitalWrite(13, HIGH);
  delay(14);
  digitalWrite(13, LOW);
  delay(14);
}
