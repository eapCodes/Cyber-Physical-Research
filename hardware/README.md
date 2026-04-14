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
```

### VII. Supplemental Analysis: Worst-Case Circuit Design
In a professional engineering environment, systems are often designed around "Worst-Case" scenarios to ensure long-term hardware reliability and fail-safe operation.

#### 1. The 625Ω Conservative Margin
While standard calculations suggest a 220Ω resistor for maximum brightness, I explored the rationale for a **625Ω** implementation. 
* **The Goal:** Limit current draw to a strict **8mA maximum**.
* **The Calculation:** $R = V_{source} / I_{max} \rightarrow 5V / 0.008A = 625\Omega$.
* **The Logic:** This calculation assumes the LED has failed and turned into a short circuit (0V drop). By using 625Ω, the Arduino GPIO pin is protected from overcurrent even in the event of a total component failure.

#### 2. Engineering Trade-offs: Reliability vs. Performance
I documented the two primary approaches to current limiting:
* **Standard Performance (220Ω - 437Ω):** Prioritizes luminosity and standard operating current (~15-20mA). 
* **High Reliability (625Ω+):** Prioritizes the absolute lifespan of the microcontroller. By running at 8mA, the thermal stress on the IC (Integrated Circuit) is minimized, though the LED output is reduced.

#### 3. Component Tolerance & Safety Factors
Resistors typically have a 5% or 10% tolerance. I noted that a "calculated" safe value like 437Ω might actually be lower in physical reality due to manufacturing variance. Choosing a 625Ω resistor provides a massive "Safety Factor," ensuring that even with low-tolerance components, the current never exceeds the safe threshold of the Arduino R4.

---

## Lesson 3: Multi-Component Arrays & Scalable Logic
**Date:** April 11, 2026

### I. Circuit Architecture (The 4-LED Array)
For this lesson, I expanded from a single LED to a 4-component array. This required a transition from basic point-to-point wiring to a more organized "Bus" architecture.

* **Common Ground Bus:** Utilized the blue breadboard rail to tie all LED cathodes to a single Ground (GND) return path. 
* **Discrete Current Limiting:** Following the "Worst-Case Design" principles from Lesson 2, I assigned a dedicated resistor to each LED branch. This prevents "Thermal Runaway"—a scenario where one LED draws too much current, fails, and causes a domino effect across the remaining components.
* **Pin Mapping:** Selected Digital Pins 2, 3, 4, and 5. This avoids Pins 0 and 1 (reserved for Serial RX/TX) and provides a contiguous range for easier programmatic indexing.

### II. Algorithmic Improvements
As a Computer Science student, I refactored the control logic to handle multiple outputs efficiently.

1. **Array Implementation:** Instead of declaring four separate integers, I stored the pin numbers in an array: `int ledPins[] = {2, 3, 4, 5};`.
2. **Setup Optimization:** Used a `for` loop in `void setup()` to initialize all pins as `OUTPUT` in just three lines of code.
3. **Sequence Logic:** Experimented with "Chasing" patterns, which introduced the concept of iterating through an array index to control hardware state.

### III. Hardware Checklist (Pre-Flight)
Before powering the board, I performed a technical audit:
* **Trace Analysis:** Verified each LED Anode (long leg) has a direct path to its assigned GPIO pin.
* **Bridge Check:** Ensured no metal legs were touching adjacent rows, which would cause a short circuit.
* **Load Calculation:** 4 LEDs at ~15mA each = 60mA total. This is well within the Arduino R4's aggregate current limit (~200mA).

### IV. Implementation: Sequential LED Control
**Objective:** Program a 4-LED "Chasing" sequence across Digital Pins 2-5.

#### Initial Functional Code
This version uses a linear approach to verify hardware connectivity and timing for each individual LED branch.

```cpp
void setup() {
  // Initializing consecutive pins 2-5 as OUTPUT
  pinMode(2, OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
}

void loop() {
  // Sequence: LED 2 -> 3 -> 4 -> 5
  digitalWrite(2, HIGH);
  delay(500);
  digitalWrite(2, LOW);

  digitalWrite(3, HIGH);
  delay(500);
  digitalWrite(3, LOW);

  digitalWrite(4, HIGH);
  delay(500);
  digitalWrite(4, LOW);

  digitalWrite(5, HIGH);
  delay(500);
  digitalWrite(5, LOW);
}
```

---

## Lesson 4: Circuit Topology & Professional Breadboarding
**Date:** April 12, 2026

### I. Physical Layout Optimization
This lesson focused on transitioning from "functional" wiring to "professional" wiring. A clean breadboard isn't just for looks; it reduces "mental load" during debugging and prevents accidental shorts.

* **Common Ground Bus:** Utilized the blue breadboard rail to tie all LED cathodes to a single Ground (GND) return path. This is a fundamental concept in PCB design—sharing a return path to simplify routing.
* **Component Orientation:** Standardized the direction of all LEDs and resistors. Aligning components in the same direction allows for a "quick-glance" audit of circuit polarity.
* **Spatial Management:** Positioned components to be "packed but not touching." This keeps the footprint small while ensuring no metal legs bridge across adjacent rows.

### II. Engineering Decisions & Constraints
* **Pin Sequencing:** Refactored the hardware to use an ascending pin sequence (2, 3, 4, 5). This aligns the physical wires with programmatic array indexing, making the code more intuitive.
* **Resource Preservation:** While the lesson suggested cutting resistor legs for a lower profile, I made the strategic decision to keep the full lead length. 
  * **Rationale:** As a student with a limited component kit, preserving the structural integrity of the resistors allows for maximum flexibility in future, larger-scale builds. This is a practical example of **Resource Management**.

### III. Hardware Audit
Checked for "Bridges" (accidental connections) across the center divot and confirmed that each of the 4 LED branches has a dedicated current-limiting resistor to prevent "Thermal Runaway" (where one failing LED kills the rest of the circuit).
---

## Lesson 5: Binary Theory & The Digital Foundation
**Date:** April 12, 2026

### I. The Universal Language of Numbers
This lesson bridged the gap between physical hardware and Computer Science theory. I explored the concept that all computer data—from simple text to high-definition video—is fundamentally numerical.
* **The Logic Gate:** At the lowest hardware level, everything is controlled by physical "switches" in a state of **0 (Off)** or **1 (On)**.
* **Moore's Law:** Observed the industry trend where transistor density doubles roughly every 18 months, allowing for the massive processing power we see in modern microcontrollers like the Arduino R4.

### II. Binary Mathematics (Base-2)
I practiced the "Carry the One" logic in Binary, comparing it to our standard Decimal (Base-10) system. Understanding how bits ($2^n$) represent values is essential for efficient embedded programming.

**Binary-to-Decimal Mapping used in Lab:**
| Bit Weight | Pin Assignment | Decimal Value |
| :--- | :--- | :--- |
| $2^3$ | Pin 2 | 8 |
| $2^2$ | Pin 3 | 4 |
| $2^1$ | Pin 4 | 2 |
| $2^0$ | Pin 5 | 1 |

### III. Homework: 4-Bit LED Counter
**Objective:** Program the 4-LED array to visually represent a binary counter from 0 to 15.

#### Implementation: Manual Logic Verification
I chose to write out the full binary sequence manually (Brute Force). This ensured 100% accuracy in bit-weighting and hardware mapping before moving toward programmatic optimization. 

*Note: For the sake of readability in this documentation, I have included a snippet of the logic below. The full 16-state implementation was verified on hardware.*

```cpp
void setup() {
  pinMode(2, OUTPUT); // MSB (8)
  pinMode(3, OUTPUT); // (4)
  pinMode(4, OUTPUT); // (2)
  pinMode(5, OUTPUT); // LSB (1)
}

void loop() {
  // Logic for Decimal 3 (Binary 0011)
  digitalWrite(4, HIGH); // bit 2^1
  digitalWrite(5, HIGH); // bit 2^0
  delay(250);
  digitalWrite(4, LOW);
  digitalWrite(5, LOW);
  delay(1000);

  // Logic for Decimal 7 (Binary 0111)
  digitalWrite(3, HIGH); // bit 2^2
  digitalWrite(4, HIGH); // bit 2^1
  digitalWrite(5, HIGH); // bit 2^0
  delay(250);
  digitalWrite(3, LOW);
  digitalWrite(4, LOW);
  digitalWrite(5, LOW);
  delay(1000);
}
```
---

## Lesson 6: 5-Bit Binary Counter Implementation
**Date:** April 13, 2026

## Class Notes & Review
* **Objective:** Review the breadboard circuit and expand the binary counter capacity.
* **Progress:** The course is currently reviewing the physical circuit and the logic used in previous steps. 
* **Focus:** Binary math theory is scheduled for next week; this week is about hardware execution and manual bit mapping.
* **Homework:** Add a 5th LED to the circuit to increase the counting range.

## Hardware Configuration
With the addition of the 5th LED, the bit weightings are now defined as:
* **LED 5:** 16 (Pin 2)
* **LED 4:** 8 (Pin 3)
* **LED 3:** 4 (Pin 4)
* **LED 2:** 2 (Pin 5)
* **LED 1:** 1 (Pin 6)
* **Total Capacity:** 0 to 31 ($2^5 - 1$)

---

## Circuit Code

```cpp
/*
 * Lesson 6: 5-Bit Binary Counter
 * Based on manual pattern mapping (16, 8, 4, 2, 1)
 */

void setup() {
  // Setup the 5 pins used for the LEDs
  pinMode(2, OUTPUT); // 16s
  pinMode(3, OUTPUT); // 8s
  pinMode(4, OUTPUT); // 4s
  pinMode(5, OUTPUT); // 2s
  pinMode(6, OUTPUT); // 1s
}

void loop() {
  // Manual count sequence example
  // 0: (0,0,0,0,0)
  displayBinary(0, 0, 0, 0, 0);
  delay(1000);

  // 1: (0,0,0,0,1)
  displayBinary(0, 0, 0, 0, 1);
  delay(1000);

  // 16: (1,0,0,0,0)
  displayBinary(1, 0, 0, 0, 0);
  delay(1000);

  // 31: (1,1,1,1,1)
  displayBinary(1, 1, 1, 1, 1);
  delay(1000);
}

// Function to translate 1s and 0s to Pin High/Low
void displayBinary(int b16, int b8, int b4, int b2, int b1) {
  digitalWrite(2, b16); 
  digitalWrite(3, b8); 
  digitalWrite(4, b4); 
  digitalWrite(5, b2); 
  digitalWrite(6, b1); 
}
```
---
# Lesson 7: Binary Foundations & Computational Logic
**Date:** April 13, 2026  
**Category:** Embedded Systems Architecture

---

## Objective
To master binary arithmetic (Base-2) at the logic level. This foundation is essential for understanding how the **ARM Cortex-M4** processor handles data, manages memory, and mitigates risks such as integer overflows in industrial control loops.

---

## Technical Task: Manual Binary Multiplication
**Problem Statement:** Multiply `1001` (Decimal 9) by `1011` (Decimal 11).

### Execution (Shift and Add Algorithm)
This exercise simulates the behavior of an **Arithmetic Logic Unit (ALU)** performing multiplication without higher-level abstraction.

| Logic Step | Operation | Binary Result |
| :--- | :--- | :--- |
| **Input A** | Base Value | `1001` (9) |
| **Input B** | Multiplier | `1011` (11) |
| **Partial 1** | $1001 \times 1$ | `00001001` |
| **Partial 2** | $1001 \times 1$ (Shift Left 1) | `00010010` |
| **Partial 3** | $1001 \times 0$ (Shift Left 2) | `00000000` |
| **Partial 4** | $1001 \times 1$ (Shift Left 3) | `01001000` |
| **Final Sum** | **Total Accumulation** | **`01100011`** |

**Verification:** $01100011_2 = 64 + 32 + 2 + 1 = \mathbf{99_{10}}$
---
# Lesson 8: Circuit Pre-Visualization & Variable Architecture
**Date:** April 14, 2026  
**Category:** Embedded Systems Logic

---

## Objective
Transitioning from hard-coded literals to dynamic, variable-based architecture. This lesson focuses on circuit planning and the use of descriptive identifiers to increase code portability and system resilience.

---

## Schematic Analysis & Hardware Protection
* **Pre-Prototyping:** Calculated hardware requirements and load limits prior to physical assembly to mitigate the risk of over-current.
* **Current Limiting:** Integrated 1,000Ω resistors in series with the LED components to ensure I/O pin current remains within the safe operating area (SOA) for the Arduino Uno R4.

---

## Implementation: Multi-Channel Timing Control
The following source code demonstrates the removal of "magic numbers" in favor of global variables. This allows for centralized timing adjustments and improves long-term maintainability.

```cpp
int redLed=8;
int greenLed=7;
int greenWait=150;
int redWait=500;
int endwait=1000;

void setup() {
  // Initialization of I/O pins as Output
  pinMode(redLed, OUTPUT);
  pinMode(greenLed, OUTPUT);
}

void loop() {
  // Channel 1: High-Frequency Oscillation (5 Cycles)
  digitalWrite(greenLed, HIGH);
  delay(greenWait);
  digitalWrite(greenLed, LOW);
  delay(greenWait);
  // [Repeated for 5 cycles...]

  // Channel 2: Low-Frequency Oscillation (10 Cycles)
  digitalWrite(redLed, HIGH);
  delay(redWait);
  digitalWrite(redLed, LOW);
  delay(redWait);
  // [Repeated for 10 cycles...]

  delay(endwait); // System dwell time between cycles
}
```

