# Generation 2: Bench Testing & Calibration Log

## Overview
This log tracks chronological bench testing sessions, multi-channel ESC calibration results, motor response synchronization, and operational diagnostic logs for the 500mm multirotor testbed.

---

## Log Entries

### Session 2026-09-25: Board Reflash & 4-Channel ESC Calibration

**Objectives:**
- Reflash Betaflight target to `DAKEFPVF405`.
- Restore 4-channel motor PWM output sync.
- Perform high/low throttle endpoint calibration across legacy 30A analog ESCs.

**Observations & Testing Steps:**
1. **Target Reflash:** Flashed target `DAKEFPVF405` onto DakeFPV F405 hardware. All 4 motor channels (M1–M4) initialized with standard ESC startup chime sequence.
2. **Initial Throttle Offset Anomaly:** Upon initial arming/throttle command, all 4 motors began spinning, but 2 motors responded at different pulse width thresholds (asymmetric idle onset).
3. **Multi-Channel ESC Calibration:**
   - Conducted full-scale PWM boundary sweep (2000µs high endpoint down to 1000µs low endpoint) via Betaflight Motor override under 3S LiPo power.
   - Synchronized high/low throttle threshold registers across all 4 legacy analog ESC microcontrollers.

**Verification & Results:**
- **Outcome:** All 4 BLDC motors now initialize and idle synchronously with a maximum initiation gap of **≤ 3µs** across all channels.
- **Status:** **PASS** – 4-channel commutation and PWM override validated for Schlieren optical thermal testing.
