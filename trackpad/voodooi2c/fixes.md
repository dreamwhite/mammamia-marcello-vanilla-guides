---
description: The following is a collection of fixes that I've found during the time
---

# Fixes

### Trackpad is unresponsive and no trackpad preference panel in System Preferences

![From gitter.im/alexandred/VoodooI2C](../../.gitbook/assets/image%20%2827%29.png)

### Missing SSCN or FMCN bus speed config

`SSCN` and `FMCN` contain information about the I2C bus. Those values depends by the hardware and need to be fixed by hand.

![From gitter.im/alexandred/VoodooI2C](../../.gitbook/assets/image%20%2823%29.png)

Open GenI2C, Diagnosis section

![](../../.gitbook/assets/image%20%28108%29.png)

#### Requirements

* Clover Configurator
* MaciASL
* IORegistryExplorer

#### Steps

1. Open IORegistryExplorer
2. [Mount EFI](../../bootloaders/mount-efi.md)
3. Navigate to /Volumes/EFI/EFI/CLOVER/ACPI/patched
4. Open DSDT.aml with MaciASL

![Step 1: search &quot;I2C&quot;](../../.gitbook/assets/image%20%2847%29.png)

![](../../.gitbook/assets/image%20%2884%29.png)

Change `USTP` to `One` as depicted in the following screenshot

![Change &quot;If \(USTP\)&quot; in &quot;If \(One&quot;\)](../../.gitbook/assets/image%20%2840%29.png)

Change `I2C1` to `I2C0`

![Change \_SB.PCI0.I2C1 to...](../../.gitbook/assets/image%20%2814%29.png)

![](../../.gitbook/assets/image%20%2874%29.png)

Save and reboot and test again with GenI2C diagnosis section

It should be as depicted in the following screenshot

![No more &quot;Missing SSCN or FFMC bus speed config&quot;](../../.gitbook/assets/image%20%2859%29.png)

