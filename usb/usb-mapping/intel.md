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

![](../../.gitbook/assets/image%20%2839%29.png)

## Step 2: extract ACPI Tables

{% page-ref page="../../acpi/extracting-acpi-tables.md" %}

## Step 3: identify the SSDT which defines USB ports

From Intel Haswell generation onwards, USB ports are no more defined inside `DSDT.aml` but in an SSDT table.

On my machine, USB ports are defined inside `SSDT-2-xh_OEMBD.aml`. Just open every single `SSDT.aml` with MaciASL and look for a tree like depicted in the following screenshot

 

![\\_SB.PCI0.XHC.RHUB.HS01](../../.gitbook/assets/image%20%2832%29.png)

## Step 4: identify which port is active or not

Open Hackintool and go in USB section

![](../../.gitbook/assets/image%20%2812%29.png)

Click on the Clear button \(the third button from left\)

![](../../.gitbook/assets/image%20%2819%29.png)

Then click on Refresh button

![](../../.gitbook/assets/image%20%282%29.png)

Finally connect a USB 2.0 in each port and note the `Name` of the USB port \(e.g. HS01 for USB3.0 etc.\)  
Then remove any port that isn't highlighted with the second button.  
  
You should have a result like the depicted one below

 

![](../../.gitbook/assets/image%20%281%29.png)

## Step 5: setup the ports inside SSDT

Open the previously identified SSDT with MaciASL  


![](../../.gitbook/assets/image%20%2837%29.png)

According to [Advanced Configuration and Power Interface \(ACPI\) Specification, version 6.3](https://uefi.org/sites/default/files/resources/ACPI_6_3_May16.pdf), page [673](https://uefi.org/sites/default/files/resources/ACPI_6_3_May16.pdf#page=673), `_UPC` method return the following Package:

```text
Return Value Information:
Package {
 Connectable // Integer (BYTE)
Type // Integer (BYTE)
 Reserved0 // Integer
 Reserved1 // Integer)
}
```

| Parameter | Values | Explanation |
| :--- | :--- | :--- |
| **Connectable** | Zero/One | Disabled port/Enabled port |
| **Type** | table below | Specifies the host connector type |

| Hex | USB Type |
| :--- | :--- |
| 0x00 | Type ‘A’ connector |
| 0x01 | Mini-AB connector |
| 0x02 | ExpressCard |
| 0x03 | USB 3 Standard-A connector |
| 0x04 | USB 3 Standard-B connector |
| 0x05 | USB 3 Micro-B connector |
| 0x06 | USB 3 Micro-AB connector |
| 0x07 | USB 3 Power-B connector |
| 0x08 | Type C connector - USB2-only |
| 0x09 | Type C connector - USB2 and SS with Switch |
| 0x0A | Type C connector - USB2 and SS without Switch |
| 0x0B-0xFE | Reserved |
| 0xFF | Proprietary connector |

Now just look for each port you've discovered before and fill a table like the below one e.g.

| Name | Connector type |
| :--- | :--- |
| HS01 | USB 3 Standard-A connector |







