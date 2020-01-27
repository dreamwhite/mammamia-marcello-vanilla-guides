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
* [USBInjectAll](../../installation/kexts/usb/usbinjectall.md#download-link)

## Step 1: Installing the kext

[Mount EFI](../../bootloaders/mount-efi.md) and place in `/Volumes/EFI/EFI/CLOVER/kexts/Other` **USBInjectAll.kext**

![](../../.gitbook/assets/image%20%2867%29.png)

## Step 2: extract ACPI Tables

{% page-ref page="../../acpi/extracting-acpi-tables.md" %}

## Step 3: identify the SSDT which defines USB ports

From Intel Haswell generation onwards, USB ports are no more defined inside `DSDT.aml` but in an SSDT table.

On my machine, USB ports are defined inside `SSDT-2-xh_OEMBD.aml`. Just open every single `SSDT.aml` with MaciASL and look for a tree like depicted in the following screenshot

 

![\\_SB.PCI0.XHC.RHUB.HS01](../../.gitbook/assets/image%20%2850%29.png)

## Step 4: identify which port is active or not

{% hint style="info" %}
**SSxx**, where **SS** stands for **S**uper **S**peed, ports are for USB3.0 \(meaning that **HS01** can have **SS01** etc.\). Their maximum speed is 5 Gbps while for USB2.0 are 480 Mbps
{% endhint %}

Open Hackintool and go in USB section

![](../../.gitbook/assets/image%20%286%29.png)

Click on the `Clear` button \(the third button from left\)

![](../../.gitbook/assets/image%20%2857%29.png)

Then click on `Refresh` button \(the third from right\)

![](../../.gitbook/assets/image%20%2881%29.png)

Finally connect a USB 2.0 in each port and note the `Name` of the USB port \(e.g. HS01 for right port of mobo etc.\)  
Then remove any port that isn't highlighted with the second button.  
  
You should have a result like the depicted one below

![](../../.gitbook/assets/image%20%2864%29.png)

 

![](../../.gitbook/assets/image%20%2822%29.png)

## Step 5: setup the ports inside SSDT

Open the previously identified SSDT with MaciASL  


![](../../.gitbook/assets/image%20%2863%29.png)

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
| HS01 | USB 3 Power-B connector |
| HS03 | Type C connector - USB2 and SS with Switch |
| HS04 | USB 3 Standard-A Connector |
| HS05 | Proprietary connector |
| HS06 | Type ‘A’ connector |
| HS07 | Type ‘A’ connector |
| SS01 | USB 3 Standard-B connector |
| SS04 | USB 3 Standard-A connector |

## Step 6: add the SSDT methods

If we look closely to `GUPC` method, we can see that it assigns for each port the **Connector Type** _**Internal**._ We need to copy this method for defining the behaviour of USB2, USB3 and USB3 powered ports.

Just add those methods:

```text
        Method (USB2, 1, Serialized)
        {
            Name (PCKG, Package (0x04)
            {
                Zero, 
                0x00, //Proprietary connector aka USB2
                Zero, 
                Zero
            })
            PCKG [Zero] = Arg0
            Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.USB2.PCKG */
        }

        Method (USB3, 1, Serialized)
        {
            Name (PCKG, Package (0x04)
            {
                Zero, 
                0x03, //USB3 connector not powered
                Zero, 
                Zero
            })
            PCKG [Zero] = Arg0
            Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.USB3.PCKG */
        }

        Method (SB3P, 1, Serialized)
        {
            Name (PCKG, Package (0x04)
            {
                Zero, 
                0x07, //USB3 connector powered 
                Zero, 
                Zero
            })
            PCKG [Zero] = Arg0
            Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.SB3P.PCKG */
        }

```

Add those methods and replace `GUPC` with the method that defines the USB which we've discovered previously

Look at the figure below

![GUPC method which sets connector type as internal](../../.gitbook/assets/image%20%2827%29.png)

![S3BP which sets connector type to USB3 B Powered](../../.gitbook/assets/image%20%2899%29.png)

Save SSDT in `/Volumes/EFI/EFI/CLOVER/ACPI/patched` __remove `USBInjectAll.kext` from `/Volumes/EFI/EFI/CLOVER/kexts/Other` and reboot.

Repeat **Step 4** and you should see something like the depicted one below

![Mapped USB](../../.gitbook/assets/image%20%2811%29.png)

Enjoy your USB ports mapped

## Credits

* [Olarila Native USB FIX for desktop ](https://olarila.com/forum/viewtopic.php?f=28&t=10171)
  * it's valid also for laptops :\)







 





