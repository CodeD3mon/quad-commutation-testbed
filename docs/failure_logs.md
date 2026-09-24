# Generation 2 Failure & Diagnostic Logs

## Overview
This log documents system anomalies, diagnostic measurements, and bench test observations during Generation 2 migration.

### Failure Mode 1: Asymmetric Actuation (M3/M4 Inactive)
- **Symptom:** M1 and M2 responded to throttle input; M3 and M4 remained inactive.
- **Cause:** Timer channel collision on STM32F405 when configured for DSHOT protocol.
- **Resolution:** Reconfigured motor PWM protocol to legacy analog PWM (400Hz).

### Failure Mode 2: Receiver Frame Decoding Loss
- **Symptom:** FlySky IBUS telemetry link failure after firmware flashing.
- **Cause:** Restored UART port assignment for serial receiver (UART2 / IBUS).
- **Resolution:** Re-enabled serial receiver on active UART index.

### Failure Mode 3: Partial Motor Initialization & Calibration Timeout
- **Symptom:** Upon connecting 3S LiPo battery power, only 3 motors produce the initialization beeping sequence. None of the motors respond to FlySky FS-i6 / FS-iA6B control signals or complete throttle endpoint calibration.
- **Cause:** Incorrect flight controller target firmware flashed (`SPEEDYBEEF405V3` instead of `DAKEFPVF405`), creating timer pinout mismatches across motor pads.
- **Resolution:** **RESOLVED.** Reflashed board target to `DAKEFPVF405` and executed 4-channel ESC calibration sweep (2000µs–1000µs). All 4 motors now initialize and start idling synchronously (max 3µs gap).

### Failure Mode 4: Target Firmware Mismatch (Flashing `SPEEDYBEEF405V3` on DakeFPV F405 Hardware)
- **Symptom:** Unstable PWM output pin mapping, non-responsive motor channels, and receiver UART mapping failure.
- **Cause:** Flight controller hardware was identified as DakeFPV F405 (STM32F405), but was flashed with SpeedyBee F405 V3 firmware target during Betaflight cloud build selection. MCU pin assignments for motor outputs M3/M4 and serial receiver i-BUS input map to different physical GPIO/Timer pins between these board revisions.
- **Resolution:** **RESOLVED.** Flashed `DAKEFPVF405` target build onto MCU. Output pin mappings for M1–M4 and serial receiver UART correctly asserted.



