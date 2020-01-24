---
description: The following is a collection of fixes that I've found during the time
---

# Fixes

### Trackpad is unresponsive and no trackpad preference panel in System Preferences

![From gitter.im/alexandred/VoodooI2C](../../.gitbook/assets/image%20%286%29.png)

### Missing SSCN or FMCN bus speed config

Open GenI2C, Diagnosis section

![](../../.gitbook/assets/image%20%2821%29.png)

#### Requirements

* Clover Configurator
* MaciASL
* IORegistryExplorer

#### Steps

1. [Mount EFI](../../efi/mount-efi.md)
2. Navigate to /Volumes/EFI/EFI/CLOVER/ACPI/patched
3. Open DSDT.aml with MaciASL
4. Search for "USTP"

![](../../.gitbook/assets/image%20%2818%29.png)

Change "USTP" to "One" as depicted in the following screenshot

![](../../.gitbook/assets/image%20%288%29.png)



