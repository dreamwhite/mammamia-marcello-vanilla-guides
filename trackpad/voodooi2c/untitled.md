# Expert mode

The following section is only for expert. Don't follow these steps if you don't know what are you doing

### Requirements <a id="requirements"></a>

* the same as before lmao

## Manual GPIO Pinning <a id="manual-gpio-pinning"></a>

**You must apply this process for each I2C device you wish to use**.

### Get ACPI ID using Windows Device Manager <a id="get-acpi-id-using-windows-device-manager"></a>

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzImQenF-Cog1yCLqaL%2F-LzImy5wO7m868iNrnLE%2Fimage.png?alt=media&token=108a99b1-2888-4339-8106-b007260e21f2)

\\_SB.PCI0.I2C0.TPL0 is the ACPI ID of the trackpad

### MaciASL <a id="maciasl"></a>

After extracting DSDT.aml and cleaning it from errors, with MaciASL, open Patch menu and apply the following patches

```text
# Windows 10 DSDT Patch for VoodooI2C# Allows I2C controllers and devices to be discovered by OS X.# Based off patches written by RehabMan​into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2009\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2009"))) end;into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2012\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2012"))) end;into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2013\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2013"))) end;into_all method code_regex If\s+\([\\]?_OSI\s+\(\"Windows\s2015\"\)\) replace_matched begin If(LOr(_OSI("Darwin"),_OSI("Windows 2015"))) end;        
```

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzI_kiUfZn2d5fK0Ufq%2F-LzIaKrHrrtz4w43iI2Q%2Fimage.png?alt=media&token=26436759-812d-40ac-8b67-f4610ea85997)

Click on Apply, save DSDT.aml in /Volumes/EFI/CLOVER/ACPI/patched and reboot

### IORegistryExplorer <a id="ioregistryexplorer"></a>

Open IORegistryExplorer and look for "GPIO"

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzIkYSnxEBkM1yuqNmt%2F-LzIkxF099BV_Lh11PE4%2Fimage.png?alt=media&token=9641aa87-1cf5-4531-8142-1c2c231ba20e)

In this case we have a VoodooGPIOSunrisePointLP controller

Search for "I2C"

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzImQenF-Cog1yCLqaL%2F-LzInyddlEGKIZzyQEKW%2Fimage.png?alt=media&token=9db39853-284b-44f0-b716-e951c028e3c9)

The ACPI ID is: \\_SB.PCI0.I2C0.TPD0

​

### Interrupt Pin <a id="interrupt-pin"></a>

The interrupt pin can be found in two ways:

* MaciASL
* IORegistryExplorer

#### MaciASL <a id="maciasl-1"></a>

Open DSDT.aml and search "TPD0"

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzImQenF-Cog1yCLqaL%2F-LzIoikKbBWLemkDbWdN%2Fimage.png?alt=media&token=c2282d22-502c-4c54-9370-cfb11948149f)

Now look for SBFI which tells us the interrupt pin

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzImQenF-Cog1yCLqaL%2F-LzIoqV50dj4WWwcI0Te%2Fimage.png?alt=media&token=4902ce29-9b17-4bc6-84e0-22ecfa6dc91e)

In this case the interrupt pin is 0x00000033

#### IORegistryExplorer <a id="ioregistryexplorer-1"></a>

Open IORegistryExplorer and search "TPD0". You should get something similar to this

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzImQenF-Cog1yCLqaL%2F-LzIpezSJCSXDjLpnUqM%2Fimage.png?alt=media&token=8954a194-0317-4e4f-a9f6-8dd345cd9eab)

In this case the interrupt pin is 0x00000033

If your hexadecimal pin number is greater than `0x2F` then proceed to the next step.

 **Step 2a: Ensuring your device is GPIO-pinned**

You have arrived at this step because Apple's drivers do not support APIC pins greater than `0x2F` \(and it would be very difficult to make them support them\). In this case, we thus make use of a kext called [VoodooGPIO](https://github.com/coolstar/VoodooGPIO) which comes bundled with all copies of VoodooI2C. VoodooGPIO allows us to get around this limitation of macOS by using GPIO interrupts instead which most new machines support.

We must first determine whether or not your device is properly configured to support GPIO pins. Search for your device ACPI ID in your DSDT until you reach its device entry. Look for a `Name` that looks like this:

```text
    Name (SBFG, ResourceTemplate ()    {        GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,            "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,            )            {   // Pin list                0x0000            }    })
```

This `Name` may appear in the root level of your device entry or it could possibly appear in the `_CRS` method. In the first case we shall say that your device is **root pinned**. In the second case, we shall say that your device is **CRS pinned**. If, furthermore, the numbers that appear under pin list are non-zero then we shall furthermore append **well-** to the previous names as follows: **well-root pinned** and **well-CRS pinned**. We shall just say **well-pinned** to mean either of these latter cases. If you cannot find such a `Name` then we shall say that your device is **unpinned**.

The purpose of these names is merely for ease of communication in this documentation - there are many possible cases out in the wild and we aim to cover them all in this guide.

If your device is unpinned, proceed to Step 2b. If your device is pinned but not well-pinned, proceed to Step 2c. If your device is well-pinned then proceed to Step 2e.

**Step 2b: Adding in the missing Resource Template**

If your device is unpinned, insert the following into the root of your device's entry:

