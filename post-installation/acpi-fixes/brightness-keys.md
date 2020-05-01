---
description: ACPI fix to enable brightness keys
---

# Brightness Keys

{% hint style="success" %}
**THE FOLLOWING SECTION IS FREE OF TYPOS/BROKEN IMAGES/SH\*T**
{% endhint %}

{% hint style="danger" %}
AppleALC and VoodooHDA are not compatible together. Use only **ONE** of them
{% endhint %}

## Marcello, what is it?

If you already have a working brightness slides in System Preferences &gt; Displays, mapping brightness key ACPI/patched may be useful for you to have enable native brightness hotkeys

## What you doin'?

Enabling brightness hotkeys

## Mammamia Marcello, this is not how to enable audio on hackintosh

What you mean?

## This is how to enable audio on hackintosh

## Requirements

* [ACPIDebug kext](https://bitbucket.org/RehabMan/os-x-acpi-debug/downloads/)
* [MaciASL](https://github.com/acidanthera/MaciASL/releases)
* [SSDT-BKEY](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/SSDT-BKEY.aml)
* MAMMAMIA

### Step 1: put ACPIDebug.kext int kexts folder

Copy `ACPIDebug.kext` into `C/k/O` if you are using Clover Bootloader. Into `OpenCore/Kexts` if you are using OpenCore

![Figure 1: Clover, kexts/Other](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/ACPIDebug-kext.png)

### Step 2: include required patches into MaciASL

Open MaciASL &gt; Preferences and add RehabMan's ACPI patches repo: `http://raw.github.com/RehabMan/OS-X-ACPI-Debug/master`

![Figure 2: MaciASL, Preferences](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/1_brightness_keys.png)

### Step 3: patch DSDT to expose the right keys

Apply `Add DSDT Debug Methods` and `Instrument EC Queries` patches from the repo we previously added

![Figure 4: MaciASL, Patch](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/2_brightness-keys.png)

### Step 4: compile your DSDT and save it

Click `Compile`to compile your patched DSDT. Hopefully you won't face any errors, if you applied the patches correctly

![Figure 5: gfxutil -f HDEF](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/3_brightness-keys.png)

Then you can save it and put it into `patched` folder

### Step 5: restart your laptop

Press your brightness keys, one time each

Now open `Terminal` app and paste

```text
log show --last 5 | grep ACPIDebug
```

You should see something like this

![Figure 6: Terminal, brightness hotkeys](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/4_brightness-keys.png)

`_Q11` would be my brightness-down key, and `_Q12` would be brightness-up

### Step 6: modify this SSDT with your values

Put `SSDT-BKEY.aml` into `patched` folder, and replace `_Q11` and `_Q12` with the ones from your terminal output

Compile and save it

{% hint style="warning" %}
If it generates errors, change `PS2K` to `PS2X` according to the one found in your DSDT \(usually `PS2M`\)
{% endhint %}

You should now enjoy your functioning brightness keys

### Step 6: add renames to your config.plist

Lastly, add these renames to your config.plist, with your favourite plist editor

```text
<dict>
  <key>Comment</key>
  <string>_Q11 to XQ11</string>
  <key>Disabled</key>
  <false/>
  <key>Find</key>
  <data>X1ExMQ==</data>
  <key>Replace</key>
  <data>WFExMQ==</data>
</dict>
<dict>
  <key>Comment</key>
  <string>_Q12 to XQ12</string>
  <key>Disabled</key>
  <false/>
  <key>Find</key>
  <data>X1ExMg==</data>
  <key>Replace</key>
  <data>WFExMg==</data>
</dict>
```

![Figure 6: Clover Configurator, DSDT Patches](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/6d460bb49ac2e2205e975a7a83bcaa89be726641/.gitbook/assets/acpi-fixes/brightness-keys/bkeys-rename.png)

## Credits

* Rehabman for his [post](https://www.insanelymac.com/forum/topic/290687-hp-envy-17t-j000-quad-haswell-108510951010x1011x1012x1013x1014x/?do=findComment&comment=1997112) and patches

