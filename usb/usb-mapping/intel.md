---
description: USB mapping procedure
---

# Intel

{% hint style="info" %}
**Q:** Can this procedure break my hackintosh?  
**A:** The following procedure is safe your hackintosh. Just fix the ACPI errors.
{% endhint %}

##  Requirements

* SSDT-USB.aml
* *  [Extracting ACPI tables](../../acpi/extracting-acpi-tables.md)
* MaciASL
* Hackintool
* Clover Configurator
* IORegistryExplorer
* [USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/RehabMan-USBInjectAll-2018-1108.zip)

## Step 1: Installing the kext

[Mount EFI](../../bootloaders/mount-efi.md) and place in _/Volumes/EFI/EFI/CLOVER/kexts/Other_ **USBInjectAll.kext**

![](../../.gitbook/assets/image%20%2834%29.png)

## Step 2: Extract ACPI Tables

{% page-ref page="../../acpi/extracting-acpi-tables.md" %}

## Step 3: identify the SSDT which defines USB ports

From Intel Haswell generation onwards, USB ports are no more defined inside `DSDT.aml` but in an SSDT table.

On my machine, USB ports are defined inside `SSDT-2-xh_OEMBD.aml`. Just open every single `SSDT.aml` with MaciASL and look for a tree like depicted in the following screenshot

 

![\\_SB.PCI0.XHC.RHUB.HS01](../../.gitbook/assets/image%20%2828%29.png)

## Step 4: identify which port is active or not

Open Hackintool and go in USB section

![](../../.gitbook/assets/image%20%2827%29.png)





