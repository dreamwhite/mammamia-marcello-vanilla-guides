---
description: Enable support for I2C trackpad
---

# VoodooI2C

## Marcello, what is it?

VoodooI2C is a project consisting of macOS kernel extensions that add support for I2C bus devices. The project is split into two main components: the core extension and various other satellite extensions.

## What you doin'?

Enabling trackpad on my hackintosh

## Mammamia Marcello, this is not how to enable trackpad on hackintosh

What you mean?

## This is how to enable trackpad on hackintosh

{% hint style="info" %}
**Q:** Can this procedure break my hackintosh?  
**A:** The following procedure is safe your hackintosh. Just fix the ACPI errors. 
{% endhint %}

{% hint style="danger" %}
Activating I2C devices requires **working** [EC](../../acpi/ec.md) and [patched framebuffer](../../graphics/intel.md)
{% endhint %}

## Requirements

* At least Intel Core Broadwell generation
* DSDT.aml
  *  [Extracting ACPI tables]()
* MaciASL
* Clover Configurator
* GenI2C
* VoodooI2C
* IORegistryExplorer

### Step 1: CPU Support

Open GenI2C and go in Diagnosis section

![](../../.gitbook/assets/image%20%2870%29.png)

{% hint style="danger" %}
If CPU Generation Support led is red you can't add support for I2C devices on your laptop
{% endhint %}

### Step 2: editing config.plist

{% hint style="warning" %}
The following patches prevent Apple I2C kexts from attaching to I2C controllers. Without them, VoodooI2C can't never attach
{% endhint %}

Open config.plist and add those patches:

```text
++++++++++++++++++++++++++++++++++++++

    Name:           com.apple.driver.AppleIntelLpssI2C
    Find:           494F4B69 74
    Replace:        494F4B69 73
    Comment:        Prevent Apple I2C kexts from attaching to I2C controllers, credit CoolStar

    Name:           com.apple.driver.AppleIntelLpssI2CController
    Find:           494F4B69 74
    Replace:        494F4B69 73
    Comment:        Prevent Apple I2C kexts from attaching to I2C controllers, credit CoolStar


Please use the Rename(s) above in the given order

++++++++++++++++++++++++++++++++++++++
```

![](../../.gitbook/assets/image%20%2852%29.png)

```text
<key>KextsToPatch</key>
		<array>          
			<dict>
				<key>Comment</key>
				<string>Prevent Apple I2C kexts from attaching to I2C controllers, credit CoolStar</string>
				<key>Disabled</key>
				<false/>
				<key>Find</key>
				<data>
				SU9LaXQ=
				</data>
				<key>InfoPlistPatch</key>
				<true/>
				<key>Name</key>
				<string>com.apple.driver.AppleIntelLpssI2C</string>
				<key>Replace</key>
				<data>
				SU9LaXM=
				</data>
			</dict>
			<dict>
				<key>Comment</key>
				<string>Prevent Apple I2C kexts from attaching to I2C controllers, credit CoolStar</string>
				<key>Disabled</key>
				<false/>
				<key>Find</key>
				<data>
				SU9LaXQ=
				</data>
				<key>InfoPlistPatch</key>
				<true/>
				<key>Name</key>
				<string>com.apple.driver.AppleIntelLpssI2CController</string>
				<key>Replace</key>
				<data>
				SU9LaXM=
				</data>
			</dict>
		</array>
	</dict>
```

Furthermore, force the loading of **IOGraphicsFamily.kext** by adding

`\System\Library\Extensions\IOGraphicsFamily.kext` inside config.plist/Kernel and Kext Patches/ForceKextsToLoad

![](../../.gitbook/assets/image%20%2844%29.png)

```text
<key>ForceKextsToLoad</key>
		<array>
			<string>\System\Library\Extensions\IOGraphicsFamily.kext</string>
		</array>
```



### Step 3: patching DSDT

After extracting DSDT.aml and cleaning it from errors, with MaciASL, open Patch menu and apply the following patches

```text
# Windows 10 DSDT Patch for VoodooI2C
# Allows I2C controllers and devices to be discovered by OS X.
# Based off patches written by RehabMan

into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2009\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2009"))) end;
into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2012\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2012"))) end;
into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2013\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2013"))) end;
into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2015\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2015"))) end;        
```

![Click on Apply, save DSDT.aml in /Volumes/EFI/EFI/CLOVER/ACPI/patched and reboot](../../.gitbook/assets/image%20%2885%29.png)

Save the file also in "Disassemled ASL File" for the next step

### Step 4: kexts installation

Add VoodooI2C.kext and the satellite kext  
More infos can be found [here](https://voodooi2c.github.io/#Satellite%20Kexts/Satellite%20Kexts)

![](../../.gitbook/assets/image%20%2814%29.png)

Furthermore don't forget to remove **VoodooPS2Mouse.kext** and **VoodooPS2Trackpad.kext** inside **VoodooPS2Controller.kext** to avoid trackpad conflicts

![](../../.gitbook/assets/image%20%2876%29.png)

![Open Contents folder](../../.gitbook/assets/image%20%2826%29.png)

![Open Plugins folder](../../.gitbook/assets/image%20%2860%29.png)

![Remove those two kexts](../../.gitbook/assets/image%20%2865%29.png)

![](../../.gitbook/assets/image%20%281%29.png)

![](../../.gitbook/assets/image%20%2821%29.png)

Finally copy **VoodooPS2Controller.kext** to _/Volumes/EFI/EFI/CLOVER/kexts/Other_ 



### Step 5: GenI2C

After saving the DSDT.dsl file, open GenI2C and go in GenSSDT section

![Tick &quot;Generate patches for this computer&quot; and click next](../../.gitbook/assets/image%20%2842%29.png)

![Select &quot;Interrupt \(APIC or GPIO\)&quot; and then click on Generate](../../.gitbook/assets/image%20%2812%29.png)

It will open a folder with Finder. Just copy the .aml file inside `/Volumes/EFI/CLOVER/ACPI/patched` directory

![](../../.gitbook/assets/image%20%2820%29.png)

Then open your config.plist and add the renames inside Readme.txt

![](../../.gitbook/assets/image%20%288%29.png)

![Clover Configurator](../../.gitbook/assets/image%20%289%29.png)

Save and reboot

### Step 6: configure trackpad

![System Preferences, Trackpad, Point &amp; Click](../../.gitbook/assets/image%20%2825%29.png)

![System Preferences, Trackpad, Scroll &amp; Zoom](../../.gitbook/assets/image%20%2879%29.png)

![](../../.gitbook/assets/image%20%2890%29.png)

**Enjoy the gestures &lt;3**

## **Credits**

* [https://voodooi2c.github.io/\#GPIO%20Pinning/GPIO%20Pinning](https://voodooi2c.github.io/#GPIO%20Pinning/GPIO%20Pinning)

### 