```text
    Name (SBFG, ResourceTemplate ()    {        GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,            "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,            )            {   // Pin list                0x0000            }    })
```

You may now consider your device to be root pinned \(but not well-root pinned\) and can proceed to Step 2d.

**Step 2c: Ensuring your device is well-pinned**

Even if the number appearing in the above `Name` is `0x0`, it is still possible that your device is well-pinned. It is fairly easy to determine whether or not it is indeed well-pinned. Find the `_CRS` method of your device. If you can find a line that looks like this:

```text
        Return (ConcatenateResTemplate (SBFB, SBFG))
```

Then your device is well-pinned. **Warning: this is not the same as the following:**

```text
        Return (ConcatenateResTemplate (SBFB, SBFI))
```

**you need** **`SBFG`** **and not** **`SBFI`**.

If you have now determined that your device is well-pinned, proceed to Step 2e. If your device is definitely not well-pinned then proceed to Step 2d.

**Step 2d: Manually pinning your device**

We now come to the task of manually assigning a GPIO pin to your device. This is potentially a tricky task as there is sometimes some trial and error involved.

Consult the list found here \([Sunrise Point](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/skylake/include/soc/gpio_defs.h#L43), [Cannon Point-LP](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/cannonlake/include/soc/gpio_defs.h#L42), [Cannon Point-H](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/cannonlake/include/soc/gpio_defs_cnp_h.h#L42)\). Look up your device's hexadecimal APIC pin number in the right hand column. The corresponding label on the left hand side is of the form `GPP_XYY_IRQ` - take a note of this label. Now consult the second list found here \([Sunrise Point](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/skylake/include/soc/gpio_soc_defs.h#L37), [Cannon Point-LP](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/cannonlake/include/soc/gpio_soc_defs.h#L45), [Cannon Point-H](https://github.com/coreboot/coreboot/blob/master/src/soc/intel/cannonlake/include/soc/gpio_soc_defs_cnp_h.h#L40)\). Look up the label you took a note of in the list \(note that 'IRQ' is no longer in the label name, this doesn't matter\). The corresponding number on the right is your **decimal GPIO pin number**.

On newer chipsets \(Cannon Point and newer\), there is a mismatch between the GPIO pin and the hardware pin number. If you are using such hardware, you will need to convert the hardware pin number you have found to a GPIO pin, to allow the driver to use the pin. Otherwise skip to the next paragraph. Consult the list found here \([Cannon Point-LP](https://github.com/coolstar/VoodooGPIO/blob/master/VoodooGPIO/CannonLake-LP/VoodooGPIOCannonLakeLP.hpp#L366), [Cannon Point-H](https://github.com/coolstar/VoodooGPIO/blob/master/VoodooGPIO/CannonLake-H/VoodooGPIOCannonLakeH.hpp#L414)\). GPIO pins are divided to multiple communities, containing multiple GPPs each. During the mapping from an APIC pin to a GPIO pin you have found a label in the form `GPP_XYY_IRQ`, or `GPP_XYY`. `X` is the GPP identifier you are looking for. Find the GPP of the pin you have found in one of the communities in the link. A GPP will have the form `CHIPSET_GPP(num, base, end, gpio_base), /* GPP_X */`. To convert your hardware pin to a usable GPIO pin, take the `decimal GPIO pin number` you have found, subtract the `base`, then add the `gpio_base`. The result is your GPIO pin number. Notice a `gpio_base` of `X_NO_GPIO` means this pin is not valid for use.

`Usable GPIO pin number = (decimal GPIO pin number) - base + gpio_base`

Convert this to a hexadecimal number, this is your **hexadecimal GPIO pin**. You may notice that your hexadecimal APIC pin appears multiple times in the first list mentioned above. You will need to repeat the lookup process for all occurences of your hexadecimal APIC pin to obtain all possible hexadecimal GPIO pins. You will then need to test all of them to see which one works.

Note that \(in very rare cases\), the corresponding GPIO hexadecimal pin will not work. In this case, you can try some of the common values such as `0x17`, `0x1B`, `0x34` and `0x55`.

Once you have a \(candidate\) hexadecimal GPIO pin, you can add it into the `SBFG` name under the `// Pin List` comment as follows \(for example, if your hexadecimal GPIO pin is 0x17\):

```text
    Name (SBFG, ResourceTemplate ()    {        GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,            "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,            )            {   // Pin list                0x17            }    })
```

Your device is now well-pinned and you may proceed to Step 2e.

**Step 2e: Ensuring your DSDT notifies the system that your device is GPIO pinned**

Finally, make sure that there are no other `Return` statements in your `_CRS` method apart from the following at the end:

```text
    Return (ConcatenateResTemplate (SBFB, SBFG))
```

This completes the GPIO pinning process for your device.

### Kexts Installation <a id="kexts-installation"></a>

Add VoodooI2C.kext and the satellite kext More infos can be found [here](https://voodooi2c.github.io/#Satellite%20Kexts/Satellite%20Kexts)​

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-Lz9HmN8eqkDm_mnJR3I%2F-LzIkYSnxEBkM1yuqNmt%2F-LzIll1t8iqjFJo4jJjR%2Fimage.png?alt=media&token=c94e2b62-fc8a-4c08-b57b-48f395f84898)

Then reboot and enjoy your trackpad gesture :\)

### ​ <a id="undefined"></a>

