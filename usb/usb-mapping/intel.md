---
description: USB mapping procedure
---

# Intel

{% hint style="info" %}
**Q:** Can this procedure break my hackintosh?  
**A:** The following procedure is safe your hackintosh. Just fix the ACPI errors.
{% endhint %}

##  Requirements

* DSDT.aml
* *  [Extracting ACPI tables]()
* MaciASL
* Clover Configurator
* USBMap
* IORegistryExplorer
* USBInjectAll

## Step 1

[Mount EFI](../../bootloaders/mount-efi.md) and place in _/Volumes/EFI/EFI/CLOVER/kexts/Other_ USBInjectAll.kext



