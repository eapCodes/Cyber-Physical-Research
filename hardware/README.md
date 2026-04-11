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
```

---

## Lesson 2: Circuit Architecture & Algorithmic Optimization
**Date:** April 11, 2026

### I. The Physics of Protection (Ohm's Law)
To transition from the onboard LED to an external circuit, I calculated the appropriate resistance to prevent overcurrent.

* **The Problem:** LEDs have negligible resistance once they hit their "Forward Voltage" ($V_f$). Without a current-limiting resistor, the LED will draw excessive current, potentially damaging the component or the Arduino's GPIO pin.
* **The Calculation:** * $V_{source} (5V) - V_{forward} (\approx 2V) = 3V$ (Voltage drop required across the resistor).
    * Target Current: $15mA$ ($0.015A$) for optimal brightness and safety.
    * Formula: $R = V / I \rightarrow 3 / 0.015 = 200\Omega$.
* **Implementation:** Used a **220-ohm resistor** (Red-Red-Brown) as a standard safety buffer.

### II. Breadboard Topology & Bus Logic
I mastered the internal routing of the solderless breadboard to create a stable hardware environment.
* **Power Rails:** Vertical strips used as a common 5V/GND bus.
* **Terminal Strips:** Horizontal rows separated by a "Center Divot" (DIP Support).
* **CS Insight:** The breadboard functions as a physical **Bus**. The center ditch allows for the placement of Integrated Circuits (ICs) by isolating the pins on each side, preventing short circuits.

### III. Software Refactoring (From Linear to Modular)
Initially, the SOS signal was written as "Spaghetti Code" (linear sequence). I refactored the code using Computer Science fundamentals to move toward a professional architecture:

1.  **Procedural Abstraction:** Encapsulated hardware actions into custom `sendDit()` and `sendDah()` functions.
2.  **Control Flow Optimization:** Implemented `for` loops to handle the SOS repetition, significantly reducing code redundancy and memory footprint.
3.  **Global Constants:** Moved pin numbers and timing delays to `const int` variables at the top of the script. This creates a "Single Source of Truth," allowing for system-wide updates with a single change.

### IV. Hardware Observations
* **Polarity:** Confirmed the Anode (longer leg) must be oriented toward the positive potential to complete the circuit.
* **Troubleshooting:** Verified connections by checking for "Bridges" across the center divot and ensuring common ground across the circuit.

### V. Evolution of the Code: Three Versions of Optimization

To demonstrate the transition from basic functionality to professional-grade architecture, I developed three versions of the SOS signal program.

#### Version 1: Linear Execution (The Baseline)
In this version, every action is hard-coded. While easy to write initially, it lacks scalability and is prone to errors during manual updates.
* **Logic:** Sequential `digitalWrite` and `delay` calls.
* **Complexity:** O(n) lines of code relative to signal length.

#### Version 2: Control Structures (The For-Loop)
I identified the repetitive nature of the "S" and "O" patterns and implemented `for` loops. This significantly reduced the line count and improved readability.
* **Optimization:** Used a loop counter (`int i = 0; i < 3; i++`) to handle triplets.
* **Improvement:** Reduced code redundancy by 60%.

#### Version 3: Procedural Abstraction (The Modular Approach)
The final optimization involved moving hardware-specific logic into custom functions. This represents a "Clean Code" approach used in professional embedded systems.
* **Strategy:** Created `sendDit()` and `sendDah()` functions.
* **Benefit:** The `loop()` function now reads like a logical sentence. This abstraction allows for easy modification of the signal (e.g., changing from SOS to another Morse sequence) without rewriting core logic.
* **Constants:** Implemented `const int` for pin assignments and timing to ensure a "Single Source of Truth."

**Final Optimized Script Architecture:**
```cpp
// Global Constants for easy configuration
const int LED_PIN = 13;
const int DIT = 250; 
const int DAH = 500;

void setup() {
  pinMode(LED_PIN, OUTPUT);
}

void sendDit() {
  digitalWrite(LED_PIN, HIGH);
  delay(DIT);
  digitalWrite(LED_PIN, LOW);
  delay(DIT);
}

void sendDah() {
  digitalWrite(LED_PIN, HIGH);
  delay(DAH);
  digitalWrite(LED_PIN, LOW);
  delay(DAH);
}

void loop() {
  for (int i = 0; i < 3; i++) { sendDit(); } // S
  for (int i = 0; i < 3; i++) { sendDah(); } // O
  for (int i = 0; i < 3; i++) { sendDit(); } // S
  
  delay(3000); 
}
