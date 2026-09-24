# 500mm Multirotor Platform: Architecture Evolution & Commutation Analysis

**Author:** Aayush Makkar  
**Focus:** Embedded Systems, Control Theory, Hardware-in-the-Loop Diagnostic Analysis  
**Target:** MIT Maker Portfolio / Technical Supplemental Documentation  

---

## 1. System Evolution & Architecture

### Generation 1 (Deprecated)
* **Flight Controller:** APM 2.8 (8-bit AVR architecture)
* **Actuation:** 1400KV BLDC Motors
* **Failure Analysis:** The 8-bit APM architecture lacks the processing bandwidth for modern dynamic PID filtering. Furthermore, 1400KV motors on a 500mm frame swinging 10-inch propellers resulted in severe over-propping, thermal inefficiency, and dynamic instability. The system was deprecated to migrate to a 32-bit architecture.

### Generation 2 (Current Production)
* **Airframe:** 500mm Diagonal Wheelbase (TBS 500 Geometry)
* **Flight Controller (FC):** SpeedyBee F405 V3 (STM32F405 MCU, 168MHz)
* **Actuation:** 4x 1000KV Sensorless Brushless DC (Optimized for 10-inch props)
* **Speed Controllers (ESC):** 4x Legacy 30A Opto/Linear Analog ESCs
* **Power Distribution:** Unregulated 3S/4S LiPo Direct Rail

---

## 2. Generation 2 Integration Anomalies 

During the migration to the STM32 avionics stack, the system exhibited two critical failure modes:
1. **Asymmetric Actuation Failure:** Channels M1 and M2 responded to command inputs, while channels M3 and M4 remained completely unpowered.
2. **Serial Telemetry Desynchronization:** Total loss of receiver frame decoding following configuration flashes, preventing arming authorization.

---

## 3. Root-Cause Engineering Analysis

### A. STM32 Timer Resource Allocation & Protocol Mismatch
Modern flight stacks default to digital protocols (`DSHOT300`/`DSHOT600`). Legacy SimonK analog ESCs rely on analog pulse-width modulation (1000us - 2000us). 
* **The Conflict:** Assigning digital pulse engines across shared STM32F405 timer blocks created register collisions on secondary channels, leaving timer outputs for M3 and M4 in an unasserted state.

### B. Common-Rail BEC Contention & Bus Ground Noise
Each legacy yellow ESC integrates an internal 5V linear regulator. Tying all four 5V leads into the SpeedyBee F405 power plane placed four independent linear regulators in parallel with the flight controller’s dedicated switching regulator, generating ground loop currents and sensor noise.

---

## 4. Methodological Resolution & Bench Diagnostics

### Step 1: Power Bus & Ground Loop Isolation
* Physically desoldered and isolated the `+5V` rail pins on all four ESC servo connectors.
* Retained strictly the **Signal** and **Common Ground** references on pads M1 through M4.

### Step 2: Betaflight Timer & PWM Down-Sampling
Output protocols were forced from DMA-based DShot to legacy analog PWM via CLI to match the 8-bit ESC MCUs:
`set dshot_burst = OFF`
`set motor_pwm_protocol = PWM`
`set motor_pwm_rate = 400`

### Step 3: Hardware UART Restoration
Identified serial receiver input line routing and restored the active UART channel configuration wiped during the firmware flash.

### Step 4: Multi-Channel Endpoint Synchronization
Calibrated high and low throttle command thresholds across all four analog channels simultaneously using a full-scale PWM boundary sweep (2000us down to 1000us).

## Firmware Architecture & Diagnostic Strategy

**Current Configuration:** Betaflight (Target: SPEEDYBEEF405V3)  
**Previous State:** ArduPilot (MAVLink)  

**Decision Rationale (September 2026):**  
The flight controller was initially running ArduPilot. While ArduPilot is the standard for autonomous waypoint navigation, its rigorous pre-arm hardware checks (compass, GPS lock, barometer calibration) introduce unnecessary operational friction for static bench testing. 

To execute the thermal boundary-layer diagnostics (Schlieren optical bench), I require raw, low-level override control of the ESCs. I flashed the board to Betaflight to utilize its direct Motor tab PWM override, allowing me to isolate motor commutation and measure stator heat dissipation without bypassing complex autonomous safety loops.


## Avionics & Firmware Configuration
*   **Flight Controller:** SpeedyBee F405 V3 (STM32F405)
*   **Firmware:** Betaflight 4.4+ (Custom Cloud Build)
*   **Receiver Protocol:** FlySky IBUS (Serial digital protocol bridging FS-i6 hardware)
*   **ESC Protocol:** Legacy PWM
*   **Build Rationale:** Stripped DSHOT and telemetry drivers from the cloud build to optimize STM32 flash memory, ensuring zero-latency compatibility with legacy 30A SimonK analog ESCs used in the static thermal diagnostic rig.
