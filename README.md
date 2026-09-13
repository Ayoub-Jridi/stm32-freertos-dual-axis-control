# stm32-freertos-dual-axis-control
FreeRTOS-based STM32 motion control system driving a servo and 28BYJ-48 stepper motor using DMA-assisted dual-channel ADC sampling and PWM generation. This project was developped on an stm32-f746ZG nucleo-board.

## 🌟 Key Features

- **Multi-Task Operating System:** Managed via **CMSIS-RTOS v2 (FreeRTOS)** for concurrent task scheduling and mutex protection.
- **DMA-Accelerated ADC:** Samples 2 analog channels (`ADC3_IN4` & `ADC3_IN5`) in continuous scan mode without blocking the CPU.
- **Noise-Filtered Signal Processing:** Implements a custom deadband hysteresis algorithm (`compare()`) to suppress sensor noise before triggering motor movements.
- **Direct PWM Servo Mapping:** Converts 12-bit ADC raw values ($0 \dots 4095$) directly into timer pulse widths for responsive servo positioning.
- **Thread-Driven Stepper Control:** Offloads 28BYJ-48 stepper stepping sequences to a dedicated low-priority FreeRTOS thread (`myTask02`).
- **Debounced External Interrupts:** Hardware button handling via `EXTI15_10` with software tick debouncing to dynamically start/stop ADC DMA acquisitions.

---

## 🛠️ Hardware Configuration

| Peripheral | Instance | Configured Mode / Purpose |
| :--- | :--- | :--- |
| **Microcontroller** | STM32 (ARM Cortex-M) | Main MCU |
| **ADC** | `ADC3` | 12-bit Resolution, Scan Mode, DMA Continuous |
| **DMA** | `DMA2 Stream 0` | Memory-to-Peripheral transfers for ADC3 |
| **Timers** | `TIM9` | Channel 2 PWM Output (Servo signal) |
| | `TIM3` | Microsecond timebase initialization |
| | `TIM2` | 32-bit free-running timer |
| **GPIO / EXTI** | `PC12` / `EXTI15_10` | External Interrupt with 70ms software debounce |
| **Motors** | 1x Servo Motor | Driven via TIM9 PWM pulse width |
| | 1x 28BYJ-48 Stepper | Driven via `STEPPER_28BYJ` library interface |
