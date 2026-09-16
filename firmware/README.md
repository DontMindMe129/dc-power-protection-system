# Firmware

Status: **not started**.

This directory will become a self-contained STM32 firmware project after requirements review. Candidate target: STM32F103C8T6 using C, STM32 HAL, CMake, Ninja and `arm-none-eabi-gcc`.

Planned user-owned modules include measurement, calibration, protection state machine, load-switch control, UI and diagnostics. Generated CubeMX files and user code must remain clearly separated.
