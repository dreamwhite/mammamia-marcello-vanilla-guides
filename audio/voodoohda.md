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

![Click on &quot;Continue&quot;](../.gitbook/assets/image%20%28121%29.png)

![Click on &quot;Continue&quot;](../.gitbook/assets/image%20%28107%29.png)

![Agree the license by clicking on &quot;Agree&quot;](../.gitbook/assets/image%20%2820%29.png)

![Click on &quot;Customize&quot;](../.gitbook/assets/image%20%2821%29.png)

![Expand &quot;VoodooHDA Clover UEFI/ESP&quot; if you&apos;re on a UEFI machine, else on &quot;Legacy&quot;](../.gitbook/assets/image%20%2854%29.png)

![Check only the macOS version you&apos;re using ](../.gitbook/assets/image%20%2869%29.png)

![](../.gitbook/assets/image%20%2825%29.png)

Click on Install, and enter your password when it's prompted.



### Uninstalling VoodooHDA

According to the previous screenshot, VoodooHDA installs 3 files:

* **VoodooHDA.kext** inside /ESP/EFI/CLOVER/kexts/**10.xx**
* **VoodooHDA.prefPane** inside /Library/PreferencePanes
* **getdump** inside /usr/local/bin/getdump

To remove the kext simply [mount EFI](../bootloaders/mount-efi.md) then remove VoodooHDA.kext as depicted below

![](../.gitbook/assets/image%20%2868%29.png)

![](../.gitbook/assets/image%20%2816%29.png)

![Remove VoodooHDA.kext](../.gitbook/assets/image%20%2838%29.png)

![If there are no more other kexts remove the 10.xx folder ](../.gitbook/assets/image%20%2830%29.png)

To remove the preference pane just open System Preferences as depicted below

![](../.gitbook/assets/image%20%2829%29.png)

![Right click on VoodooHDA icon and remove the preference pane](../.gitbook/assets/image%20%28138%29.png)

![](../.gitbook/assets/image%20%2865%29.png)

Finally, remove **getdump** by opening a terminal window:

Get the path to the executable with

```bash
which getdump
```

![/usr/local/bin/getdump is the path](../.gitbook/assets/image%20%286%29.png)

Then remove the executable with

```bash
# ONLY FOR NOOBS #

sudo rm -r /usr/local/bin/getdump

# OR FOR REAL PROs #

sudo rm -r $(which getdump)
```

![](../.gitbook/assets/image%20%28137%29.png)

{% hint style="success" %}
VoodooHDA is successfully uninstalled from your system. Remember to reboot in order to apply changes
{% endhint %}











### 









