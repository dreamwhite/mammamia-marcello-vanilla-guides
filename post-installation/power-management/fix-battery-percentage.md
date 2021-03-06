---
description: Battery percentage widget reports 0% of charge
---

# Fix missing battery percentage

## Marcello, what is it?

As **Rehabman** says in this [guide](https://www.tonymacx86.com/threads/guide-how-to-patch-dsdt-for-working-battery-status.116102/)...

"Because the battery hardware in PCs is not compatible with Apple SMbus hardware, we use ACPI to access battery state when running OS X on laptops. Generally I recommend you use ACPIBatteryManager.kext, available here: [https://github.com/RehabMan/OS-X-ACPI-Battery-Driver](https://github.com/RehabMan/OS-X-ACPI-Battery-Driver)

Later releases of AppleACPIPlatform are unable to correctly access fields within the EC \(embedded controller\). This causes problems for ACPIBatteryManager as the various ACPI methods for battery fail \(\_BIF, \_STA, \_BST, etc\). Although it is possible to use an older version of AppleACPIPlatform \(from Snow Leopard\), it is desirable to use the latest version of AppleACPIPlatform because with computers that have Ivy Bridge CPUs it enables native power management for those computers. To use the latest version, DSDT must be changed to comply with the limitations of Apple's AppleACPIPlatform.

In particular, any fields in the EC larger than 8-bit, must be changed to be accessed 8-bits at one time. This includes 16, 32, 64, and larger fields."

## What you doin'?

Trying to fix my battery percentage on my hackintosh

## Mammamia Marcello, this is not how to fix the battery on hackintosh

What you mean?

## This is how to fix it

## Requirements

* [Extracted DSDT.aml](../../tools/maciasl/extracting-acpi-tables.md)
* [MaciASL](fix-battery-percentage.md)
* MAMMAMIA
* Battery kext \(only one of them\)
  * [ACPIBatteryManager.kext](https://bitbucket.org/RehabMan/os-x-acpi-battery-driver/downloads/)
  * SMCBatteryManager \(bundled with VirtualSMC\)

{% hint style="danger" %}
Please note, the following procedure isn't intended for those who don't have basic knowledge of ACPI patching. Please go back if you don't know what are you doing
{% endhint %}

### Step 1: identify field name

Look for `EmbeddedControl` inside `DSDT.aml`

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2829%29.png)

Let's focus on the following lines:

```text
OperationRegion (ECRM, EmbeddedControl, Zero, 0xFF)
Field (ECRM, ByteAcc, NoLock, Preserve) 
{
    //...
}
```

According to AML language specification, variables are defined with a **identificator**, a string of 4 chars, and can't start with a number \(e.g. `0ABC` is an invalid name, `ABC0` is a valid name\), and the **size** of the variable expressed with decimal notation or even in hexadecimal notation.

Note down `ECRM` \(which we'll call `Field name`\) and proceed to the next step

### Step 2: note down the n\*8-bit fields

Let's identify the variables which size is more than 8 \(usually multiples, such as 16, 32 etc...\). Open a text editor and paste the whole `Field name` field content and make a list like the depicted below:

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2841%29.png)

Now search inside DSDT how many times the variable is used

![This variable is found only 1 time inside DSDT, so let&apos;s skip it and proceed to the next one](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2826%29.png)

![There&apos;s more than 1 match so let&apos;s note down the name of the variable, then look for the next one](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%285%29.png)

Iterate this process until you have a filtered list such as the below one:

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2834%29.png)

The above variables are used in DSDT at least one time, and need to be splitted into two \(or four if their size is 32\).

### Step 3: add the B1B2/B1B4 method

Imagine that the field is like a cracker: you have two pieces merged together. Splitting them into two pieces and placing side by side still is the same. We're applying the same method: we split the variable into two, or more, fields. To split them simply rename the variable into two, or more, variables whose size is of 8.

e.g.

```text
BDC,   16, // This variable has 16 as size

//let's split it, without making name conflicts in the DSDT
//let's split it, for example, with BDC0 and BDC1, so you'll have:

BDC0,   8,
BDC1,   8,
```

After splitting the variable into two, or more, variables and you try to compile the DSDT you'll get some errors, due to DSDT still trying to access to the original field \(`BDC`in the e.g.\). To fix this we'll use an utility called `B1B2` for **16-bit** fields, and `B1B4` for **32-bit** fields, which will take the **8-bit** variables as input and return a **16-bit** or **32-bit** field. Below the patches:

{% tabs %}
{% tab title="16-bit" %}
```text
#Maintained by: RehabMan for: Laptop Patches
#b1b2-method.txt

# created by RehabMan

into method label B1B2 remove_entry;
into definitionblock code_regex . insert
begin
Method (B1B2, 2, NotSerialized) { Return(Or(Arg0, ShiftLeft(Arg1, 8))) }\n
end;
```

This method takes two **8-bit** fields and return one **16-bit** field.
{% endtab %}

{% tab title="32-bit" %}
```text
#Maintained by: RehabMan for: Laptop Patches
#b1b4-method.txt

# created by RehabMan

into method label B1B4 remove_entry;
into definitionblock code_regex . insert
begin
Method (B1B4, 4, NotSerialized)\n
{\n
    Store(Arg3, Local0)\n
    Or(Arg2, ShiftLeft(Local0, 8), Local0)\n
    Or(Arg1, ShiftLeft(Local0, 8), Local0)\n
    Or(Arg0, ShiftLeft(Local0, 8), Local0)\n
    Return(Local0)\n
}\n
end;
```

This patch takes four **8-bit** fields as input and returns one **32-bit** field
{% endtab %}
{% endtabs %}

### Step 4: split the variables and fixing the errors

Look for each variable you've noted down before and split it into two variables as depicted below:

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2823%29.png)

`BCV4`is a field of 16bit which needs to be splitted into two sub-fields. How to do it?

Simply, rename, without making name conflicts, `BCV4` into `CV40` and `CV41` and try to compile

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2866%29.png)

You'll probably find some errors such as `Object does not exist (BCV4)`. To fix this error replace every single use of the variable with `B1B2(CV40, CV41)` as depicted below

![](https://github.com/dreamwhite/mammamia-marcello-vanilla-guides/tree/3e094b2a4c55a47687b1dc786680ba5f3a2a1494/.gitbook/assets/image%20%2873%29.png)

Iterate through this process until you've patched every variable noted down before.

The job isn't finished yet, you still need to find the other variables and patch them as soon as explained above. Look for your `Field name` obtained from the first step and repeat the patching process.

Finally save `DSDT.aml`in `ECAP` and reboot.

## Credits

* Rehabman for his contribute in the Hackintosh scene and the [battery patching guide](https://www.tonymacx86.com/threads/guide-how-to-patch-dsdt-for-working-battery-status.116102/)

