# Comprehensive Wiring and Assembly Guide
## Ammonia Detection and Mitigation System (ADMS)

---

## TABLE OF CONTENTS
1. [Overview and Safety](#overview-and-safety)
2. [Materials and Components Checklist](#materials-and-components-checklist)
3. [Step-by-Step Assembly Tables](#step-by-step-assembly-tables)
4. [Troubleshooting Guide](#troubleshooting-guide)
5. [Testing and Validation](#testing-and-validation)

---

## OVERVIEW AND SAFETY

### System Description
The Ammonia Detection and Mitigation System (ADMS) is an automated prototype designed to detect harmful ammonia gas concentrations in the air and activate a mitigation response (ultrasonic mist). The system includes:
- Real-time ammonia concentration monitoring via analog sensor
- Visual alerts (LED indicators)
- Audio alerts (voice notification via speaker)
- Automatic mist generation via relay-controlled ultrasonic mist maker
- LCD display for status and readings

### Safety Warnings
- **Do NOT connect the mist maker directly to AC power.** Only use 5V USB power.
- **Do NOT apply voltages above 5V to the Arduino or any sensor.**
- **Always use resistors** to protect the Arduino D11 pin (1 kΩ) and LEDs (220 Ω).
- **Ensure all connections are secure** before powering the system.
- **Never touch the relay contacts** while the system is powered.
- **Allow the MQ-137 sensor to warm up for 2–5 minutes** before taking measurements.

---

## MATERIALS AND COMPONENTS CHECKLIST

### Electronic Components
| Component | Quantity | Notes |
|-----------|----------|-------|
| Arduino Uno R3 | 1 | Microcontroller board |
| Breadboard (830 holes, 2×10 power rails) | 1 | Power distribution only |
| MQ-137 Ammonia Sensor | 1 | Analog air quality sensor |
| 16×2 LCD Display with I²C Module | 1 | Parallel display interface |
| DFPlayer Mini | 1 | Serial MP3 audio playback module |
| Relay Module (5V, Single Channel) | 1 | Automatic switching circuit |
| Green LED | 1 | Status indicator (normal operation) |
| Red LED | 1 | Status indicator (high ammonia) |
| Ultrasonic Mist Maker | 1 | 5V USB-powered misting device |
| Speaker | 1 | 0.5–2W, 8Ω recommended |
| Power Bank (Dual USB Ports, ≥10,000 mAh) | 1 | Battery power supply |

### Wiring and Passive Components
| Item | Quantity | Type/Specification |
|------|----------|-------------------|
| Male-to-Male Jumper Wire | 4 | 22 AWG, 0.5 m length (Red, Black, Red, Black) |
| Female-to-Male Jumper Wire | 20 | 22 AWG, 0.5 m length (Red, Black, Yellow, Blue, Purple, Orange, Green) |
| Speaker Wire | 0.5 m | 16–22 AWG paired cable |
| USB Cable (Type A to Micro-B) | 2 | For Arduino and mist maker |
| Resistor 1 kΩ | 1 | ¼W carbon film (DFPlayer RX protection) |
| Resistor 220 Ω | 2 | ¼W carbon film (LED current limiting) |
| MicroSD Card (≤2 GB) | 1 | For audio files on DFPlayer (optional but recommended) |

### Tools Required
| Tool | Purpose |
|------|---------|
| Multimeter | Verify connections and voltage levels |
| Flashlight or Headlamp | Inspect connections on breadboard |
| Small Screwdriver | Adjust relay and sensor potentiometers (if needed) |
| Soldering Iron (optional) | Secure critical connections |
| Helping Hands / Alligator Clips | Hold wires during assembly |

---

## STEP-BY-STEP ASSEMBLY TABLES

### Table 1: Preparing the Workspace and Main Board

| Step | What to Do | Materials Used | Expected Result |
|------|-----------|-----------------|-----------------|
| 1.1 | Place the Arduino Uno R3 on the working table | Arduino Uno R3 | The Arduino is ready for wiring |
| 1.2 | Place the breadboard to the right of the Arduino | Breadboard | The breadboard is in easy reach |
| 1.3 | Arrange all components in groups by type | All components | Easy identification and access to each part |
| 1.4 | Verify all components match the checklist | Checklist | All items are present before assembly begins |
| 1.5 | Clear a space for tool placement | Multimeter, screwdriver | Tools are within reach during assembly |

---

### Table 2: Setting Up the Arduino Power Supply

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 2.1 | Locate the 5V pin on the Arduino Uno R3 | None | Arduino board | Pin is identified |
| 2.2 | Locate the GND pin on the Arduino Uno R3 | None | Arduino board | Pin is identified |
| 2.3 | Connect Arduino 5V to breadboard + rail | Male-to-Male Jumper Wire (Red) | Arduino 5V → Breadboard + Rail | The breadboard receives +5V power supply |
| 2.4 | Connect Arduino GND to breadboard − rail | Male-to-Male Jumper Wire (Black) | Arduino GND → Breadboard − Rail | The breadboard has a common ground (GND) |
| 2.5 | Verify both rails are firmly seated | None | Visual inspection | No loose wires or gaps visible |

---

### Table 3: Installing the MQ-137 Ammonia Sensor

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 3.1 | Mount the MQ-137 sensor on the breadboard or holder | None | MQ-137 sensor positioned | Sensor is stable and air can flow freely around it |
| 3.2 | Connect VCC (red module pin) to breadboard + rail | Female-to-Male Jumper Wire (Red) | + Rail → MQ-137 VCC | The sensor receives +5V power |
| 3.3 | Connect GND (black module pin) to breadboard − rail | Female-to-Male Jumper Wire (Black) | − Rail → MQ-137 GND | The sensor has a complete ground connection |
| 3.4 | Connect AO (yellow module pin) to Arduino A0 | Female-to-Male Jumper Wire (Yellow) | MQ-137 AO → Arduino A0 | Analog ammonia readings are transmitted to the Arduino |
| 3.5 | Leave DO (digital output) disconnected | None | DO pin left unconnected | Digital output is not used; analog readings provide better sensitivity |
| 3.6 | Allow sensor to warm up (if powered on) | None | Leave powered for 2–5 minutes | Sensor output becomes stable and accurate |

---

### Table 4: Installing the 16×2 LCD Display with I²C Module

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 4.1 | Mount the LCD module with I²C adapter on the breadboard or clamping bracket | None | LCD positioned facing forward | LCD is secure and easy to read |
| 4.2 | Connect VCC (red I²C pin) to breadboard + rail | Female-to-Male Jumper Wire (Red) | + Rail → LCD VCC | The LCD receives power (+5V) |
| 4.3 | Connect GND (black I²C pin) to breadboard − rail | Female-to-Male Jumper Wire (Black) | − Rail → LCD GND | The LCD has a complete ground connection |
| 4.4 | Connect SDA (data line) to Arduino A4 | Female-to-Male Jumper Wire (Blue) | LCD SDA → Arduino A4 | I²C data communication is established |
| 4.5 | Connect SCL (clock line) to Arduino A5 | Female-to-Male Jumper Wire (Blue) | LCD SCL → Arduino A5 | I²C clock communication is established |
| 4.6 | Verify LCD displays "ADMS Ready" or default text | None | Power on Arduino briefly | LCD is functioning and connected correctly |

---

### Table 5: Installing the DFPlayer Mini Audio Module

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 5.1 | Mount the DFPlayer Mini on the breadboard or PCB board | None | DFPlayer positioned safely | Module is secure and microSD slot is accessible |
| 5.2 | Connect VCC (red pin) to breadboard + rail | Female-to-Male Jumper Wire (Red) | + Rail → DFPlayer VCC | The DFPlayer receives +5V power |
| 5.3 | Connect GND (black pin) to breadboard − rail | Female-to-Male Jumper Wire (Black) | − Rail → DFPlayer GND | The DFPlayer has a complete ground connection |
| 5.4 | Connect TX (transmit) to Arduino D10 | Female-to-Male Jumper Wire (Purple) | DFPlayer TX → Arduino D10 | Serial data is transmitted from DFPlayer to Arduino |
| 5.5 | Insert 1 kΩ resistor into breadboard between Arduino D11 and DFPlayer RX | Female-to-Male Jumper Wire (Purple) + 1 kΩ Resistor | Arduino D11 → 1 kΩ Resistor → DFPlayer RX | RX input is protected from overvoltage; serial commands are received |
| 5.6 | Connect speaker red wire to DFPlayer SPK1 | Speaker Wire (Red) | Speaker (+) → DFPlayer SPK1 | Audio signal path is complete |
| 5.7 | Connect speaker black wire to DFPlayer SPK2 | Speaker Wire (Black) | Speaker (−) → DFPlayer SPK2 | Audio return path is complete |
| 5.8 | Verify speaker connection is secure | None | Physical inspection | No loose speaker connections |
| 5.9 | (Optional) Insert microSD card with audio file into DFPlayer slot | microSD Card | Insert into DFPlayer slot | Audio file is available for playback |

---

### Table 6: Installing the Relay Module

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 6.1 | Mount the relay module near the mist maker wiring area | None | Relay module positioned | Module is secure and accessible |
| 6.2 | Connect VCC (red pin) to breadboard + rail | Female-to-Male Jumper Wire (Red) | + Rail → Relay VCC | The relay receives +5V power |
| 6.3 | Connect GND (black pin) to breadboard − rail | Female-to-Male Jumper Wire (Black) | − Rail → Relay GND | The relay has a complete ground connection |
| 6.4 | Connect IN (input control signal) to Arduino D7 | Female-to-Male Jumper Wire (Green) | Relay IN → Arduino D7 | Arduino can control relay activation via D7 |
| 6.5 | Leave NC (normally closed) terminal unconnected | None | NC pin left unconnected | NC terminal is not used; mist maker remains OFF until ammonia detected |
| 6.6 | Verify relay module status LED (if present) | None | Visual inspection | LED behavior matches relay state (ON/OFF) |

---

### Table 7: Installing the Green LED (Normal Operation Indicator)

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 7.1 | Mount the green LED on the front panel of the prototype | None | LED is positioned and secured | LED is visible from the front of the device |
| 7.2 | Connect Arduino D5 to a 220 Ω current-limiting resistor | Female-to-Male Jumper Wire (Orange) + 220 Ω Resistor | Arduino D5 → 220 Ω Resistor | Resistor protects the Arduino and LED |
| 7.3 | Connect the 220 Ω resistor to the long leg (anode, +) of the green LED | Female-to-Male Jumper Wire (Orange) | 220 Ω Resistor → Green LED Anode (+) | Current flows to the LED through the resistor |
| 7.4 | Connect the short leg (cathode, −) of the green LED to breadboard − rail | Female-to-Male Jumper Wire (Black) | Green LED Cathode (−) → Breadboard − Rail | The LED circuit is complete; ground returns to breadboard |
| 7.5 | Test LED brightness (should be dim to moderate) | None | Apply 5V and observe | LED lights up; brightness is safe for extended operation |

---

### Table 8: Installing the Red LED (High Ammonia Alert Indicator)

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 8.1 | Mount the red LED on the front panel beside the green LED | None | LED is positioned and secured | LED is visible from the front of the device |
| 8.2 | Connect Arduino D6 to a 220 Ω current-limiting resistor | Female-to-Male Jumper Wire (Orange) + 220 Ω Resistor | Arduino D6 → 220 Ω Resistor | Resistor protects the Arduino and LED |
| 8.3 | Connect the 220 Ω resistor to the long leg (anode, +) of the red LED | Female-to-Male Jumper Wire (Orange) | 220 Ω Resistor → Red LED Anode (+) | Current flows to the LED through the resistor |
| 8.4 | Connect the short leg (cathode, −) of the red LED to breadboard − rail | Female-to-Male Jumper Wire (Black) | Red LED Cathode (−) → Breadboard − Rail | The LED circuit is complete; ground returns to breadboard |
| 8.5 | Test LED brightness (should be dim to moderate) | None | Apply 5V and observe | LED lights up; brightness indicates alarm state |

---

### Table 9: Installing the Ultrasonic Mist Maker and Power Relay Circuit

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 9.1 | Mount the ultrasonic mist maker in a container with water | None | Mist maker positioned in water | Device is ready for operation; water fills the chamber |
| 9.2 | Connect the mist maker to USB Port 2 of the power bank | USB Cable | Power Bank USB Port 2 → Mist Maker USB Port | Power is supplied via USB cable (do NOT use AC adapters) |
| 9.3 | Extract the red (+5V) wire from the USB cable (if using a splitter) | Speaker Wire or USB Cable (Red) | USB +5V → Relay COM | Power line is ready for relay control |
| 9.4 | Connect the red (+5V) wire to the relay COM (common) terminal | Speaker Wire or USB Cable (Red) | USB +5V → Relay COM | COM terminal receives +5V when relay is powered |
| 9.5 | Connect the relay NO (normally open) terminal to the mist maker positive wire | Speaker Wire (Red) or USB Cable (Red) | Relay NO → Mist Maker Positive (+) | Power is delivered to mist maker only when relay activates |
| 9.6 | Extract the black (GND) wire from the USB cable | Speaker Wire or USB Cable (Black) | USB GND → Mist Maker Ground | Ground line is ready for mist maker return |
| 9.7 | Connect the black (GND) wire directly to the mist maker negative wire | Speaker Wire or USB Cable (Black) | USB GND → Mist Maker Negative (−) | Ground circuit is complete; mist maker can return current |
| 9.8 | Leave the relay NC (normally closed) terminal unconnected | None | NC pin left unconnected | NC terminal is not used in this configuration |
| 9.9 | Verify all power connections are secure and insulated | None | Visual and physical inspection | No bare wires are exposed; no short circuits detected |

---

### Table 10: Connecting the Power Bank and Verifying Power Distribution

| Step | What to Do | Jumper Wire Used | Physical Connection | Expected Result |
|------|-----------|-----------------|---------------------|-----------------|
| 10.1 | Verify the power bank has at least two USB ports with independent outputs | None | Power bank inspection | Both ports can deliver 5V simultaneously |
| 10.2 | Connect the Arduino to USB Port 1 of the power bank using a USB cable | USB Cable (Type A to Micro-B) | Power Bank USB Port 1 → Arduino USB Port | Arduino receives +5V power; status LED lights up |
| 10.3 | Connect the mist maker to USB Port 2 of the power bank using the primary USB cable | USB Cable (Type A to Micro-B) | Power Bank USB Port 2 → Mist Maker USB Port | Mist maker receives +5V power |
| 10.4 | Verify that both USB ports share a common internal battery | None | Power bank specifications | Both ports deliver power from the same battery source |
| 10.5 | Confirm that both USB ports share a common ground (GND) internally | None | Power bank specifications (or multimeter check) | No additional external ground wire is required between ports |
| 10.6 | Power on the power bank and verify the Arduino boots | None | Observe Arduino status LED | Arduino boots successfully; USB communication is ready |
| 10.7 | Verify that the MQ-137 sensor is powered | None | Observe sensor warm-up behavior or multimeter | Sensor voltage is +5V; ready for warm-up period |

---

### Table 11: Final Connection Verification and Safety Checks

| Step | What to Do | Tool Used | Expected Result |
|------|-----------|-----------|-----------------|
| 11.1 | Inspect all red wires to confirm they connect to +5V or + Rail | Multimeter + Visual Inspection | All red wires are at +5V potential |
| 11.2 | Inspect all black wires to confirm they connect to GND or − Rail | Multimeter + Visual Inspection | All black wires are at 0V potential (ground) |
| 11.3 | Verify that the 1 kΩ resistor is connected between Arduino D11 and DFPlayer RX | Visual Inspection | Resistor is visible and in correct position |
| 11.4 | Verify that both 220 Ω resistors are connected in series with the LED anodes | Visual Inspection | Both resistors are in correct positions; LEDs are functional |
| 11.5 | Confirm that Arduino A0, A4, A5 have correct signal wires | Multimeter (continuity check) | Continuity is verified from Arduino pins to modules |
| 11.6 | Confirm that Arduino D5, D6, D7, D10, D11 have correct signal wires | Multimeter (continuity check) | Continuity is verified from Arduino pins to modules |
| 11.7 | Ensure no loose wires are present and no exposed conductive parts touch | Visual and Physical Inspection | All wires are secure; no short circuits possible |
| 11.8 | Check that the speaker wires are correctly connected to DFPlayer SPK1 and SPK2 | Visual and Physical Inspection | Speaker wires are secure and in correct polarity |
| 11.9 | Verify that the mist maker USB cable is firmly connected to the power bank | Physical Inspection | USB cable is fully inserted and seated |
| 11.10 | Check that all breadboard rail connections are tight and not bent | Visual Inspection | All wire ends are fully inserted into breadboard | 

---

### Table 12: System Testing and Initialization

| Step | What to Do | Expected Behavior | Pass / Fail |
|------|-----------|-------------------|-----------|
| 12.1 | Turn on the power bank and allow the system to boot (wait 3–5 seconds) | Arduino boots; status light appears on LCD; both LEDs remain off | ✓ / ✗ |
| 12.2 | Observe the MQ-137 sensor warm-up phase (2–5 minutes) | Sensor output stabilizes; no LED activity yet; LCD displays "Warming up..." or similar | ✓ / ✗ |
| 12.3 | Bring an ammonia source (e.g., household ammonia or ammonia pad) near the sensor | Red LED turns ON; green LED turns OFF; LCD displays "HIGH AMMONIA" | ✓ / ✗ |
| 12.4 | Listen for the speaker to activate and play the recorded warning message | Speaker emits a clear audio alert message (e.g., "High ammonia detected") | ✓ / ✗ |
| 12.5 | Observe the relay module control light and listen for relay activation | Relay clicks audibly; relay control LED lights up | ✓ / ✗ |
| 12.6 | Verify that the mist maker activates and produces mist | Mist maker vibrates and produces visible mist in the air or water | ✓ / ✗ |
| 12.7 | Remove the ammonia source and allow the sensor to return to normal levels | Red LED turns OFF; green LED turns ON; LCD displays "Normal" or similar; mist maker stops | ✓ / ✗ |
| 12.8 | Repeat the ammonia detection cycle 3–5 times to verify consistency | System responds consistently to ammonia detection and removal | ✓ / ✗ |
| 12.9 | Check LCD display for clarity and readability from at least 1 meter away | Text is clear and easy to read; contrast is appropriate | ✓ / ✗ |
| 12.10 | Measure ammonia concentration readings and compare against known standards | Readings are within expected range; sensor sensitivity is appropriate | ✓ / ✗ |

---

## TROUBLESHOOTING GUIDE

### Problem: Arduino Does Not Boot
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| No status LED on Arduino | Power not connected | Verify USB cable is plugged into power bank and Arduino |
| Power bank appears dead | Power bank battery depleted | Charge power bank for at least 4 hours using a proper USB charger |
| Loose USB connection | Check both ends of USB cable; reseat firmly |

### Problem: MQ-137 Sensor Not Responding
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Arduino A0 reads 0V or 1023 constantly | Sensor not powered or A0 not connected | Verify VCC and GND connections; check A0 wire continuity |
| Sensor not detecting ammonia | Sensor in warm-up phase | Wait 2–5 minutes after power-on before testing |
| Sensor threshold too high | Adjust sensor sensitivity via onboard potentiometer (clockwise = more sensitive) |

### Problem: LCD Display Does Not Show Text
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| LCD remains dark | I²C communication failed | Check SDA (A4) and SCL (A5) wire continuity using multimeter |
| Power is not reaching LCD | Verify VCC and GND connections to LCD; use multimeter to confirm +5V |
| I²C address mismatch | Scan I²C bus address using Arduino IDE; update code if address differs |

### Problem: Speaker Does Not Play Audio
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| DFPlayer Mini not powered | Check VCC and GND connections to DFPlayer | Verify connections using multimeter |
| No microSD card in slot | Insert formatted microSD card with audio file (MP3 format) | Ensure file is named correctly and placed in root directory |
| Serial communication error | Check D10 (TX) and D11 (RX) connections | Verify 1 kΩ resistor is in series with D11 |
| Speaker is defective | Test speaker with known working audio source | Replace speaker if no sound output detected |

### Problem: Relay Does Not Activate
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Relay does not click when ammonia detected | Arduino D7 not sending signal | Verify D7 wire connection using multimeter (should read ~5V when active) |
| Relay powered but no switch action | Relay IN pin disconnected or loose | Check connection between Arduino D7 and relay IN pin |
| Mist maker does not activate even when relay clicks | COM or NO terminal not connected | Verify relay wiring to power and mist maker |

### Problem: LEDs Do Not Light Up
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| LED remains dark | Wrong pin connected | Verify D5 (green) and D6 (red) connections |
| 220 Ω resistor missing or damaged | Check that resistor is connected between Arduino pin and LED anode | Replace resistor if damaged |
| LED polarity reversed | Long leg (anode, +) must connect to resistor, short leg (cathode, −) to ground | Correct polarity and verify brightness |

### Problem: Mist Maker Does Not Produce Mist
| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Mist maker powered but no mist | Water chamber is empty | Add distilled water to the mist maker chamber |
| Ultrasonic element blocked or damaged | Clean the ultrasonic element; replace if still no mist | Use distilled water to avoid mineral buildup |
| Relay does not pass power to mist maker | Check relay COM and NO terminals for continuity | Verify wiring between relay and mist maker positive wire |

---

## TESTING AND VALIDATION

### Test 1: Power Supply Verification
**Objective:** Confirm that all modules receive correct voltage levels.

| Module | Expected Voltage | Measurement Method | Pass Criteria |
|--------|-----------------|-------------------|---------------|
| Arduino 5V Rail | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |
| Breadboard + Rail | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |
| MQ-137 VCC | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |
| DFPlayer VCC | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |
| Relay VCC | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |
| LCD VCC | +5.0V ± 0.2V | Multimeter DC volts | ✓ 4.8V–5.2V |

### Test 2: Signal Line Integrity
**Objective:** Verify that signal lines are properly connected and not shorted.

| Signal Line | Expected State (No Load) | Measurement Method | Pass Criteria |
|-------------|-------------------------|-------------------|---------------|
| Arduino A0 (Sensor) | Floating 0V–5V | Multimeter DC volts | ✓ Changes with ammonia |
| Arduino A4 (LCD SDA) | Floating 3.3V–5V | Multimeter DC volts | ✓ Toggles during I²C activity |
| Arduino A5 (LCD SCL) | Floating 3.3V–5V | Multimeter DC volts | ✓ Toggles during I²C activity |
| Arduino D5 (Green LED) | ~5V (PWM) | Multimeter DC volts | ✓ LED brightness controlled |
| Arduino D6 (Red LED) | ~5V (PWM) | Multimeter DC volts | ✓ LED brightness controlled |
| Arduino D7 (Relay) | 0V (inactive) or +5V (active) | Multimeter DC volts | ✓ Toggles with relay state |
| Arduino D10 (DFPlayer TX) | Toggling 0V–5V | Multimeter DC volts | ✓ Serial data observed |
| Arduino D11 (DFPlayer RX) | ~3.3V–5V with 1 kΩ resistor | Multimeter DC volts | ✓ Protected from overvoltage |

### Test 3: Sensor Accuracy and Response
**Objective:** Validate that the MQ-137 sensor responds correctly to ammonia.

| Test Condition | Expected Sensor Output | Pass Criteria |
|----------------|----------------------|---------------|
| Clean air (background) | ADC reading 200–400 | ✓ Stable baseline |
| Mild ammonia (low concentration) | ADC reading 400–600 | ✓ Gradual increase detected |
| High ammonia (strong source) | ADC reading 600+ | ✓ Red LED activates; relay triggers |
| Ammonia removed | ADC reading returns to baseline | ✓ Red LED deactivates; system returns to normal |

### Test 4: Output Device Verification
**Objective:** Confirm that all output devices (LEDs, speaker, relay, mist maker) function correctly.

| Device | Test Method | Expected Result | Pass |
|--------|------------|-----------------|------|
| Green LED | Apply +5V to D5 | LED lights up (dim, safe brightness) | ✓ / ✗ |
| Red LED | Apply +5V to D6 | LED lights up (dim, safe brightness) | ✓ / ✗ |
| Speaker | Send audio command to DFPlayer | Speaker plays recorded message clearly | ✓ / ✗ |
| Relay | Set D7 HIGH | Relay clicks audibly; indicator light activates | ✓ / ✗ |
| Mist Maker | Power relay NO with +5V | Ultrasonic element vibrates; mist produced | ✓ / ✗ |

### Test 5: Full System Integration Test
**Objective:** Verify complete system operation under realistic conditions.

| Test Step | Action | Expected Outcome | Pass |
|-----------|--------|-----------------|------|
| 1 | Power on system; allow 3–5 second boot | Arduino boots; LCD displays initialization message | ✓ / ✗ |
| 2 | Wait 2–5 minutes for sensor warm-up | Green LED steady; LCD displays "System Ready" | ✓ / ✗ |
| 3 | Introduce ammonia source near sensor | Red LED activates; green LED deactivates | ✓ / ✗ |
| 4 | Relay activates within 1–2 seconds | Relay clicks; control light on | ✓ / ✗ |
| 5 | Mist maker starts producing mist | Visible mist spray; humidification begins | ✓ / ✗ |
| 6 | Speaker plays recorded alert message | Audio alert heard clearly | ✓ / ✗ |
| 7 | LCD displays high ammonia warning | "HIGH AMMONIA DETECTED" or similar message shown | ✓ / ✗ |
| 8 | Remove ammonia source | System gradually returns to normal state | ✓ / ✗ |
| 9 | Red LED deactivates; green LED reactivates | Normal operation resumed | ✓ / ✗ |
| 10 | Mist maker stops; LCD returns to "Ready" | System idle until next detection | ✓ / ✗ |

### Test 6: Reliability and Endurance Test
**Objective:** Verify system stability over extended operation.

| Parameter | Duration | Acceptance Criteria |
|-----------|----------|-------------------|
| Continuous operation | 1–2 hours | No false alarms; no component failures; all readings stable |
| Repeated ammonia detection cycles | 10+ cycles | System responds consistently; no degradation observed |
| Thermal stability | 30 minutes at full load | No component overheating; case remains cool to touch |
| Power stability | Throughout test | Voltage remains within ±5% of nominal 5V |

---

## FINAL ASSEMBLY CHECKLIST

- [ ] All components sourced and verified
- [ ] Workspace prepared and organized
- [ ] Arduino connected to breadboard power rails
- [ ] MQ-137 sensor installed and powered
- [ ] LCD module installed and I²C configured
- [ ] DFPlayer Mini installed with speaker connected
- [ ] Relay module installed and controlled via D7
- [ ] Green LED installed on D5 with 220 Ω resistor
- [ ] Red LED installed on D6 with 220 Ω resistor
- [ ] Mist maker connected via relay to power bank Port 2
- [ ] Power bank connected: Arduino to Port 1, Mist Maker to Port 2
- [ ] All connections verified with multimeter
- [ ] No loose wires or exposed conductive parts
- [ ] All resistors in correct positions (1 kΩ for DFPlayer, 220 Ω for LEDs)
- [ ] System powers on successfully
- [ ] MQ-137 sensor warms up and stabilizes
- [ ] All LEDs respond to test signals
- [ ] Speaker plays audio correctly
- [ ] Relay activates when ammonia detected
- [ ] Mist maker produces mist when relay activates
- [ ] LCD displays information correctly
- [ ] Full system test completed successfully

---

## REFERENCE DIAGRAMS AND PINOUTS

### Arduino Uno R3 Pinout Summary
```
Digital Pins: D0–D13 (D0, D1 used by serial communication)
Analog Pins: A0–A5 (all 5V analog inputs)
Power Pins: 5V, GND, Vin, 3.3V
```

### Module Pin Assignments
| Module | Arduino Pin | Function |
|--------|------------|----------|
| MQ-137 AO | A0 | Analog ammonia reading |
| LCD SDA | A4 | I²C data line |
| LCD SCL | A5 | I²C clock line |
| Green LED | D5 | PWM output (normal status) |
| Red LED | D6 | PWM output (alert status) |
| Relay IN | D7 | Digital output (relay control) |
| DFPlayer TX | D10 | Serial RX (software serial) |
| DFPlayer RX | D11 | Serial TX (software serial) with 1 kΩ resistor |

---

## REVISION HISTORY

| Date | Version | Changes |
|------|---------|---------|
| 2026-01-15 | 1.0 | Initial comprehensive assembly guide |
| — | — | — |

---

**Document Status:** Ready for inclusion in research paper and prototype manual.  
**Last Updated:** 2026-01-15  
**Author:** ADMS Project Team
