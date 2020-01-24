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
* [USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/RehabMan-USBInjectAll-2018-1108.zip)

## Step 1: Installing the kext

[Mount EFI](../../bootloaders/mount-efi.md) and place in _/Volumes/EFI/EFI/CLOVER/kexts/Other_ **USBInjectAll.kext**

![](../../.gitbook/assets/image%20%2832%29.png)



