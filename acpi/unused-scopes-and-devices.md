---
description: Many devices defined in DSDT aren't used in macOS
---

# Remove unused scopes and devices

## Marcello, what is it?

macOS kexts attaches to some devices that are defined in DSDT. But many of them aren't necessary 

## What you doin'?

Removing unused scopes and devices from my hackintosh

## Mammamia Marcello, this is not how to remove unused scopes and devices from hackintosh

What you mean?

## This is how to remove unused scopes and devices from hackintosh

{% hint style="info" %}
**Q:** Can this procedure break my hackintosh?  
**A:** The following procedure is safe your hackintosh. Just fix the ACPI errors. 
{% endhint %}

## Requirements

* DSDT.aml
* *  [Extracting ACPI tables](extracting-acpi-tables.md)
* [MaciASL](../tools/useful-tools/maciasl.md)
* [Mount EFI](../bootloaders/mount-efi.md)
* [IORegistryExplorer](../tools/debugging/ioregistryexplorer.md)
* IOReg export from same SMBIOS Mac

### Step 1: identify unused devices

Open IORegistryExplorer and the previously downloaded same SMBIOS Mac IORegistryExplorer exported file.

![e.g. SMBIOS is MacBookPro15,2 ](../.gitbook/assets/image%20%2814%29.png)





### 

