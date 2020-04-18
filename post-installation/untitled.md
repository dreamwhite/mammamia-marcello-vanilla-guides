# Fix missing battery percentage

Look for `EmbeddedControl` inside `DSDT.aml` 

![](../.gitbook/assets/image%20%2829%29.png)

Let's focus on the following lines:

```text
OperationRegion (ECRM, EmbeddedControl, Zero, 0xFF)
Field (ECRM, ByteAcc, NoLock, Preserve) 
{
    //...
}
```

Standing to AML language specification, variables are defined with a **identificator**, a string of 4 chars, and can't start with a number \(e.g. `0ABC` is an invalid name, `ABC0` is a valid name\), and the **size** of the variable expressed with decimal notation. 

Note down `ECRM` \(which we'll call `Field name` and let's identify the variables which size is more than 8 \(usually multiples, such as 16, 32 etc...\). Open a text editor and paste the whole `ECRM` field content and make a list like the depicted below:

![](../.gitbook/assets/image%20%2841%29.png)

Now search inside DSDT how many times the variable is used

![This variable is found only 1 time inside DSDT, so let&apos;s skip it and proceed to the next one](../.gitbook/assets/image%20%2826%29.png)

![There&apos;s more than 1 match so let&apos;s note down the name of the variable, then look for the next one](../.gitbook/assets/image%20%285%29.png)

Iterate this process until you have a filtered list such as the below one:

![](../.gitbook/assets/image%20%2834%29.png)

The above variables are used in DSDT at least one time, and need to be patched.

We'll 

Add the following patch on MaciASL:

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

Then look for each variable you've noted down before and split it into two variables:

e.g.

![](../.gitbook/assets/image%20%2823%29.png)

`BCV4`is a field of 16bit which needs to be splitted into two sub-fields. How to do it?

Simply, rename, without making name conflicts, `BCV4` into `CV40` and `CV41` and try to compile

![](../.gitbook/assets/image%20%2866%29.png)

You'll probably find some errors such as `Object does not exist (BCV4)`. To fix this error replace every single use of the variable with `B1B2(CV40, CV41)` as depicted below

![](../.gitbook/assets/image%20%2873%29.png)

Iterate through this process until you've patched every variable noted down before.

The job isn't finished yet, you still need to find the other variables and patch them as soon as explained. 

Look for your `Field name` obtained from the first step and repeat the patching process.  

Finally save `DSDT.aml`in `ECAP` and reboot. You shouldn't have any problem of battery percentage

