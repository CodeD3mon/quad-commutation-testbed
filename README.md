# 500mm Multirotor Platform: Diagnostic Testbed

**Author:** Aayush Makkar  
**Target:** Embedded Systems, Control Theory, Hardware-in-the-Loop Diagnostic Analysis  
**Status:** Active Bench Testing (Generation 2)

This repository tracks the architecture evolution, firmware configurations, and failure mode analysis of a custom 500mm multirotor platform. Originally built on an 8-bit AVR architecture, the system has been migrated to a 32-bit STM32F405 flight controller to enable low-level PWM override for static thermal diagnostics and commutation testing.

## Core Hardware Stack
* **Airframe:** 500mm Diagonal Wheelbase (TBS 500 Geometry)
* **Avionics:** DakeFPV F405 (STM32F405 MCU)
* **Radio Link:** FlySky FS-i6 Transmitter & FlySky FS-iA6B Receiver (i-BUS protocol)
* **Power:** 3S LiPo Battery (Direct Rail)
* **Firmware:** Betaflight 4.4+ (Target: DAKEFPVF405)
* **Actuation:** 4x 1000KV Sensorless BLDC motors 
* **Speed Controllers:** Legacy 30A Linear Analog ESCs (Modified for isolated common-rail power)

## Technical Documentation & Logs
* [Architecture Evolution & Commutation Analysis](docs/hardware_architecture.md)  
  *Root-cause engineering analysis regarding STM32 timer resource collisions, ground loop isolation, and Betaflight PWM down-sampling.*
* [Bench Testing & Calibration Log](docs/bench_testing_log.md)  
  *Operational test session logs, multi-channel ESC calibration results, and motor startup synchronization data.*
* [Failure & Diagnostic Anomaly Logs](docs/failure_logs.md)  
  *FMEA anomaly documentation, firmware target mismatch analysis, and troubleshooting steps.*

## Visual Documentation & Hardware Media
* **Full Assembly View:** ![Full 500mm Multirotor Platform](assets/hardware/full_quadcopter_assembly_top_view.jpg)
* **Flight Controller Pinout Macro:** ![DakeFPV F405 Pinout](assets/hardware/dakefpv_f405_pinout_macro.jpg)
* **Avionics & Receiver Wiring:** ![FC & FlySky FS-iA6B Wiring](assets/hardware/fc_and_receiver_wiring.jpg)
* **Post-Crash Propeller Damage:** ![Damaged 10-inch Propeller](assets/hardware/propeller_crash_damage.jpg)
* **Video Walkthrough:** [Bench Test Hardware Walkaround (MP4)](assets/media/bench_test_walkaround.mp4)


