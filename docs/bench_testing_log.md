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

