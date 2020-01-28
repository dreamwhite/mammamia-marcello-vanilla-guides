# AppleALC

## Marcello, what is it?

AppleALC is "an open source kernel extension enabling native macOS HD audio for not officially supported codecs without any filesystem modifications".

## What you doin'?

Enabling audio on my hackintosh

## Mammamia Marcello, this is not how to enable audio on hackintosh

What you mean?

## This is how to enable audio on hackintosh

## Requirements

* [AppleALC compiled kext](https://github.com/acidanthera/AppleALC/releases)
* [Lilu compiled kext](https://github.com/acidanthera/Lilu/releases)
* Codec name
  * Motherboard manual
  * [DPCIManager](https://github.com/MuntashirAkon/DPCIManager/releases)
  * [Hackintool](http://headsoft.com.au/download/mac/Hackintool.zip)
* [AppleALC layout id list](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs)
* [AppleALC Resources](https://github.com/acidanthera/AppleALC/tree/master/Resources)
* [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/)
* [gfxutil](https://github.com/acidanthera/gfxutil/releases)
* MAMMAMIA

{% hint style="danger" %}
Please note that AppleALC automatically renames HDAS/HDAU/BOD3 to HDEF  
Don't add those renames in Clover
{% endhint %}

### Step 1: identify codec name

* DPCIManager
* Hackintool

![Status/Devices section](../.gitbook/assets/image%20%28102%29.png)

{% hint style="warning" %}
Please note that Hackintool suggest you some Layout IDs. In any case check the official AppleALC page for all layout ids
{% endhint %}

![Audio section](../.gitbook/assets/image%20%2846%29.png)

### Step: identify layout ID

![e.g. ALC295 layout ids](../.gitbook/assets/image%20%2841%29.png)

Note the "layoutXX.xml" and proceed to the Installation step

### Step 3: installation

1. Verify that your Codec name is present in AppleALC layout id list
2. Put the compiled kexts inside /Volumes/EFI/EFI/CLOVER/kexts/Other folder

![Step 2: kext installation](../.gitbook/assets/image%20%2888%29.png)

### Step 4: get DevicePath address using gfxutil

![...and drag the executable &quot;gfxutil&quot; inside the terminal window and type &quot;-f HDEF&quot; ](../.gitbook/assets/image%20%2887%29.png)

Copy the DevicePath, "PciRoot\(0x0\)/Pci\(0x1f,0x3\)" and proceed to the next step

### Step 5: Clover Configurator

1. Mount your ESP 
2. Open your config.plist
3. Go in Devices/Properties section
4. Fill the fields as below

![Mount EFI and open config.plist](../.gitbook/assets/image%20%2838%29.png)

![Set Audio/Inject to No and change 77 with your layout-id](../.gitbook/assets/image%20%2844%29.png)



#### Code editor

Add the following code inside config.plist/Devices/Properties section

```text
<key>PciRoot(0x0)/Pci(0x1f,0x3)</key>
			<dict>
				<key>layout-id</key>
				<integer>77</integer>
			</dict>
```

### Step 6: check if audio is working

{% hint style="warning" %}
Make sure that _Internal Speakers_ is in Output devices
{% endhint %}

{% hint style="info" %}
Test only speakers. For the 3.5mm combojack follow the next section
{% endhint %}

![System Preferences, Sound and check Output section](../.gitbook/assets/image%20%2890%29.png)

## Credits

* [https://github.com/acidanthera/AppleALC](https://github.com/acidanthera/AppleALC/)
  * check their credits page









