---
description: The following is a collection of fixes that I've found during the time
---

# Fixes

### Trackpad is unresponsive and no trackpad preference panel in System Preferences

![From gitter.im/alexandred/VoodooI2C](../../.gitbook/assets/image%20%286%29.png)

### Missing SSCN or FMCN bus speed config

Open GenI2C, Diagnosis section

![](../../.gitbook/assets/image%20%2823%29.png)

#### Requirements

* Clover Configurator
* MaciASL
* IORegistryExplorer

#### Steps

1. Open IORegistryExplorer
2. [Mount EFI](../../efi/mount-efi.md)
3. Navigate to /Volumes/EFI/EFI/CLOVER/ACPI/patched
4. Open DSDT.aml with MaciASL
5. Search for "USTP"

![Step 1: search &quot;I2C&quot;](../../.gitbook/assets/image%20%289%29.png)

![](../../.gitbook/assets/image%20%2820%29.png)

Change "USTP" to "One" as depicted in the following screenshot

![Change &quot;If \(USTP&quot; to &quot;If \(One&quot;](../../.gitbook/assets/image%20%288%29.png)

Change "I2C1" to "I2C0" as depicted in the following screenshot

![Change &quot;If \(USTP&quot; to &quot;If \(One&quot;](../../.gitbook/assets/image%20%288%29.png)

![](../../.gitbook/assets/image%20%2818%29.png)

