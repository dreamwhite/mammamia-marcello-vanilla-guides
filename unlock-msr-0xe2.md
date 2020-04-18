---
description: Enable native power management
---

# Unlock MSR 0xE2

**MSR 0xE2** is a region of the BIOS which is essential for macOS: here the C-states of the CPU are written.

## What are C-states?

According to [Dell.com](https://www.dell.com/support/article/it-it/qna41893/what-is-the-c-state?lang=en):

In order to save energy when the CPU is idle, you can command the CPU to enter a low-power mode. Each CPU has several power modes, which are collectively called "C-states" or "C-modes.".

The lower-power mode was first introduced with the 486DX4 processor, so this concept is far from new. Newer modes have been enhanced to allow the CPU to consume even less power.

These modes work by cutting the clock signal and power from idle units inside the CPU. The more units that you stop by cutting the clock, reducing the voltage, or even completely shutting down, the more energy you save. However, it takes more time for the CPU to fully "wake up" from sleep mode.

These modes or "C-states" start at C0, which is the normal CPU operating mode \(the CPU is 100% activated\). The higher the C number is, the deeper into sleep mode the CPU goes. In other words, more circuits and signals are turned off, and the CPU takes more time to fully wake up into C0 mode.

Each C-state has a name, and several of them have submodes with different power-saving levels and wake-up times.

The table below summarizes all currently available C-state modes. Modes C1 to C3 work by cutting the CPU's clock signals, while modes C4 to C6 work by reducing the CPU's voltage. "Enhanced" modes can do both simultaneously.

| Mode | Name | What it does | CPUs |
| :--- | :--- | :---: | :--- |
| C0 | Operating State | CPU fully turned on | All CPUs |
| C1 | Halt | Stops CPU main internal clocks via software; bus interface unit and APIC are kept running at full speed | 486DX4 and above |
| C1E | Enhanced Halt | Stops CPU main internal clocks via software and reduces CPU voltage; bus interface unit and APIC are kept running at full speed | All socket 775 CPUs |
| C1E | — | Stops all CPU internal clocks | Turion 64, 65-nm Athlon X2 and Phenom CPUs |
| C2 | Stop Grant | Stops CPU main internal clocks via hardware; bus interface unit and APIC are kept running at full speed | 486DX4 and above |
| C2 | Stop Clock | Stops CPU internal and external clocks via hardware | Only 486DX4, Pentium, Pentium MMX, K5, K6, K6-2, K6-III |
| C2E | Extended Stop Grant | Stops CPU main internal clocks via hardware and reduces CPU voltage; bus interface unit and APIC are kept running at full speed | Core 2 Duo and above \(Intel only\) |
| C3 | Sleep | Stops all CPU internal clocks | Pentium II, Athlon and above, but not on Core 2 Duo E4000 and E6000 |
| C3 | Deep Sleep | Stops all CPU internal and external clocks | Pentium II and above, but not on Core 2 Duo E4000 and E6000; Turion 64 |
| C3 | AltVID | Stops all CPU internal clocks and reduces CPU voltage | AMD Turion 64 |
| C4 | Deeper Sleep | Reduces CPU voltage | Pentium M and above, but not on Core 2 Duo E4000 and E6000 series; AMD Turion 64 |
| C4E/C5 | Enhanced Deeper Sleep | Reduces CPU voltage even more and turns off the memory cache | Core Solo, Core Duo and 45-nm mobile Core 2 Duo only |
| C6 | Deep Power Down | Reduces the CPU internal voltage to any value, including 0 Volts | 45-nm mobile Core 2 Duo only |

## What is CFG Lock?

`CFG Lock` is a boolean bit, `true` or `false`, `0x1`or`0x0`. When this bit value is `0x1` it prevents macOS writing to the **MSR 0xE2** region, and without proper kernel patches it can't boot.

In Clover bootloader the patches are:

* `KernelPM`
* `KernelXCPM`

In OpenCore bootloader the patches are: 

* `AppleCpuPmCfgLock`
* `AppleXcpmCfgLock`

In order to grant native power management of CPU, you need to flip this bit to `0x0`.

## How to flip CFG Lock bit to 0x0?

{% hint style="info" %}
Many guides found on the web only mentions "BIOS firmware image". The purpose of the following guide is helping the people extract the BIOS Firmware image from their vendor BIOS upgrade package
{% endhint %}

### Requirements

* BIOS firmware image
* [UEFITool](https://github.com/LongSoft/UEFITool/releases)
* [Universal-IFR-Extractor](https://github.com/LongSoft/Universal-IFR-Extractor/releases)

## Step 1: getting the BIOS image from vendor

Below the list of the steps to take in order to extract the BIOS image from the BIOS upgrade package divided for vendor.

* ASRock
* ASUS
* [Dell](https://github.com/dreamwhite/bios-extraction-guide/blob/master/Dell/README.md)
* Gigabyte
* HP
* Lenovo
* MSI
* Xiaomi





### 

