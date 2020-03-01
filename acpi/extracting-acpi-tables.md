# Extracting ACPI tables

## Marcello, what is it?

An ACPI table is a AML file which... ehm... \*wtf is goin' on\*

For more infos check here:

* [kernel.org](https://www.kernel.org/doc/html/latest/arm64/acpi_object_usage.html)
* [OSDev Wiki](https://wiki.osdev.org/ACPI)
* [Microsoft Docs](https://docs.microsoft.com/en-us/windows-hardware/drivers/bringup/acpi-system-description-tables)

## What you doin'?

Extracting ACPI tables from my computer

## Mammamia Marcello, this is not how to extract ACPI tables

What you mean?

## This is how to extract them

## Requirements

* [Mount EFI](../bootloaders/mount-efi.md)
* MAMMAMIA

{% hint style="danger" %}
Don't extract ACPI tables using MaciASL or DPCIManager as they are already patched using DSDT with Clover/Opencore renames
{% endhint %}

While on Clover Boot Screen press **F1**

![](../.gitbook/assets/image%20%2852%29.png)

![If this menu doesn&apos;t open repeat the procedure using fn+F1](../.gitbook/assets/image%20%283%29.png)



