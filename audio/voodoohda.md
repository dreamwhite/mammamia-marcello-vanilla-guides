---
description: Open source audio driver
---

# VoodooHDA

{% hint style="danger" %}
AppleALC and VoodooHDA are not compatible together. Use only **ONE** of them
{% endhint %}

## Marcello, what is it?

VoodooHDA is an open source audio driver for devices compliant with the Intel High Definition Audio \(HDA\) specification.

## What you doin'?

Enabling audio on my hackintosh

## Mammamia Marcello, this is not how to enable audio on hackintosh

What you mean?

## This is how to enable audio on hackintosh

## Requirements

* [VoodooHDA](https://github.com/chris1111/VoodooHDA-2.9.2-Clover-V15/releases)

### Installing VoodooHDA

Download the latest kext release and open the .pkg file

![Click on &quot;Continue&quot;](../.gitbook/assets/image%20%28108%29.png)

![Click on &quot;Continue&quot;](../.gitbook/assets/image%20%2896%29.png)

![Agree the license by clicking on &quot;Agree&quot;](../.gitbook/assets/image%20%2817%29.png)

![Click on &quot;Customize&quot;](../.gitbook/assets/image%20%2818%29.png)

![Expand &quot;VoodooHDA Clover UEFI/ESP&quot; if you&apos;re on a UEFI machine, else on &quot;Legacy&quot;](../.gitbook/assets/image%20%2846%29.png)

![Check only the macOS version you&apos;re using ](../.gitbook/assets/image%20%2860%29.png)

![](../.gitbook/assets/image%20%2822%29.png)

Click on Install, and enter your password when it's prompted.



### Uninstalling VoodooHDA

According to the previous screenshot, VoodooHDA installs 3 files:

* _VoodooHDA.kext_ inside /ESP/EFI/CLOVER/kexts/**10.xx**
* 
**/**Firstly [Mount EFI](../bootloaders/mount-efi.md) then remove VoodooHDA.kext as depicted below

![](../.gitbook/assets/image%20%2859%29.png)





### 









