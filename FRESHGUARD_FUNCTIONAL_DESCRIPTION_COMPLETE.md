# Functional Description of the FreshGuard NH₃ Detection and Automatic Fragrance Dispensing System

The **FreshGuard NH₃ Detection and Automatic Fragrance Dispensing System** is an Arduino Uno-based prototype designed to continuously monitor ammonia (NH₃) concentration in comfort rooms using an MQ-135 gas sensor. The system automatically detects unpleasant odors, notifies users through visual and audio alerts, and activates an ultrasonic fragrance mist dispenser to improve indoor air quality. The system integrates an MQ-135 gas sensor, Arduino Uno, 16×2 I2C LCD, LED indicators, DFPlayer Mini audio module with speaker, relay module, and an ultrasonic mist maker to provide a fully automated odor detection and mitigation process.

---

## Table of Contents
1. [System Initialization](#1-system-initialization)
2. [Continuous Ammonia Monitoring](#2-continuous-ammonia-monitoring)
3. [Normal (Safe) Operation](#3-normal-safe-operation)
4. [Ammonia Detection and Alarm Activation](#4-ammonia-detection-and-alarm-activation)
5. [Voice Notification Function](#5-voice-notification-function)
6. [Automatic Fragrance Dispensing Cycle](#6-automatic-fragrance-dispensing-cycle)
7. [Continuous Automatic Cycling](#7-continuous-automatic-cycling)
8. [LCD Status Display](#8-lcd-status-display)
9. [LED Indicator Function](#9-led-indicator-function)
10. [Relay-Controlled Ultrasonic Mist Maker](#10-relay-controlled-ultrasonic-mist-maker)
11. [Non-Blocking Operation](#11-non-blocking-operation)
12. [Serial Monitor Logging](#12-serial-monitor-logging)
13. [Overall System Function](#overall-system-function)

---

## 1. System Initialization

Upon powering the system, the Arduino initializes all connected hardware components, including the MQ-135 gas sensor, LCD display, LEDs, DFPlayer Mini audio module, relay module, and ultrasonic mist maker. 

**Initialization Sequence:**

```
Time 0s:    Power applied to Arduino Uno R3
Time 0.1s:  GPIO pins configured as INPUT/OUTPUT
Time 0.2s:  I²C communication initialized with LCD
Time 0.5s:  DFPlayer Mini serial communication ready
Time 1.0s:  LCD displays splash screen: "FreshGuard NH3"
Time 1.5s:  LCD displays: "Starting..."
Time 2.0s:  MQ-135 sensor begins reading
Time 2.5s:  LCD clears and displays initial readings
Time 3.0s:  System enters IDLE state
```

**Component States During Initialization:**
- **LCD Display:** Shows startup messages, then displays "NH3: [value]" and "STATUS: SAFE"
- **Green LED:** Activated (HIGH voltage) to indicate standby mode
- **Red LED:** Deactivated (LOW voltage)
- **Relay:** Remains OFF (mist maker not powered)
- **DFPlayer Mini:** Ready to play audio notifications
- **Serial Monitor:** Outputs initialization status messages

The system is now ready to monitor ammonia concentration continuously.

---

## 2. Continuous Ammonia Monitoring

The Arduino continuously reads the analog output of the MQ-135 gas sensor through analog pin **A0**. The sensor converts the detected ammonia concentration into an analog value (0–1023), which is compared against two predefined thresholds to determine system response:

### Activation and Reset Thresholds

* **Activation Threshold (THRESHOLD_ON = 400):** 
  - When the sensor reading reaches or exceeds this value, the system identifies the presence of elevated ammonia levels and initiates odor mitigation.
  - Indicates unpleasant odor levels requiring intervention.

* **Reset Threshold (THRESHOLD_OFF = 350):** 
  - The system returns to normal operation only when the sensor reading falls below this value. 
  - Using separate activation and reset thresholds creates **hysteresis**, which prevents rapid switching caused by fluctuating sensor readings near the boundary.

### Hysteresis Benefit

The 50-point difference between THRESHOLD_ON (400) and THRESHOLD_OFF (350) creates a "dead zone" that prevents the system from rapidly toggling ON and OFF when ammonia levels fluctuate near the activation threshold.

```
Ammonia Level
    ↑
500 |        ┌─────────────────────────────
400 | ═══════ THRESHOLD_ON (Activate)  ────
350 | ═══════ THRESHOLD_OFF (Deactivate) 
300 |└────────────────────────────────
    |___________________________→ Time
    
State:  OFF   →    ON    →   OFF
Cycles: None     Misting    Waiting
```

**Sensor Reading Frequency:**
- Reads ammonia concentration approximately 100 times per second
- Averages multiple readings to reduce noise
- Typical reading range: 0–1023 ADC units

---

## 3. Normal (Safe) Operation

When the detected ammonia concentration remains below the activation threshold (< 350):

* The system stays in the **IDLE** state.
* The **green LED** remains illuminated, indicating acceptable air quality.
* The **red LED** remains OFF.
* The relay remains OFF, preventing activation of the ultrasonic mist maker.
* The LCD displays the current ammonia sensor value together with **"STATUS: SAFE"**.
* No voice notification is played.
* No power is consumed by the relay or mist maker.

**Example LCD Display (IDLE State):**
```
NH3: 285
STATUS: SAFE
```

**Operations During IDLE State:**
- Sensor continuously reads ammonia levels every ~10ms
- LCD updates display every 1 second
- Serial Monitor logs current reading and state
- System remains fully responsive to ammonia detection
- Minimal power consumption (only microcontroller and sensors active)

This indicates that the restroom environment is operating under normal conditions and no odor mitigation is required. The system continuously monitors and is ready to respond immediately if ammonia levels increase.

---

## 4. Ammonia Detection and Alarm Activation

When the MQ-135 sensor detects an ammonia concentration equal to or greater than **400**, the system immediately transitions from the **IDLE** state to the **CYCLE_ON** state.

**State Transition Trigger:**
```
Sensor Reading ≥ 400
        ↓
Check: Is current state IDLE?
        ↓
Yes: Initiate alarm sequence
     - Command DFPlayer Mini to play voice notification
     - Set relay HIGH (energize mist maker)
     - Turn ON red LED
     - Turn OFF green LED
     - Record state start time
     - Transition to CYCLE_ON state
```

During this stage:

* The **red LED** turns ON immediately.
* The **green LED** turns OFF immediately.
* The **DFPlayer Mini** plays a prerecorded voice notification through the speaker (played only once per alarm event).
* The relay activates the ultrasonic mist maker.
* The LCD displays an alarm message together with the remaining misting time.
* Serial Monitor logs: `[ALARM] Ammonia detected! Reading: 410 (Threshold: 400)`

**Example LCD Display (Beginning of Alarm):**
```
NH3: 418
STATUS: ALARM
```

**Immediate Response Timeline:**
```
Time (ms)  |  Event
0          |  Ammonia detected (≥ 400)
10         |  DFPlayer commanded to play voice
50         |  Relay energizes (relay clicks audible)
100        |  Red LED turns ON
100        |  Green LED turns OFF
200        |  LCD updates to show alarm status
500        |  Voice announcement begins playing
```

This immediate multi-sensory response alerts restroom users (through visual LED change, audio notification, and LCD display) while simultaneously beginning odor mitigation.

---

## 5. Voice Notification Function

To enhance user awareness and provide comprehensive feedback, FreshGuard incorporates a **DFPlayer Mini audio module** and speaker that provide an audible notification whenever elevated ammonia levels are first detected.

Once the ammonia concentration reaches the activation threshold, the Arduino commands the DFPlayer Mini to play the prerecorded announcement:

> **"Hello, mga Deewhalians! FreshGuard has detected an unpleasant odor. Upang mapanatiling malinis, presko, at mabango ang ating comfort room, awtomatikong maglalabas ang FreshGuard ng fragrance mist. Paalala lamang, sama-sama nating panatilihing malinis ang ating comfort room para sa komportable at kaaya-ayang kapaligiran. Maraming salamat, at magandang araw!"**

**English Translation:**
> "Hello, everyone! FreshGuard has detected an unpleasant odor. To keep our comfort room clean, fresh, and fragrant, FreshGuard will automatically release fragrance mist. Just a reminder, let us together maintain the cleanliness of our comfort room for a comfortable and pleasant environment. Thank you, and have a good day!"

### Voice Notification Features

The announcement serves to:

* **Inform users** that elevated ammonia levels have been detected.
* **Notify users** that FreshGuard has automatically activated the fragrance mist dispenser.
* **Encourage users** to maintain cleanliness and proper hygiene.
* **Increase awareness** through an audible alert in addition to visual indicators.
* **Provide feedback** that the system is functioning correctly.

### Single Notification Strategy

To prevent unnecessary repetition and audio fatigue:

* The voice notification is played **only once** when the system first enters the alarm condition.
* If the ammonia concentration remains elevated and the system cycles through multiple ON/OFF periods, the notification is NOT replayed during subsequent cycles.
* After the ammonia concentration decreases below the reset threshold and the system returns to IDLE, the notification is reset and becomes ready to play again during the next ammonia detection event.

**DFPlayer Control Code:**
```cpp
#include <SoftwareSerial.h>

SoftwareSerial dfplayerSerial(10, 11);  // RX, TX

void play_voice_notification() {
  // Send command to play track 1 on DFPlayer Mini
  // Command format: AA 55 03 03 00 00 01 FE FF FA
  dfplayerSerial.write(0xAA);  // Start byte
  dfplayerSerial.write(0x55);  // Version byte
  dfplayerSerial.write(0x03);  // Length
  dfplayerSerial.write(0x03);  // Play command
  dfplayerSerial.write(0x00);  // Parameter 1 (high byte)
  dfplayerSerial.write(0x00);  // Parameter 1 (low byte)
  dfplayerSerial.write(0x01);  // Track number
  dfplayerSerial.write(0xFE);  // Checksum high
  dfplayerSerial.write(0xFF);  // Checksum low
  dfplayerSerial.write(0xFA);  // End byte
}
```

---

## 6. Automatic Fragrance Dispensing Cycle

Following the voice notification, the relay energizes the ultrasonic mist maker to begin odor mitigation.

Instead of operating continuously, FreshGuard employs a timed misting cycle that reduces fragrance consumption while maintaining effective odor control.

### Misting ON Phase (20 Seconds)

During the **CYCLE_ON** state:

* **Relay:** ON (energized, contacts closed)
* **Ultrasonic mist maker:** Operating at full power
* **Fragrance mist:** Released continuously for **20 seconds**
* **Sensor:** Continues reading ammonia levels
* **LCD:** Updates every second showing remaining dispensing time

**ON Cycle Operations:**
```cpp
if (state == CYCLE_ON) {
  unsigned long elapsed = millis() - cycle_start_time;
  
  // Relay remains ON
  digitalWrite(RELAY_PIN, HIGH);
  
  // Update LCD every 1 second
  if (elapsed % 1000 < previous_elapsed % 1000) {
    int remaining = (20000 - elapsed) / 1000;
    lcd.setCursor(0, 1);
    lcd.print("MIST ON ");
    lcd.print(remaining);
    lcd.print("s ALARM");
  }
  
  // Check if 20 seconds elapsed
  if (elapsed >= 20000) {
    // Transition to CYCLE_OFF
    state = CYCLE_OFF;
    cycle_start_time = millis();
  }
}
```

**Example LCD Display (CYCLE_ON):**
```
NH3: 430
MIST ON 18s ALARM
```

**ON Cycle Timeline:**
```
Time (s)  |  Relay  |  Mist Maker  |  LCD Display           |  Action
0–1       |  ON     |  Running     |  MIST ON 20s ALARM     |  Initial misting
2–5       |  ON     |  Running     |  MIST ON 18s ALARM     |  Continuous misting
6–10      |  ON     |  Running     |  MIST ON 14s ALARM     |  Mid-cycle
11–18     |  ON     |  Running     |  MIST ON 7s ALARM      |  Final phase
19–20     |  ON     |  Running     |  MIST ON 1s ALARM      |  Near end
20        |  OFF    |  Stopping    |  Transition to OFF     |  Misting complete
```

**Misting Effectiveness:**
- Ultrasonic element vibrates at ~120 kHz
- Water atomized into 1–5 μm particles (ultra-fine)
- Fragrance distribution: ~20–30 mL per minute
- 20-second cycle releases ~6–10 mL of fragrance
- Particles remain airborne for several minutes after misting stops

---

### Misting OFF Phase (10 Seconds)

After the 20-second misting period:

* **Relay:** OFF (de-energized, contacts open)
* **Mist generation:** Temporarily stops completely
* **System state:** Enters **CYCLE_OFF** for **10 seconds**
* **LCD:** Shows countdown during waiting period
* **Sensor:** Continues to monitor ammonia levels

This pause serves multiple purposes:

* Allows fragrance droplets to settle and disperse throughout the room
* Reduces power consumption and extends mist maker lifespan
* Provides acoustic relief from the mist maker's humming sound
* Allows sensor readings to stabilize for accurate next-cycle decision

**OFF Cycle Operations:**
```cpp
if (state == CYCLE_OFF) {
  unsigned long elapsed = millis() - cycle_start_time;
  
  // Relay remains OFF
  digitalWrite(RELAY_PIN, LOW);
  
  // Update LCD every 1 second
  if (elapsed % 1000 < previous_elapsed % 1000) {
    int remaining = (10000 - elapsed) / 1000;
    lcd.setCursor(0, 1);
    lcd.print("MIST OFF ");
    lcd.print(remaining);
    lcd.print("s ALARM ");
  }
  
  // Check if 10 seconds elapsed
  if (elapsed >= 10000) {
    // Re-evaluate ammonia levels and decide next action
    evaluate_next_cycle();
  }
}
```

**Example LCD Display (CYCLE_OFF):**
```
NH3: 425
MIST OFF 9s ALARM
```

**OFF Cycle Timeline:**
```
Time (s)  |  Relay  |  Mist Maker  |  LCD Display           |  Action
0–2       |  OFF    |  Stopped     |  MIST OFF 10s ALARM    |  Initial rest
3–5       |  OFF    |  Stopped     |  MIST OFF 7s ALARM     |  Mid-rest
6–8       |  OFF    |  Stopped     |  MIST OFF 2s ALARM     |  Near end of rest
9–10      |  OFF    |  Stopped     |  MIST OFF 1s ALARM     |  Final second
10        |  (eval) |  (stopped)   |  Evaluate ammonia      |  Decision point
```

---

## 7. Continuous Automatic Cycling

After every 10-second OFF period, the system evaluates the ammonia concentration again and makes an intelligent decision about the next operational phase.

### Decision Logic at End of OFF Cycle

```
                    ┌─────────────────────┐
                    │ End of OFF cycle    │
                    │ (10 seconds passed) │
                    └──────────┬──────────┘
                               │
                    ┌──────────v──────────┐
                    │ Read current NH₃    │
                    │ sensor value        │
                    └──────────┬──────────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
        ┌───────v────────┐ ┌──v──────────┐ ┌─v──────────────┐
        │  ≥ 400         │ │350–399      │ │ < 350          │
        │(Still High)    │ │(Elevated)   │ │(Normal)        │
        └────┬────────────┘ └──┬──────────┘ └─┬──────────────┘
             │                  │              │
      ┌──────v──────────┐ ┌─────v────────┐ ┌──v──────────┐
      │ START CYCLE_ON  │ │STAY CYCLE_OFF │ │  → IDLE    │
      │ (20s mist)      │ │(wait 10s more)│ │ (normal)   │
      │ Relay: ON       │ │ Relay: OFF    │ │Relay: OFF  │
      │ Red LED: ON     │ │ Red LED: ON   │ │Green LED:ON│
      │ Cycle +1        │ │ Continue wait │ │Red LED: OFF│
      └─────────────────┘ └───────────────┘ └────────────┘
```

### Three Possible Outcomes

**Outcome 1: Ammonia Still High (≥ 400)**

* **Action:** Immediately begin another 20-second misting cycle
* **Relay:** Turns ON again
* **LCD Display:** Changes to "MIST ON 20s ALARM"
* **Cycle Counter:** Increments (cycle 1 → cycle 2, etc.)
* **Purpose:** Continue odor mitigation while ammonia remains elevated

```cpp
if (current_reading >= THRESHOLD_ON) {
  state = CYCLE_ON;
  cycle_start_time = millis();
  cycleCount++;
  digitalWrite(RELAY_PIN, HIGH);
  Serial.println("[CYCLE] Ammonia still high - repeating cycle");
}
```

**Outcome 2: Ammonia Elevated But Below Trigger (350–399)**

* **Action:** Remain in OFF phase and reset the waiting timer
* **Relay:** Stays OFF
* **LCD Display:** Resets countdown "MIST OFF 10s ALARM"
* **Duration:** Wait another full 10 seconds before reassessing
* **Purpose:** Prevent unnecessary misting while still monitoring for dangerous levels

```cpp
else if (current_reading >= THRESHOLD_OFF) {
  state = CYCLE_OFF;
  cycle_start_time = millis();  // Reset timer
  Serial.println("[WAIT] Ammonia elevated - waiting for recovery");
}
```

**Outcome 3: Ammonia Returned to Normal (< 350)**

* **Action:** Exit alarm mode and return to IDLE state
* **Relay:** Turns OFF (if not already)
* **Red LED:** Turns OFF
* **Green LED:** Turns ON
* **LCD Display:** Changes to "STATUS: SAFE"
* **Serial Output:** Logs successful recovery and total cycles used

```cpp
else {
  state = IDLE;
  cycle_start_time = millis();
  digitalWrite(RELAY_PIN, LOW);
  digitalWrite(RED_LED_PIN, LOW);
  digitalWrite(GREEN_LED_PIN, HIGH);
  Serial.println("[IDLE] Air quality restored - returning to normal");
  Serial.print("[INFO] Total misting cycles: ");
  Serial.println(cycleCount);
}
```

### Benefits of This Approach

✅ **Efficient Resource Usage** – Fragrance used only as needed  
✅ **Adaptive Response** – Continues cycling if problem persists  
✅ **Intelligent Evaluation** – Uses hysteresis to prevent oscillation  
✅ **User-Friendly** – Returns to normal automatically when air clears  
✅ **Datalogging** – Tracks number of cycles for analysis  

This hysteresis-based control strategy minimizes unnecessary relay switching while ensuring that odor mitigation continues whenever ammonia concentration remains elevated.

---

## 8. LCD Status Display

The 16×2 I2C LCD provides continuous real-time feedback by displaying:

* Current ammonia sensor reading (numeric value 0–1023)
* System operating status (SAFE, ALARM, or countdown)
* Remaining misting countdown during ON and OFF cycles
* All information updates every second

### Display Structure

The LCD uses a two-line format:

**Line 1:** Ammonia reading
```
NH3: ###     (### = numeric reading from 0-1023)
```

**Line 2:** Status information
```
STATUS: SAFE         (during IDLE state)
STATUS: ALARM        (when alarm first triggered)
MIST ON ##s ALARM    (during 20s misting phase)
MIST OFF ##s ALARM   (during 10s rest phase)
```

### Possible Display States

**State 1: Normal Operation (IDLE)**
```
NH3: 285
STATUS: SAFE
```
- Meaning: Air quality is acceptable
- Green LED: ON
- Red LED: OFF
- Relay: OFF
- Actions: Continuous monitoring only

**State 2: Alarm Detection (Start of CYCLE_ON)**
```
NH3: 418
STATUS: ALARM
```
- Meaning: High ammonia detected, beginning response
- Green LED: OFF
- Red LED: ON
- Relay: Just turned ON
- Actions: Voice notification playing, mist starting
- Duration: Display for 1 second then changes to countdown

**State 3: Misting Active (During CYCLE_ON)**
```
NH3: 430
MIST ON 15s ALARM
```
- Meaning: Fragrance mist currently releasing
- Green LED: OFF
- Red LED: ON
- Relay: ON
- Actions: Mist maker operating
- Countdown: Updates every second (20s → 19s → ... → 1s → 0s)

**State 4: Rest Period (During CYCLE_OFF)**
```
NH3: 425
MIST OFF 7s ALARM
```
- Meaning: Mist stopped, resting before next decision
- Green LED: OFF
- Red LED: ON (still in alarm)
- Relay: OFF
- Actions: Waiting for ammonia to decrease or preparing for next cycle
- Countdown: Updates every second (10s → 9s → ... → 1s → 0s)

**State 5: Returning to Safe (End of CYCLE_OFF if ammonia < 350)**
```
NH3: 340
STATUS: SAFE
```
- Meaning: Air quality restored to acceptable level
- Green LED: ON (just turned back on)
- Red LED: OFF (just turned off)
- Relay: OFF
- Actions: System returning to idle monitoring

### LCD Update Function

```cpp
void update_lcd() {
  static unsigned long lastUpdate = 0;
  unsigned long now = millis();
  
  // Update every 1000 milliseconds (1 second)
  if (now - lastUpdate >= 1000) {
    lastUpdate = now;
    
    int reading = read_nh3_sensor();
    
    // Line 1: Always show ammonia reading
    lcd.setCursor(0, 0);
    lcd.print("NH3: ");
    if (reading < 100) lcd.print(" ");    // Padding for alignment
    if (reading < 10) lcd.print(" ");     // Padding for alignment
    lcd.print(reading);
    lcd.print("     ");  // Clear any leftover characters
    
    // Line 2: Status depends on current state
    lcd.setCursor(0, 1);
    
    if (state == IDLE) {
      lcd.print("STATUS: SAFE   ");
      
    } else if (state == CYCLE_ON) {
      unsigned long elapsed = now - stateStartTime;
      int remaining = (20000 - elapsed) / 1000;
      lcd.print("MIST ON ");
      if (remaining < 10) lcd.print(" ");  // Padding
      lcd.print(remaining);
      lcd.print("s ALARM");
      
    } else if (state == CYCLE_OFF) {
      unsigned long elapsed = now - stateStartTime;
      int remaining = (10000 - elapsed) / 1000;
      lcd.print("MIST OFF ");
      if (remaining < 10) lcd.print(" "); // Padding
      lcd.print(remaining);
      lcd.print("s    ");
    }
  }
}
```

---

## 9. LED Indicator Function

The LED indicators provide immediate visual feedback regarding air quality status. They are the fastest way to understand the system's current condition without reading the LCD.

### LED States and Meanings

| Green LED | Red LED | System Condition | Meaning |
|-----------|---------|------------------|---------|
| ON (steady) | OFF | IDLE (safe) | Normal air quality - no action needed |
| OFF | ON (steady) | CYCLE_ON (misting) | Elevated ammonia detected - mist actively releasing |
| OFF | ON (steady) | CYCLE_OFF (waiting) | Alarm acknowledged - resting before next cycle |

### Visual Design

**LED Brightness and Appearance:**
- Both LEDs use 220Ω current-limiting resistors
- Brightness is dim-to-moderate (safe for extended viewing)
- No flashing or rapid blinking (steady illumination)
- Clear color contrast between green (safe) and red (alarm)

### LED Control Functions

```cpp
#define GREEN_LED_PIN 5
#define RED_LED_PIN 6

void turn_on_green_led() {
  digitalWrite(GREEN_LED_PIN, HIGH);
  Serial.println("[LED] Green ON");
}

void turn_off_green_led() {
  digitalWrite(GREEN_LED_PIN, LOW);
  Serial.println("[LED] Green OFF");
}

void turn_on_red_led() {
  digitalWrite(RED_LED_PIN, HIGH);
  Serial.println("[LED] Red ON");
}

void turn_off_red_led() {
  digitalWrite(RED_LED_PIN, LOW);
  Serial.println("[LED] Red OFF");
}

void emergency_blink_red() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(RED_LED_PIN, HIGH);
    delay(200);
    digitalWrite(RED_LED_PIN, LOW);
    delay(200);
  }
}
```

### User Interface Guidance

**For System Users:**
- **Green ON, Red OFF:** Everything normal, breathe easy ✓
- **Red ON:** Odor detected, fragrance being released ⚠️
- **Both OFF:** This should not happen (error condition)

**For Maintenance Personnel:**
- Easy visual confirmation that system is operating
- Red LED blinking pattern indicates error state
- LED brightness indicates system power and functionality

---

## 10. Relay-Controlled Ultrasonic Mist Maker

The relay module functions as an electronically controlled switch that supplies power to the ultrasonic mist maker. This configuration is essential because:

1. **Electrical Isolation:** The Arduino (5V/40mA max per pin) cannot directly power the mist maker (5V/2.5–5W)
2. **Safe Operation:** Relay prevents high-current feedback into Arduino circuits
3. **Automated Control:** Arduino can safely control mist maker on/off cycles
4. **Longevity:** Protects Arduino from damage due to load variations

### Relay Operation Modes

**Relay ON (Digital Output D7 = HIGH, ~5V)**

When the Arduino sets pin D7 to HIGH:

* Relay coil energizes (electromagnet pulls contacts)
* Audible *click* sound confirms relay activation
* Internal switch contacts CLOSE
* Power is delivered from Power Bank to mist maker positive terminal
* Mist maker circuit completes: +5V → Mist Maker → Ground
* Ultrasonic piezo element begins vibrating at ~120 kHz
* Water in chamber is atomized into fine mist (1–5 μm particles)
* Mist is dispersed through outlet nozzle
* Visible mist appears within 1–2 seconds

```
Arduino D7 = HIGH
        ↓
Relay Coil Energizes
        ↓
Contacts Close
        ↓
Power Bank +5V ──→ Mist Maker ──→ Ground
        ↓
Ultrasonic Element Vibrates
        ↓
Water Atomized
        ↓
Fragrance Mist Released
```

**Relay OFF (Digital Output D7 = LOW, ~0V)**

When the Arduino sets pin D7 to LOW:

* Relay coil de-energizes (electromagnetic field collapses)
* Spring returns contacts to OPEN position
* Audible *click* confirms relay deactivation
* Power is cut from mist maker circuit
* Ultrasonic vibration stops immediately
* Mist generation ceases within milliseconds
* Residual particles settle and disperse

```
Arduino D7 = LOW
        ↓
Relay Coil De-energizes
        ↓
Contacts Open (Spring returns)
        ↓
Power to Mist Maker Disconnected
        ↓
Ultrasonic Element Stops
        ↓
Mist Generation Ceases
```

### Relay Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| Control Voltage | 5V DC | Same as Arduino supply |
| Signal Pin | D7 | Single GPIO pin |
| Rated Current | 10A @ 250V AC or 30V DC | Safe for 5V/5W load |
| Coil Resistance | ~200–500Ω | Determines activation current |
| Response Time | 5–10 ms | Nearly instant activation |
| Contact Bounce | <2 ms | Negligible in 20s cycles |
| Isolation | Electrical | Arduino isolated from high current |

### Relay Control Code

```cpp
#define RELAY_PIN 7

void relay_on() {
  digitalWrite(RELAY_PIN, HIGH);
  Serial.println("[RELAY] ON - Mist maker activated");
}

void relay_off() {
  digitalWrite(RELAY_PIN, LOW);
  Serial.println("[RELAY] OFF - Mist maker deactivated");
}

void relay_pulse(unsigned long duration_ms) {
  relay_on();
  delay(duration_ms);
  relay_off();
}

void activate_fragrance_cycle() {
  relay_on();
  delay(20000);  // 20-second ON period
  relay_off();
  delay(10000);  // 10-second OFF period
}
```

### Ultrasonic Mist Maker Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| Operating Voltage | 5V DC | Via relay from power bank |
| Power Consumption | 2.5–5W | Typical 3–4W operation |
| Vibration Frequency | ~120 kHz | Ultrasonic (inaudible to humans) |
| Water Tank Capacity | 40–50 mL | ~2 tablespoons |
| Mist Output | 20–30 mL/minute | ~6–10 mL per 20s cycle |
| Particle Size | 1–5 μm | Ultra-fine cold mist |
| Warm-up Time | <1 second | Instant activation |
| Operating Temperature | 5–60°C | Standard room conditions |

---

## 11. Non-Blocking Operation

FreshGuard utilizes a **millis()-based non-blocking state machine** instead of long `delay()` commands to control timing. This is a critical architectural decision that enables reliable, responsive operation.

### Why Non-Blocking is Essential

**The Problem with `delay()`:**
```cpp
// PROBLEMATIC CODE - DO NOT USE FOR FreshGuard
void loop() {
  read_ammonia();
  
  if (ammonia > 400) {
    digitalWrite(RELAY, HIGH);
    delay(20000);  // ❌ BLOCKS for 20 seconds!
                   // ❌ Cannot read sensor during delay
                   // ❌ Cannot update LCD during delay
                   // ❌ Cannot detect changes during delay
                   // ❌ System appears frozen
    digitalWrite(RELAY, LOW);
    delay(10000);  // ❌ BLOCKS for 10 seconds!
  }
}
```

During the 20-second `delay()`:
- ❌ Ammonia sensor NOT read (missed changes!)
- ❌ LCD NOT updated (frozen display)
- ❌ User input NOT processed
- ❌ System appears unresponsive
- ❌ Safety hazard if ammonia suddenly increases

**The Solution: Non-Blocking millis() Timer:**
```cpp
// CORRECT CODE - Non-blocking state machine
unsigned long cycleStartTime = 0;
const unsigned long MIST_ON_TIME = 20000;   // 20 seconds
const unsigned long MIST_OFF_TIME = 10000;  // 10 seconds

void loop() {
  unsigned long now = millis();
  int ammonia = read_ammonia();  // ✓ Always read
  
  if (state == IDLE && ammonia >= 400) {
    state = CYCLE_ON;
    cycleStartTime = now;
    digitalWrite(RELAY, HIGH);
  }
  
  if (state == CYCLE_ON) {
    if (now - cycleStartTime >= MIST_ON_TIME) {
      state = CYCLE_OFF;
      cycleStartTime = now;
      digitalWrite(RELAY, LOW);
    }
  }
  
  if (state == CYCLE_OFF) {
    if (now - cycleStartTime >= MIST_OFF_TIME) {
      if (ammonia >= 400) {
        state = CYCLE_ON;
        cycleStartTime = now;
        digitalWrite(RELAY, HIGH);
      } else if (ammonia < 350) {
        state = IDLE;
        digitalWrite(RELAY, LOW);
      }
    }
  }
  
  update_lcd(ammonia);     // ✓ Always update
  update_leds();           // ✓ Always update
  update_serial();         // ✓ Always update
}
```

### How millis() Timer Works

```cpp
// Current milliseconds since Arduino started
unsigned long now = millis();

// Record when an event started
unsigned long eventStart = millis();

// Later, check how much time has elapsed
unsigned long elapsed = millis() - eventStart;

// When enough time has passed, trigger action
if (elapsed >= 20000) {  // 20 seconds
  // Perform action
  // Reset timer if needed
  eventStart = millis();
}
```

### State Machine Architecture

```cpp
enum SystemState {
  IDLE,      // Normal monitoring
  CYCLE_ON,  // Misting active (20s)
  CYCLE_OFF  // Rest period (10s)
};

SystemState currentState = IDLE;
unsigned long stateStartTime = 0;

void update_state_machine() {
  unsigned long now = millis();
  unsigned long elapsed = now - stateStartTime;
  
  switch(currentState) {
    
    case IDLE:
      // Check for alarm condition
      if (ammonia >= THRESHOLD_ON) {
        currentState = CYCLE_ON;
        stateStartTime = now;
        activate_mist();
      }
      break;
      
    case CYCLE_ON:
      // Check if 20 seconds elapsed
      if (elapsed >= 20000) {
        currentState = CYCLE_OFF;
        stateStartTime = now;
        stop_mist();
      }
      break;
      
    case CYCLE_OFF:
      // Check if 10 seconds elapsed
      if (elapsed >= 10000) {
        // Re-evaluate and decide next state
        if (ammonia >= THRESHOLD_ON) {
          currentState = CYCLE_ON;
          stateStartTime = now;
          activate_mist();
        } else if (ammonia < THRESHOLD_OFF) {
          currentState = IDLE;
          stateStartTime = now;
          deactivate_system();
        } else {
          // Still in gray zone - wait more
          stateStartTime = now;  // Reset timer
        }
      }
      break;
  }
}
```

### Main Loop Structure

```cpp
void loop() {
  // Non-blocking execution: each operation < 10ms
  
  // 1. Update state machine (< 1ms)
  update_state_machine();
  
  // 2. Update LCD (if 1 second elapsed)
  update_lcd();
  
  // 3. Update Serial (if 1 second elapsed)
  update_serial();
  
  // 4. Small delay to prevent CPU overload
  delay(10);  // Allows millis() to increment
  
  // Loop runs ~100 times per second with 10ms delay
  // Total execution time: ~15–20ms per loop
  // CPU usage: ~2–5% (very efficient)
}
```

### Simultaneous Operations

With non-blocking design, the system can do EVERYTHING simultaneously:

✅ Continuously monitor ammonia levels (~100 reads/second)  
✅ Update LCD every 1 second  
✅ Control relay with precise 20s/10s timing  
✅ Update LEDs instantly  
✅ Process serial commands (if any)  
✅ Respond to alarm conditions immediately  
✅ Maintain accurate millis() counters  

### Timing Accuracy

```
millis() Timer Accuracy:
- Resolution: 1 millisecond
- Overflow: Every ~49 days (roll over to 0)
- Accuracy: ±1-2% (depends on crystal oscillator)

Example: 20-second mist cycle
- Theoretical: exactly 20,000ms
- Actual: 19,980ms to 20,020ms
- Acceptable: ✓ (0.1% error margin)

Non-blocking design ensures:
- No missed cycles
- No frozen system
- Responsive to all inputs
- Accurate timing
```

---

## 12. Serial Monitor Logging

For calibration, troubleshooting, and system evaluation, the Arduino continuously outputs diagnostic information to the Serial Monitor. This real-time feedback is essential for understanding system behavior.

### Serial Monitor Configuration

**Connection Settings:**
- **Port:** COM3 (or whatever port Arduino is using)
- **Baud Rate:** 9600 bits/second
- **Data Bits:** 8
- **Stop Bits:** 1
- **Parity:** None
- **Flow Control:** None

**How to Open Serial Monitor:**
1. Connect Arduino to computer via USB
2. Open Arduino IDE
3. Click **Tools → Serial Monitor**
4. Set baud rate to **9600** in bottom-right corner
5. Ensure "Both NL & CR" is selected

### Logged Information

The Arduino continuously outputs:

* **Current ammonia sensor reading** (numeric value)
* **Current operating state** (IDLE, CYCLE_ON, or CYCLE_OFF)
* **Relay status** (MIST ON or MIST OFF)
* **LED status** (Green/Red ON/OFF)
* **System events** (alarm triggered, state transitions, etc.)
* **Timestamps** (for analyzing response timing)

### Example Serial Output

```
═══════════════════════════════════════════════════════════════
  FreshGuard NH₃ Detection System - Serial Monitor Output
═══════════════════════════════════════════════════════════════

[STARTUP] System initializing...
[INIT] Sensor warming up...
[INIT] LCD initialized successfully
[INIT] DFPlayer serial ready
[INIT] GPIO pins configured
[INFO] System ready. Entering IDLE state.

────────────────────────────────────────────────────────────────
IDLE (Normal Monitoring)
────────────────────────────────────────────────────────────────

[IDLE] NH3: 245 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 248 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 252 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 250 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 246 | Relay: OFF | G=ON  R=OFF

────────────────────────────────────────────────────────────────
ALARM DETECTED
────────────────────────────────────────────────────────────────

[ALARM] ⚠️ HIGH AMMONIA DETECTED!
[ALARM] Sensor Reading: 410 (Threshold: 400)
[STATE] Transition: IDLE → CYCLE_ON
[DFPLAYER] Commanding to play voice notification...
[RELAY] ON - Mist maker activated
[LED] Red ON
[LED] Green OFF

────────────────────────────────────────────────────────────────
CYCLE_ON (Misting Active - 20 seconds)
────────────────────────────────────────────────────────────────

[CYCLE_ON] Time: 1s/20s  | NH3: 415 | Relay: ON  | R=ON
[CYCLE_ON] Time: 2s/20s  | NH3: 418 | Relay: ON  | R=ON
[CYCLE_ON] Time: 3s/20s  | NH3: 420 | Relay: ON  | R=ON
[CYCLE_ON] Time: 5s/20s  | NH3: 422 | Relay: ON  | R=ON
[CYCLE_ON] Time: 10s/20s | NH3: 418 | Relay: ON  | R=ON
[CYCLE_ON] Time: 15s/20s | NH3: 414 | Relay: ON  | R=ON
[CYCLE_ON] Time: 19s/20s | NH3: 410 | Relay: ON  | R=ON
[CYCLE_ON] Time: 20s/20s | NH3: 408 | Relay: ON  | R=ON

[STATE] Transition: CYCLE_ON → CYCLE_OFF
[RELAY] OFF - Mist maker stopped
[INFO] Entering 10-second rest period...

────────────────────────────────────────────────────────────────
CYCLE_OFF (Rest Period - 10 seconds)
────────────────────────────────────────────────────────────────

[CYCLE_OFF] Time: 1s/10s  | NH3: 405 | Relay: OFF | R=ON
[CYCLE_OFF] Time: 3s/10s  | NH3: 398 | Relay: OFF | R=ON
[CYCLE_OFF] Time: 5s/10s  | NH3: 395 | Relay: OFF | R=ON
[CYCLE_OFF] Time: 8s/10s  | NH3: 390 | Relay: OFF | R=ON
[CYCLE_OFF] Time: 10s/10s | NH3: 385 | Relay: OFF | R=ON

────────────────────────────────────────────────────────────────
EVALUATION AND DECISION
────────────────────────────────────────────────────────────────

[INFO] 10-second rest completed. Evaluating ammonia level...
[INFO] Current ammonia reading: 385
[INFO] Lower threshold (THRESHOLD_OFF): 350
[DECISION] Ammonia is now BELOW reset threshold (385 < 350)
[STATE] Transition: CYCLE_OFF → IDLE
[RELAY] OFF - Remaining OFF
[LED] Green ON - Restoring normal indicator
[LED] Red OFF - Alarm cleared
[INFO] ✓ Air quality restored to SAFE level
[INFO] Misting cycles used: 1
[INFO] Total operation time: 30 seconds

────────────────────────────────────────────────────────────────
RETURN TO IDLE (Normal Monitoring)
────────────────────────────────────────────────────────────────

[IDLE] NH3: 340 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 342 | Relay: OFF | G=ON  R=OFF
[IDLE] NH3: 338 | Relay: OFF | G=ON  R=OFF

═══════════════════════════════════════════════════════════════
```

### Serial Output Functions

```cpp
void print_system_status() {
  static unsigned long lastPrint = 0;
  unsigned long now = millis();
  
  // Print status every 1 second
  if (now - lastPrint >= 1000) {
    lastPrint = now;
    
    int reading = read_nh3_sensor();
    
    // Format: [STATE] NH3: ### | Relay: ON/OFF | G=ON/OFF R=ON/OFF
    Serial.print("[");
    
    if (currentState == IDLE) {
      Serial.print("IDLE");
    } else if (currentState == CYCLE_ON) {
      Serial.print("CYCLE_ON");
    } else if (currentState == CYCLE_OFF) {
      Serial.print("CYCLE_OFF");
    }
    
    Serial.print("] NH3: ");
    Serial.print(reading);
    Serial.print(" | Relay: ");
    Serial.print(digitalRead(RELAY_PIN) ? "ON" : "OFF");
    Serial.print(" | G=");
    Serial.print(digitalRead(GREEN_LED_PIN) ? "ON" : "OFF");
    Serial.print(" R=");
    Serial.println(digitalRead(RED_LED_PIN) ? "ON" : "OFF");
  }
}

void print_event(const char* eventName, const char* details) {
  Serial.print("[");
  Serial.print(eventName);
  Serial.print("] ");
  Serial.println(details);
}
```

### Using Serial Output for Troubleshooting

**Check 1: Sensor Reading**
- Normal range: 200–400 in clean air
- If stuck at 0: Sensor not connected
- If stuck at 1023: Sensor shorted

**Check 2: State Transitions**
- Should transition at exactly 400 (IDLE → CYCLE_ON)
- Should cycle every 30 seconds (20s ON + 10s OFF)
- Should transition to IDLE when < 350

**Check 3: Relay Activation**
- Should change from OFF to ON immediately when alarm
- Should change from ON to OFF after exactly 20 seconds
- Should remain OFF during 10-second rest

**Check 4: LED Status**
- Green should be ON during IDLE
- Red should be ON during CYCLE_ON and CYCLE_OFF
- Both should never be ON simultaneously

---

## Overall System Function

The **FreshGuard NH₃ Detection and Automatic Fragrance Dispensing System** continuously monitors ammonia concentration in comfort rooms using an MQ-135 gas sensor.

### Normal Conditions (IDLE State)

Under normal conditions:
- System indicates safe air quality through a **green LED** and LCD display showing **"STATUS: SAFE"**
- No power is consumed by the relay or mist maker
- System remains fully responsive and continuously monitors sensor
- Minimal system noise (only microcontroller and sensor active)

### Ammonia Detection (Transition to Alarm)

When ammonia levels exceed the preset threshold (≥ 400):

1. **Immediate Alerts:**
   - Red LED turns ON
   - Green LED turns OFF
   - Voice notification plays from speaker
   - LCD displays alarm message

2. **Automatic Response:**
   - Relay activates
   - Ultrasonic mist maker begins operating
   - Fragrance mist releases into the air

### Active Mitigation (Automatic Cycling)

Fragrance is released in repeating cycles:

* **20-second ON period:** Mist maker continuously releases fragrance
* **10-second OFF period:** Mist maker stops; fragrance settles and disperses
* **Cycle repeats:** If ammonia still elevated, another cycle begins
* **Smart decision:** If ammonia decreased sufficiently, system may wait or return to IDLE

### Recovery (Return to Normal)

Once the ammonia concentration decreases below the reset threshold (< 350):

1. **Automatic Deactivation:**
   - Relay turns OFF
   - Mist maker stops
   - No more fragrance is released

2. **Status Restoration:**
   - Red LED turns OFF
   - Green LED turns ON
   - LCD displays "STATUS: SAFE"

3. **Return to Monitoring:**
   - System returns to IDLE state
   - Continuous air quality monitoring resumes
   - System is ready for next detection event

### Key Operational Features

✅ **Real-time Monitoring** – MQ-135 sensor continuously reads ammonia levels  
✅ **Multi-sensory Alerts** – Visual (LEDs), Audio (voice), and Textual (LCD) feedback  
✅ **Automatic Response** – No manual intervention needed  
✅ **Efficient Cycling** – 20s ON / 10s OFF prevents excessive fragrance use  
✅ **Intelligent Logic** – Hysteresis prevents rapid switching and oscillation  
✅ **User-Friendly** – Clear status indicators and voice guidance  
✅ **Non-Blocking Operation** – Responsive to all inputs and changes  
✅ **Diagnostic Logging** – Serial Monitor shows real-time system status  

### Integrated Components Working Together

By integrating real-time ammonia sensing, visual indicators, audible notifications, automated fragrance dispensing, and intelligent non-blocking control, **FreshGuard provides an efficient, user-friendly, and reliable solution for maintaining a clean, comfortable, and pleasant restroom environment.**

The system operates seamlessly in the background, automatically detecting problems and responding with appropriate mitigation strategies, all without requiring any user action. Whether in busy shopping centers, office buildings, hospitals, or schools, FreshGuard ensures that restroom air quality is continuously monitored and improved as needed.

---

**System Operational Guarantee:**
- ✅ Continuous 24/7 ammonia monitoring
- ✅ Automatic response within 1 second of alarm
- ✅ Clear feedback through multiple communication channels
- ✅ Efficient resource usage and minimal maintenance
- ✅ Reliable operation in diverse environmental conditions
- ✅ Safe for all users and easy to maintain

**Status:** Ready for deployment and testing  
**Last Updated:** 2026-01-15

