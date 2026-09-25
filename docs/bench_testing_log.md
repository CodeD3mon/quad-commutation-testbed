# Generation 2: Bench Testing & Calibration Log

## Overview
This log tracks chronological bench testing sessions, multi-channel ESC calibration results, motor response synchronization, and operational diagnostic logs for the 500mm multirotor testbed.

---

## Log Entries

### Session 2026-09-25: Board Reflash, ESC Throttle Endpoint Calibration & Serial RX Config

**Objectives:**
- Reflash Betaflight target to `DAKEFPVF405`.
- Measure uncalibrated motor startup thresholds and eliminate ~140µs actuation offset.
- Perform high/low throttle endpoint calibration across legacy 30A analog ESCs.
- Configure UART Serial Receiver (`serialrx_provider = IBUS`) for FlySky FS-iA6B / FS-i6 integration.

**Quantitative Data & Observations:**
1. **Uncalibrated Throttle Start Thresholds:**
   - Motor 1: **1189 µs**
   - Motor 2: **1189 µs**
   - Motor 3: **1048 µs**
   - Motor 4: **1049 µs**
   *(Observation: Channels M1/M2 exhibited a ~140µs delayed response compared to M3/M4 prior to endpoint sweep).*

2. **Multi-Channel ESC Calibration Procedure:**
   - Conducted full-scale PWM boundary sweep (2000µs high endpoint down to 1000µs low endpoint) via Betaflight Motor override under 3S LiPo power.
   - Synchronized high/low throttle threshold registers across all 4 legacy analog ESC microcontrollers.

3. **Serial Receiver (UART / i-BUS) Configuration:**
   - Configured active UART port to Serial RX in Betaflight Ports tab.
   - Set Receiver Mode to `Serial-based receiver` and Receiver Provider to `IBUS` to decode frame data from FlySky FS-iA6B receiver connected to FS-i6 transmitter.

**Verification & Final Results:**
- **Calibrated Motor Idle Start Range:** All 4 motors now initialize and idle synchronously within **1047 µs – 1049 µs** (maximum gap of **~2 µs**).
- **Status:** **PASS** – 4-channel commutation, PWM endpoint alignment, and i-BUS RC telemetry validated for Schlieren optical bench testing.

---

### Session 2026-09-25: Radio Link Verification & Receiver Signal Decoding

**Objectives:**
- Connect 3S LiPo battery power and verify active serial communication between FlySky FS-iA6B receiver and DakeFPV F405 flight controller.
- Test stick/switch channel decoding in Betaflight Configurator Receiver tab.

**Observations & Testing Steps:**
1. **Power Sequence:** Connected 3S LiPo main supply. Receiver initialized and established RF link with FlySky FS-i6 transmitter.
2. **Channel Mapping & Responsiveness:** Verified Roll, Pitch, Throttle, Yaw, and Auxiliary channel inputs in Betaflight Configurator.
3. **Signal Quality:** All control stick movements and auxiliary channel toggles are smoothly and accurately decoded via i-BUS protocol with zero frame drops observed in the software GUI.

**Verification & Results:**
- **Outcome:** Radio control link fully functional and verified in software.
- **Status:** **PASS** – Manual flight/bench override link operational.

---

### Session 2026-09-25: Flight Modes & AUX Switch Allocation

**Objectives:**
- Configure arming safety authorization and stabilization modes in Betaflight Modes tab.
- Map auxiliary physical switches on FlySky FS-i6 transmitter to target PWM signal windows.

**Channel Mapping & Threshold Configuration:**
1. **ARM Mode (Arming Authorization):**
   - **Auxiliary Channel:** `AUX 1` (Mapped to 3-position toggle switch, Position 3 / HIGH).
   - **Signal Range:** `1800 µs – 2100 µs`.
   - **Function:** Enforces explicit physical switch toggle to authorize motor arming and bench commutation overrides.

2. **ANGLE Mode (Self-Leveling Stabilization):**
   - **Auxiliary Channel:** `AUX 2` (Mapped to VRB rotary knob, turned extreme clockwise).
   - **Signal Range:** `1800 µs – 2100 µs`.
   - **Function:** Engages accelerometer-based horizon self-leveling during static/bench testing.

**Verification & Results:**
- **Outcome:** Modes tab indicates active engagement highlighting when AUX 1 switch (Pos 3) and AUX 2 VRB knob (Extreme CW) enter the 1800–2100µs boundary.
- **Status:** **PASS** – Arming safety sequence and ANGLE stabilization modes verified.

---

### Session 2026-09-25: Accelerometer Calibration & Level Attitude Zeroing

