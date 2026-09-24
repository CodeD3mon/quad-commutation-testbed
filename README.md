# 500mm Multirotor Platform: Diagnostic Testbed

**Author:** Aayush Makkar  
**Target:** Embedded Systems, Control Theory, Hardware-in-the-Loop Diagnostic Analysis  
**Status:** Active Bench Testing (Generation 2)

This repository tracks the architecture evolution, firmware configurations, and failure mode analysis of a custom 500mm multirotor platform. Originally built on an 8-bit AVR architecture, the system has been migrated to a 32-bit STM32F405 flight controller to enable low-level PWM override for static thermal diagnostics and commutation testing.

## Core Hardware Stack
* **Airframe:** 500mm Diagonal Wheelbase (TBS 500 Geometry)
* **Avionics:** SpeedyBee F405 V3 (STM32F405 MCU)
* **Firmware:** Betaflight 4.4+ (Custom Cloud Build)
* **Actuation:** 4x 1000KV Sensorless BLDC motors 
* **Speed Controllers:** Legacy 30A Linear Analog ESCs (Modified for isolated common-rail power)

## Technical Documentation
* [Architecture Evolution & Commutation Analysis](docs/hardware_architecture.md) 
  *Read this dossier for the root-cause engineering analysis regarding the STM32 migration, timer resource collisions, ground loop isolation, and Betaflight PWM down-sampling methodology.*
