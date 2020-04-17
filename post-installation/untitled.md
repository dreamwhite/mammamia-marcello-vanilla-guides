# Fix missing battery percentage

Look for `EmbeddedControl` inside `DSDT.aml` 

![](../.gitbook/assets/image%20%2828%29.png)

Let's focus on the following lines:

```text
OperationRegion (ECRM, EmbeddedControl, Zero, 0xFF)
Field (ECRM, ByteAcc, NoLock, Preserve) 
{
    //...
}
```

Standing to AML language specification, variables are defined with a **identificator**, a string of 4 chars, and can't start with a number \(e.g. `0ABC` is an invalid name, `ABC0` is a valid name\), and the **size** of the variable expressed with decimal notation. 

Note down `ECRM` and let's identify the variables which size is more than 8 \(usually multiples, such as 16, 32 etc...\). Open a text editor and paste the whole `ECRM` field content and make a list like the depicted below:

![](../.gitbook/assets/image%20%2840%29.png)

Now search inside DSDT how many times the variable is used

![This variable is found only 1 time inside DSDT, so let&apos;s skip it and proceed to the next one](../.gitbook/assets/image%20%2825%29.png)

![There&apos;s more than 1 match so let&apos;s note down the name of the variable, then look for the next one](../.gitbook/assets/image%20%285%29.png)

Iterate this process until you have a filtered list such as the below one:

![](../.gitbook/assets/image%20%2833%29.png)

The above variables are used in DSDT at least one time, and need to be patched.

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