**Objectives:**
- Calibrate the onboard IMU accelerometer sensor on the DakeFPV F405 flight controller to establish accurate level reference vectors.

**Procedure & Observations:**
1. **Physical Surface Alignment:** Placed the 500mm airframe on a flat, level bench surface.
2. **Calibration Execution:** Triggered Accelerometer Calibration (`Calibrate Accelerometer`) in the Betaflight Configurator Setup tab.
3. **Offset Zeroing:** Onboard IMU accelerometer bias and gravity vector reference offsets were recalculated and stored to EEPROM/flash memory.

**Verification & Results:**
- **Attitude Alignment:** 3D model orientation in Betaflight Setup tab reflects level resting attitude with zero static roll and pitch drift (`Roll: 0.0°`, `Pitch: 0.0°`).
- **Status:** **PASS** – Accelerometer reference validated for ANGLE auto-level mode operation.

---

### Session 2026-09-25: Motor Rotation Vector Validation & Mechanical Friction Audit

**Objectives:**
- Validate rotational direction vectors across all 4 BLDC motors (M1–M4) to match target quadcopter motor layout geometry.
- Audit physical motor mounting, shaft rotation smoothness, and mechanical tolerances.

**Observations & Testing Steps:**
1. **Actuation Direction Sweep:** Triggered individual motor test overrides in Betaflight Motor tab.
   - All 4 motors rotate cleanly in their expected directional vectors (CW / CCW matching quad X layout).
2. **Video & Empirical Media Verification:**
   - [Bench Test Walkaround Video (MP4)](../assets/media/bench_test_walkaround.mp4) serves as the primary visual and empirical reference for correct motor rotation direction and ARM mode testbed configuration.
3. **Mechanical Friction Inspection (Motor 4):**
   - **Observation:** Motor 4 exhibits minor mechanical drag/stiffness during manual rotation compared to M1–M3.
   - **Suspected Causes:** Overtightened motor mount screws contacting internal stator assembly/windings or minor bearing/stator friction.
   - **Action Item:** Marked for mechanical inspection (adjusting screw torque or replacing stator/motor if thermal buildup is observed during high-current diagnostic runs). Non-blocking for current static bench testing.

**Verification & Results:**
- **Commutation Direction:** Correct rotational vectors verified across all 4 channels (referenced in walkaround video).
- **Status:** **PASS (Conditioned)** – Rotational directions validated; Motor 4 mechanical friction flagged for deferred maintenance.


---

### Session 2026-09-25: PID Filter Tuning & Frame Vibration Attenuation

**Objectives:**
- Tune digital lowpass filter cutoff frequencies to mitigate physical 500mm frame mechanical resonance and prevent ESC thermal overload.

**Filter Adjustments & Parameters:**
1. **Gyro Filter Multiplier:** `0.8` (`set gyro_filter_multiplier = 80`).
   - *Rationale:* Lowers primary lowpass filter cutoff frequencies to attenuate low-frequency structural vibrations typical of 500mm wheelbase frames with 10-inch propellers.
2. **D-Term Filter Multiplier:** `0.8` (`set dterm_filter_multiplier = 80`).
   - *Rationale:* Increases D-term noise filtering depth to prevent high-frequency noise amplification from passing into the legacy 30A analog ESCs, minimizing stator heating during dynamic overrides.

**Verification & Results:**
- **Configuration Save:** Filter multipliers applied and saved to flight controller storage.
- **Status:** **PASS** – Base filter envelope configured for static thermal diagnostic testing.

---

### Session 2026-09-25: PID Controller Multiplier Tuning (Large Propeller & Legacy ESC Optimization)

**Objectives:**
- Re-balance PID gains to stabilize large 10-inch propellers on the 500mm frame while protecting legacy 30A SimonK analog ESCs from high-frequency thermal stress.

**PID Gain Multiplier Adjustments & Rationales:**
1. **Stick Response / Feedforward Gains (FF):** `1.00` → `0.00` (`set feedforward_multiplier = 0`)
   - *Engineering Rationale:* Completely disables Feedforward prediction. Prevents violent FC twitching during static bench testing and raw override diagnostics.
2. **Damping / Derivative Gains (D):** `1.00` → `0.50` (`set d_gain_multiplier = 50`)
   - *Engineering Rationale:* Halves derivative term to prevent high-frequency control loop oscillations from overheating legacy SimonK ESCs when dampening low-frequency 500mm frame vibrations.
3. **Tracking / Proportional Gains (P):** `1.00` → `0.50` (`set p_gain_multiplier = 50`)
   - *Engineering Rationale:* Slashes P-gain to prevent heavy 10-inch propellers from overshooting and overcompensating during attitude corrections. *(Note: This slider adjustment also drops raw I-gain multiplier to 0.50).*
