---
description: Grant more stability to hackintosh
---

# Renames

## Marcello, what is it?

This is a collection of rename that are needed on macOS. This knowledge come from [here](https://applelife.ru/threads/dampy-originalnyx-makov.2943712/)

## What you doin'?

I'm trying to get my hackintosh more stable

## Mammamia Marcello, this is not how to do it

## What you mean?

## This is how to get a more stable hackintosh

### Requirements

* IORegistryExplorer
* [Mount EFI](../../bootloaders/mount-efi.md)
* Editing config.plist

## Step 1: determine which devices needs rename

{% hint style="warning" %}
Starting from macOS 10.15 Catalina, **EC** device is required for booting
{% endhint %}

Open IORegistryExplorer and look for the following devices:

* AC
* EHC1
* EHC2
* EUSB
* USBE
* GFX0
* PEGP
* HECI
* MEI
* SAT0
* LPC

Now take note of the devices that you've found previously and calculate the HEX of them as depicted below

## Step 2: add renames from Clover

{% hint style="info" %}
You can use **any** HEX calculator
{% endhint %}

{% hint style="info" %}
This method is preferred because, if there is any external reference to the renamed device, such as a SSDT,  it will be automatically renamed by Clover
{% endhint %}

Note that if the device name length is less than 4 char add as many "\_" as there are missing characters to reach the length of 4 \(e.g. `EC0` will be `EC0_` \)

![4543305f is EC0\_ in HEX](../../.gitbook/assets/image%20%2882%29.png)

![45435f5f is EC\_\_ in HEX](../../.gitbook/assets/image%20%2824%29.png)

Add the rename as depicted below



![](../../.gitbook/assets/image%20%2873%29.png)

Save `config.plist` and reboot

