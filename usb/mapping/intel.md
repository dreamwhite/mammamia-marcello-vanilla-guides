---
description: USB mapping procedure
---

# Intel

{% hint style="info" %}
**Q:** Can this procedure break my hackintosh?  
**A:** The following procedure is safe your hackintosh. Just fix the ACPI errors.
{% endhint %}

##  Requirements

* DSDT/SSDT-USB.aml
* *  [Extracting ACPI tables](../../acpi/extracting-acpi-tables.md)
* MaciASL
* Hackintool
* Clover Configurator
* IORegistryExplorer
* [USBInjectAll](../../installation/kexts/usb/usbinjectall.md#download-link)

## Step 1: Adding port limit patch

With the release of every macOS version you'll probably need a port-limit removal patch to begin your USB configuration on a new build.

![Port Limit Patch for macOS 10.15.x](../../.gitbook/assets/image%20%28113%29.png)

Choose the patches for your OS version

### El Capitan \(10.11.x\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.driver.usb.AppleUSBXHCIPCI | 83BD8CFE FFFF10 | 83BD8CFE FFFF1B |

### Sierra \(10.12.x\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.driver.usb.AppleUSBXHCIPCI | 83BD74FF FFFF10 | 83BD74FF FFFF1B |

### High Sierra \(10.13.0 to 10.13.3\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.driver.usb.AppleUSBXHCIPCI | 837D8C10 | 837D8C1B |

### High Sierra \(10.13.4 to 10.13.5\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.driver.usb.AppleUSBXHCIPCI | 837D940F 0F839704 0000 | 837D940F 90909090 9090 |

### High Sierra \(10.13.6\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.driver.usb.AppleUSBXHCIPCI | 837D880F 0F83A704 0000 | 837D880F 90909090 9090 |

### Mojave \(10.14.x\)

| Name | Find | Replace |
| :--- | :--- | :--- |
| com.apple.iokit.IOUSBHostFamily | 83FB0F0F | 83FB3F0F |
| com.apple.iokit.IOUSBHostFamily | 83E30FD3 | 83E33FD3 |
| com.apple.driver.usb.AppleUSBXHCI | 83FB0F0F | 83FB3F0F |
| com.apple.driver.usb.AppleUSBXHCI | 83FF0F0F | 83FF3F0F |

### Catalina \(10.15.x\)

| Name | Find |  |
| :--- | :--- | :--- |
| com.apple.iokit.IOUSBHostFamily | 83FB0F0F | 83FB3F0F |
| com.apple.driver.usb.AppleUSBXHCI | 83F90F0F | 83F93F0F |

## Step 2: Installing the kext

[Mount EFI](../../bootloaders/mount-efi.md) and place in `/Volumes/EFI/EFI/CLOVER/kexts/Other` **USBInjectAll.kext**

![](../../.gitbook/assets/image%20%2874%29.png)

To ensure that the kext is correctly loaded in kernel cache type in a terminal window  


```text
kextstat | grep USBInjectAll
```

![In this case USBInjectAll kext is not loaded so isn&apos;t in kextcache](../../.gitbook/assets/image%20%28115%29.png)



## Step 3: extract ACPI Tables

{% page-ref page="../../acpi/extracting-acpi-tables.md" %}

## Step 4: identify the SSDT which defines USB ports

From Intel Haswell generation onwards, USB ports are no more defined inside `DSDT.aml` but in an SSDT table.

{% hint style="info" %}
Some newer machines have USB ports still defined in DSDT, just look for **HS01**
{% endhint %}

On my machine, USB ports are defined inside `SSDT-2-xh_OEMBD.aml`. Just open every single `SSDT-X-YYYYY.aml` with MaciASL and look for a tree like depicted in the following screenshot

 

![\\_SB.PCI0.XHC.RHUB.HS01](../../.gitbook/assets/image%20%2855%29.png)

## Step 5: drop SSDT table loading

In order to load custom USB SSDT, drop the SSDT table which defines it. 

According to [Advanced Configuration and Power Interface \(ACPI\) Specification, version 6.3](https://uefi.org/sites/default/files/resources/ACPI_6_3_May16.pdf), page [1009](https://uefi.org/sites/default/files/resources/ACPI_6_3_May16.pdf#page=1009), `DefinitionBlock` has the following syntax:

```text
DefinitionBlock (AMLFileName, TableSignature, ComplianceRevision, OEMID, TableID, OEMRevision)
{TermList}
```

| Parameter | Allowed value |
| :--- | :--- |
| AMLFileName | Desired name of the translated output AML file |
| TableSignature | 4-character ACPI signature |
| ComplianceRevision | 8-bit value |
| OEMID | 6-character string |
| TableId | 8-character string |
| OEMRevision | 32-bit value |
| TermList | Sequence of executable ASL expressions |

Open the SSDT and identify the `TableId` as depicted in the example below:

![&quot;xh\_OEMBD&quot; is the TableId](../../.gitbook/assets/image%20%2817%29.png)

After identifying the TableId of the SSDT that must be dropped add it inside `config.plist` 

![Clover Configurator](../../.gitbook/assets/image%20%2815%29.png)

Or via code:

```text
<key>DropTables</key>
		<array>
			<dict>
				<key>Signature</key>
				<string>SSDT</string>
				<key>TableId</key>
				<string>xh_OEMBD</string>
			</dict>
		</array>
```

## Step 6: identify which port is active or not

{% hint style="info" %}
**SSxx**, where **SS** stands for **S**uper **S**peed, ports are for USB3.0 \(meaning that **HS01** can have **SS01** etc.\). Their maximum speed is 5 Gbps while for USB2.0 are 480 Mbps
{% endhint %}

Open Hackintool and go in USB section

![](../../.gitbook/assets/image%20%286%29.png)

Click on the `Clear` button \(the third button from left\)

![](../../.gitbook/assets/image%20%2862%29.png)

Then click on `Refresh` button \(the third from right\)

![](../../.gitbook/assets/image%20%2893%29.png)

Finally connect a USB 2.0 in each port and note the `Name` of the USB port \(e.g. HS01 for right port of mobo etc.\)  
Then remove any port that isn't highlighted with the second button.  
  
You should have a result like the depicted one below

![](../../.gitbook/assets/image%20%2870%29.png)

 

![](../../.gitbook/assets/image%20%2825%29.png)

## Step 7: setup the ports inside SSDT

Open the previously identified SSDT with MaciASL  


![](../../.gitbook/assets/image%20%2869%29.png)

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

Then remove the unused ports from SSDT by applying the following patch

{% hint style="warning" %}
Replace **xx** with the unused port number previously found 
{% endhint %}

{% tabs %}
{% tab title="HSxx" %}
```text
into scope label \_SB.PCI0.XHC.RHUB.HSxx remove_entry;
```
{% endtab %}

{% tab title="SSxx" %}
```
into scope label \_SB.PCI0.XHC.RHUB.SSxx remove_entry;
```
{% endtab %}
{% endtabs %}

Finally remove the unused external references to unused ports as depicted below

![e.g. SS01 is unused therefore remove the external reference](../../.gitbook/assets/image%20%2871%29.png)

## Step 8: add the SSDT methods

If we look closely to `GUPC` method, we can see that it assigns for each port the **Connector Type** _**Internal**._ We need to copy this method for defining the behaviour of USB2, USB3 and USB3 powered ports.

Just add those methods using MaciASL patch menu:

{% tabs %}
{% tab title="USB2" %}
```text
# USB 2 METHOD #

into scope label \_SB.PCI0.XHC.RHUB insert
begin
Method ( USB2, 1, NotSerialized)\n
{\n
Name (PCKG, Package (0x04)\n
{\n
Zero, \n
0x00, //Proprietary connector aka USB2\n
Zero, \n
Zero\n
})\n
PCKG [Zero] = Arg0 //This tells to replace the first element of our Package with the Arg0 that is passed\n
Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.USB2.PCKG */
}
end;
```
{% endtab %}

{% tab title="USB3" %}
```
# USB 3.0 METHOD #
into scope label \_SB.PCI0.XHC.RHUB insert
begin
Method ( USB3, 1, NotSerialized)\n
{\n
Name (PCKG, Package (0x04)\n
{\n
Zero, \n
0x03, //Proprietary connector aka USB3\n
Zero, \n
Zero\n
})\n
PCKG [Zero] = Arg0 //This tells to replace the first element of our Package with the Arg0 that is passed\n
Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.USB3.PCKG */
}
end;
```
{% endtab %}

{% tab title="USB3 Powered" %}
```
# USB 3.0 Powered METHOD #
into scope label \_SB.PCI0.XHC.RHUB insert
begin
Method ( SB3P, 1, NotSerialized)\n
{\n
Name (PCKG, Package (0x04)\n
{\n
Zero, \n
0x07, //Proprietary connector aka USB3 Powered\n
Zero, \n
Zero\n
})\n
PCKG [Zero] = Arg0 //This tells to replace the first element of our Package with the Arg0 that is passed\n
Return (PCKG) /* \_SB_.PCI0.XHC_.RHUB.SB3P.PCKG */
}
end;
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
For better reading, thought is useless, just place the methods from bottom file next to **GUPC** method
{% endhint %}

Replace `GUPC` call with the method that defines the USB which we've discovered previously

Look at the figure below

![GUPC method which sets connector type as internal](../../.gitbook/assets/image%20%2831%29.png)

![S3BP which sets connector type to USB3 B Powered](../../.gitbook/assets/image%20%28117%29.png)

Save SSDT in `/Volumes/EFI/EFI/CLOVER/ACPI/patched` __remove `USBInjectAll.kext` from `/Volumes/EFI/EFI/CLOVER/kexts/Other` and reboot.

Repeat **Step 4** and you should see something like the depicted one below

![Mapped USB ports](../../.gitbook/assets/image%20%2850%29.png)

Enjoy your USB ports mapped

## Credits

* [MacOS86](https://www.macos86.it/topic/9-mappatura-porte-usb/page/1) Italian community







 