4. **Drift - Wobble / Integral Gains (I):** `1.00` → `2.00` (`set i_gain_multiplier = 200`)
   - *Engineering Rationale:* Mathematically compensates for the Tracking slider drop (`0.50 P/I Multiplier × 2.00 I-Gain Multiplier = 1.00 Effective I Baseline`), restoring exact baseline Integral stabilization to prevent attitude drift.
5. **Master Multiplier:** Retained strictly at `1.00` (`set master_multiplier = 100`).

**Verification & Results:**
- **CLI Configuration Export:** Verified parameters saved in configuration file.
- **Status:** **PASS** – Custom PID gain structure optimized for 500mm airframe inertia and analog ESC thermal limits.

---

### Session 2026-09-25: Tethered Testbed Rig Preparation (15 kg Dumbbell Ballast)

**Objectives:**
- Prepare a safe, anchored static test environment to perform live-power thrust sweeps and thermal boundary layer diagnostics without un-tethered flight risk.

**Safety Rig & Ballast Configuration:**
1. **Mechanical Anchor:** Airframe central load point anchored via heavy-duty tether to a **15 kg dumbbell** mass ballast.
2. **Safety Protocols:**
   - Props-off preliminary spin verification complete.
   - Remote kill switch mapped (`AUX 1`, Pos 3 disarm threshold).
   - Schlieren optical bench alignment and thermal monitoring ready.

**Asset Storage Structure:**
- Rig setup photos, 360° airframe views, wiring schematics, and video logs mapped to repository directory [`assets/`](file:///d:/MIT/Projects/Drone/quad-commutation-testbed/assets/).

**Status:** **READY FOR TETHERED THRUST & COMMUTATION TEST RUNS**.

---

### Session 2026-09-25: Acoustic Anomaly & Electromagnetic Resistance Audit

**Objectives:**
- Isolate ARM mode idle acoustic creaking across all 4 motors and analyze Motor 4 power-dependent resistance.

**Empirical Observations & Diagnostic Findings:**
1. **Quad-Motor Acoustic Creaking (All 4 Motors):**
   - *Finding:* Low-frequency acoustic creaking noise is emitted simultaneously by **all 4 BLDC motors** upon arming at idle throttle.
   - *Technical Cause:* Low-frequency 400Hz PWM carrier pulse switching and active ESC motor holding current propagating acoustically through motor stators and airframe arms.
2. **Motor 4 Power-Dependent Electromagnetic Resistance:**
   - *Unpowered State:* Motor 4 rotates 100% freely and smoothly with zero mechanical resistance (conclusively ruling out mechanical screw binding or bad bearings).
   - *Powered State:* Upon connecting 3S LiPo main power, spinning, and dropping throttle, Motor 4 halts instantly and exhibits strong magnetic resistance when turned manually.
   - *Power Disconnected:* Motor 4 immediately returns to smooth, free rotation.
   - *Technical Cause:* ESC Channel 4 active braking / MOSFET bridge gate driver leakage creating a low-impedance electromagnetic damping loop across stator phases when energized.

**Status:** **ACTIVE BENCH ISOLATION** – Logged as Failure Mode 5 and Failure Mode 6 in [`docs/failure_logs.md`](file:///d:/MIT/Projects/Drone/quad-commutation-testbed/docs/failure_logs.md).

---

### Session 2026-09-25: Untethered Throttle Incident & Hardware Maintenance Hold

**Objectives:**
- Document untethered bench throttle test incident, PID windup behavior, and logistics for replacement components.

**Incident Summary & Observations:**
1. **Test Procedure:** Increased idle throttle from 5.5% to 7.0%. Conducted untethered throttle sweep on bench with propellers attached.
2. **PID Windup Behavior:** In ANGLE mode, initial throttle input caused uncontrollable PID I-term windup (FC commanding max correction on a stationary airframe) exacerbated by ESC 4 phase short asymmetry.
3. **Impact & Airframe Damage:** Dropping throttle to idle triggered sudden runaway RPM. Airframe collided with lab wall, resulting in 2 destroyed propellers and 1 cracked propeller.

**Hardware Orders & Procurement:**
- **Propellers:** 2 sets of 10-inch props ordered.
- **ESC:** 1 replacement 30A analog ESC ordered for Channel 4.

**Status:** **TESTING PAUSED** – Bench testing suspended pending delivery and installation of replacement ESC and propellers. All future props-on power testing strictly mandated on 15 kg dumbbell tethered anchor.











