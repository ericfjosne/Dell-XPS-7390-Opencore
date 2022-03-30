Dell XPS 13 7390
---

Machine specifications:
---
- **CPU** : [Intel® Core™ i7-10510U Processor](https://ark.intel.com/content/www/us/en/ark/products/196449/intel-core-i710510u-processor-8m-cache-up-to-4-90-ghz.html)
  - processor ID: 806ED (found in the BIOS)
  - 4 cores
  - 8 threads
  - clock speed: 
    - minimum: 400 MHz (found in the BIOS)
    - base : 1.80 GHz 
    - turbo-boost: 4.90 GHz
- **RAM** : 16 GB Dual-channel LPDDR3 at 2133 MHz
- **GPU** : 
  - Intel UHD 620
  - Video memory: 64Mb
  - Video BIOS version: 9.0.1092
- **Storage** : 
  - Interface: PCIe Gen3 x4 NVMe, up to 32 Gbps
  - SSD: Samsung SSD 970 EVO Plus 2Tb
- **Display** : 
  - Panel type: Ultra High Definition (UHD) 13.3" screen
  - Native resolution: 3840x2160 
  - Touch options: yes
- **WiFi** : Rivet Killer AX1650W (Intel AX200 chip, Soldered)
- **Soundcard** : Realtek ALC3271-CG with Waves MaxxAudio ProRealtek
- **Biometric devices** : Goodix fingerprint
- **Battery** : DELL H754V98 Li-ion 52.0 Wh

Configuration:
---
- **BIOS version** : `1.13.0`
- **XG6 NVMe device firmware version** : `10604107`

Gathering files
---

This guide applies to Opencore version `0.7.9`

Base files:
----

- [OpenCorePKG](https://github.com/acidanthera/OpenCorePkg/releases/), retaining only the following:
  - EFI/BOOT/BOOTx64.efi
  - EFI/OC/OpenCore.efi
  - EFI/OC/ACPI/* (empty folder)
  - EFI/OC/Kexts/* (empty folder)
  - EFI/OC/Drivers/OpenRuntime.efi
  - EFI/OC/Drivers/OpenCanopy.efi
  - EFI/OC/Tools/OpenShell.efi
  - EFI/OC/Resources/* (keep all of it)

Your EFI folder should look like this:

<img src="https://i.imgur.com/aQ3Uu3i.png"/>

Drivers:
----
- [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi): Needed for seeing HFS volumes(ie. macOS Installers and Recovery partitions/images).

Kexts:
----

- [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases): Emulates the SMC chip found on real macs, without this macOS will not boot
  - VirtualSMC.kext (obviously)
  - SMCBatteryManager.kext: Used for measuring battery readouts on laptops
  - SMCDellSensors.kext: Allows for finer monitoring and control of the fans on Dell machines supporting System Management Mode(SMM)
  - SMCProcessor.kext: Used for monitoring CPU temperature
- [Lilu](https://github.com/acidanthera/Lilu/releases): A kext to patch many processes, required for AppleALC, WhateverGreen, VirtualSMC and many other kexts. Without Lilu, they will not work.
- [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases): Used for graphics patching, DRM fixes, board ID checks, framebuffer fixes, etc; all GPUs benefit from this kext.

Sound:
- [AppleALC](https://github.com/acidanthera/AppleALC/releases): Used for AppleHDA patching, allowing support for the majority of on-board sound controllers. AppleALCU.kext is a pared down version of AppleALC that only supports digital audio - but you can still use AppleALC.kext on digital audio-only systems

Wifi:
- [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases): Adds support for a large variety of Intel wireless cards and works natively in recovery thanks to IO80211Family integration. Get the kext file matching your macOS distribution.

Bluetooth:
- [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases): Adds Bluetooth support to macOS when paired with an Intel wireless card. Get only the `IntelBluetoothFirmware.kext` file.
- [BlueToolFixup.kext](https://github.com/acidanthera/BrcmPatchRAM/releases): Specific need for Bluetooth to work on Monterey

Storage controller:
- [NVMeFix](https://github.com/acidanthera/NVMeFix/releases): Used for fixing power management and initialization on non-Apple NVMe

PS2 Keyboards/Trackpads:
- [VoodooPS2](https://github.com/acidanthera/VoodooPS2/releases): Works with various PS2 keyboards, mice, and trackpads
- [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C/releases): Attaches to I2C controllers to allow plugins to talk to I2C trackpads. To be paired with the plugin VoodooI2CHID.

USB ports mapping:
- [USBToolBox](https://github.com/USBToolBox/tool): Instead of mapping everything manually, let's use something that does it for you. Used the Windows tool to discover all available ports of the machine and generate the `UTBMap.kext` for it. Both the [latest `USBToolBox.kext`](https://github.com/USBToolBox/kext/releases) and this generated `UTBMap.kext` are necessary.

SSDTs:
----
Needed for Comet Lake CPU:

- CPU: [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html). Link to prebuilt: [SSDT-PLUG-DRTNIA.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PLUG-DRTNIA.aml).
- Embedded controllers: [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html). Link to laptop prebuilt: [SSDT-EC-USBX-LAPTOP.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-LAPTOP.aml)
- Backlight: [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html). Link to prebuilt: [SSDT-PNLF.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF.aml)
- I2C Trackpad: [SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html). This is a manual step, so it will be done post-install. You will need a mouse to install macOS. For now, we will use [SSDT-XOSI.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-XOSI.aml)
- AWAC system clock: [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html). Link to prebuilt: [SSDT-AWAC.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-AWAC.aml)


At this stage, your EFI folder should be looking like this:

<img src="https://i.imgur.com/BpuXThZ.png"/>

Let's get started with the configuration file.

Config.plist
---
For this machine, we need to follow the [Coffee Lake laptop guide](coffee-lake-plus). Here are the adjusted parts which ended up being custom, or not straightforward:

- ACPI
  - Patch: Because we are currently using SSDT-XOSI, we need to add the [following patching](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake-plus.html#acpi)
- DeviceProperties
  - Add: The GPU is supposed to be UHD 620, meaning that the AAPL,ig-platform-id _should_ be `00009B3E` according to the install guide. However, the [WhateverGreen FAQ](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#intel-uhd-graphics-610-655-coffee-lake-and-comet-lake-processors) recommends using the value `0900A53E` as AAPL,ig-platform-id (framebuffer = `0x3EA50009`). I went with WhateverGreen recommendation, but forced device-id with data `9B3E0000`.
- Kernel
  - Emulate: since this is a comet lake CPU, I checked the hardware ID according to Windows. It is identified as `ACPI\GenuineIntel_-_Intel64_Family_6_Model_142`. On Linux, it returns 142. No need for spoofing any value.
- NVRAM
  - Add
    - I added UIScale as Data = 02 under `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14` as the screen of this machine is a HiDPI one.
- PlatformInfo
  - I decided to go with a MacBookPro16,3. It matches the CPU type of the [i7-10510U](https://ark.intel.com/content/www/us/en/ark/products/196449/intel-core-i710510u-processor-8m-cache-up-to-4-90-ghz.html): quad core 15W, 13" display size. Only the iGPU does not match, but what we changed in the DeviceProperties should be enough. You will need to adjust all other parameters for this to work.


Post-install fixes:
---

Fixing Sound:
---

According to the soundcard name, our codec is ALC3271. But this is a rebranded one. The true lies elsewhere. Under Windows, in the Device Manager, when opening up the properties > events linked to the sound card, it shows:

```
Device INTELAUDIO\FUNC_01&VEN_10EC&DEV_0299&SUBSYS_10280962&REV_1000\5&18f4f3cb&0&0001 was configured.
```

This means that this actually requires codec ALC299, which requires layout 21 or 22 according to the [AppleALC Supported codecs page](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs). The right value for this machine is `22`. 

After checking with gfxutil, our sound devide is identified as follow:

```
efj@xps Downloads % ./gfxutil -f HDEF
00:1f.3 8086:02c8 /PCI0@0/HDEF@1F,3 = PciRoot(0x0)/Pci(0x1F,0x3)
```

So, we reflect all this in our `config.plist` by adding:

```
DeviceProperties > Add > PciRoot(0x0)/Pci(0x1F,0x3) > layout-id = 22 (Number)
```

But it still doesn't work ... so, as advised in the install guide, we need to create [SSDT-HPET fixes](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) for our system.

Adding the aml file to acpi
Replacing the plist content in the acpi patches


Fixing Power Management:
---

When validating whether X86PlatformPlugin was enabled using [IORegistryExplorer](https://github.com/khronokernel/IORegistryClone/blob/master/ioreg-302.zip), all seemed ok. 

Went on to use [CPUFriend](https://github.com/acidanthera/CPUFriend/releases) and [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend).

When running CPUFriendFriend, the following values were selected:
- 04 for LFM (value found in BIOS)
- 90 for balanced power saving (default)
- 05 for Modern MacBook Pro (default)
- y to enable additional power saving features (custom)

Fixing CFG_LOCK:
---
Tried to follow the guide for Dell machines, [available here](https://github.com/dreamwhite/bios-extraction-guide/tree/master/Dell), and ended up finding the following offset for the CFG Lock flag (in BIOS version `1.13.0`):
```
0x71027     One Of: CFG Lock, VarStoreInfo (VarOffset/VarName): 0x3E, VarStore: 0x3, QuestionId: 0x165, Size: 1, Min: 0x0, Max 0x1, Step: 0x0 {05 91 62 03 63 03 65 01 03 00 3E 00 10 10 00 01 00}
```

However, when getting the current value at that offset via `setup_var  0x3E`, it is already `0x00`.
According to this value, it should already be unlocked, which is apparently not the case when validating this using `ControlMsrE2.efi`.

Since the system boots and is working, I decided to leave it at this and move on.


External useful links
---
- Official Dell product page: [XPS 13 7390 Setup and Specifications](https://www.dell.com/support/manuals/en-us/xps-13-7390-laptop/xps-7390-setup-and-specifications/specifications-of-xps-13-7390?guid=guid-7c9f07ce-626e-44ca-be3a-a1fb036413f9&lang=en-us)
- Sambow23 similar project: https://github.com/sambow23/Dell-XPS-13-7390-macOS
- Intel ARK processor page: [Intel® Core™ i7-10510U Processor](https://ark.intel.com/content/www/us/en/ark/products/196449/intel-core-i710510u-processor-8m-cache-up-to-4-90-ghz.html)
- Wikipedia Intel Comet Lake page: https://en.wikipedia.org/wiki/Comet_Lake_(microprocessor)
- Check Apple coverage based on serial number: https://checkcoverage.apple.com/

License
-----

Work in this repo, including previous commits (SSDTs, config files, etc) is distributed under a [Creative Commons Attribution-NonCommercial-ShareAlike](https://creativecommons.org/licenses/by-nc-sa/3.0/) (CC BY-NC-SA) 3.0 License. All others' work (clover, kexts, drivers) retains the original license it was distributed with.

