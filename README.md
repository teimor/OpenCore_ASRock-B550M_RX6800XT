# Hackintosh on ASRock B550M Steel Legend with AMD RX 6800 XT via [OpenCore][1]

![About this mac][100]

![AMD Power Tool][104]

### **Hardware configuration**

* AMD Zen 3 Ryzen 7 5800x
* ASRock B550M Steel Legend
* G.Skill Trident Z Neo RGB 2x32GB DDR4 3800Mhz CL18 (F4-3800C18D-64GTZN)
* ASRock AMD Radeon RX 6800 XT Taichi X 16G OC
* Intel 660p 1TB m.2 (macOS)
* Adata XPG SX8200 Pro 1TB m.2 (Windows10)
* [Fenvi FV-HB1200][11] WiFi BT PCIe Card (BCM94360CS2 Based)
* Dell P2418D QHD (Monitor) 

### **Before you start make sure you have**

* Working hardware
* [BIOS][10] version `>= 2.00`
* Read [OpenCore Desktop Guide][20]
* [OpenCore][1] `= 0.6.9`

## Installation

### BIOS Settings

[ BIOS Features][102] and [Peripherals][103]

* *Save & Exit* → Load Optimized Defaults [**Yes**]
* *BIOS Features* → Fast boot [**Disabled**]
* *BIOS Features* → VT-d [**Enabled**]
* *BIOS Features* → Windows 8 Features [**Windows 8 WHQL**]
* *BIOS Features* → CSM Support [**Disabled**]
* *Peripherals* → Initial Display Output [**IGFX**]
* *Peripherals* → XHCI Mode [**Enabled**]
* *Peripherals* → Intel Processor Graphics Memory Allocation [**64M**]
* *Peripherals* → XHCI Hand-off [**Enabled**]
* *Peripherals* → EHCI Hand-off [**Enabled**]
* *Peripherals* → Super IO Configuration → Serial Port A [**Disabled**]
  * Must be disable it in order that macOS Sleep function will work properly.

### What's behind the scenes

* `CFG-Lock / MSR 0xE2` option is [**UNLOCKED**][104].

## Gathering files

- You must download all not bundled kexts and drivers from repositories by yourself.

### ACPI

You can use `SSDT-EC.aml` and `SSDT-PLUG.aml` files, but it probably better to create your own - [SSDTs: The easy way][21]

### EFI drivers

* [HfsPlus.efi][7] - Needed for seeing HFS volumes(ie. macOS Installers and Recovery partitions/images).
* OpenRuntime.efi - Must have to work with native NVRAM
* OpenCanopy.efi - For [OpenCore's GUI][25]

### Kexts

* [VirtualSMC.kext][4] - A advanced replacement of FakeSMC, almost like native mac SMC.
  * SMCProcessor.kext - Used for monitoring CPU temperature.
  * SMCSuperIO.kext - Used for monitoring fan speed.
* [Lilu.kext][3] - Dependency of `VirtualSMC.kext` and `WhateverGreen.kext`
* [WhateverGreen.kext][5] - Need for iGPU support
* [AppleALC.kext][2] - Getting audio to work as easy-peasy.
* [IntelMausi.kext][6] - Intel driver for Ethernet 
* USBH97-D3H-CF.kext - Plist-only kext for USB port mapping

### Resources

- [OcBinaryData][26] - For [Setting up OpenCore's GUI][25]

-----



### Config Property list

Please check `Config Example\config.plist` for post-install config example.

#### Pre-Install

**Set iGPU/dGPU config**

- If you are using dGPU (for example: AMD RX580), under `DeviceProperties` → `PciRoot(0x0)/Pci(0x2,0x0)` :
  - `AAPL,ig-platform-id` = `04001204` [Data] - iGPU is only used for compute tasks
  - `device-id` = `12040000` [Data] - Fake in case you have an HD 4400 
- If you are using just the iGPU, set `DeviceProperties` → `PciRoot(0x0)/Pci(0x2,0x0)` :
  - `AAPL,ig-platform-id` = `0300220D` [Data] - iGPU is used to drive a display
  - `device-id` = `12040000` [Data] - Fake in case you have an HD 4400 
- Fix DRM for RX580, under `DeviceProperties` add `PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)` dictionary with:
  *Not needed for Big Sur*
  - `shikigva` = `80` [Number]

**Set audio with AppleALC**

- Set AppleALC, under `DeviceProperties` add `PciRoot(0x0)/Pci(0x1B,0x0)` dictionary with:
  - `layout-id` = `63000000` [Data]

**Platfom information & USB**

- Populated `PlatformInfo > Generic` section in `config.plist`, can be easily done with `GenSMBIOS` please follow [OpenCore Desktop Guide][22].
- Add the `USBH97-D3H-CF.kext` depends on the model you use `iMac14,1 / iMac14,2 / iMac15,1 / iMacPro1,1 ` from `USB Kexts`. (Also add it to your config, you can see an example on `Config Example`)
- Big Sur if you are using any supported AMD dGPU my recommendation is using `iMacPro1,1` SMBIOS.

#### Post-Install

- `Misc -> Boot`
  - Set `PickerMode` as `External` and add files from [Setting up OpenCore's GUI][25]
- `Misc -> Security`
  - Set `ScanPolicy` to `983299` - for more information [Scanpolicy Docs][23]
- `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> boot-args`:
  - Remove `-v` from your config.plist
- `NVRAM -> Add -> D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14 -> UIScale`:
  - One-byte data defining boot.efi user interface scaling. Should be `01` for normal screens and `02` for HiDPI screens. (When using Dell P2418D set it to `02`)

**DRM Compatibility on macOS Big Sur**

[*Source: WhateverGreen documentation*][93]

- `defaults write com.apple.AppleGVA gvaForceAMDKE -boolean yes` forces AMD DRM decoder for streaming services (like Apple TV and iTunes movie streaming)
- `defaults write com.apple.AppleGVA gvaForceAMDAVCDecode -boolean yes` forces AMD AVC accelerated decoder
- `defaults write com.apple.AppleGVA gvaForceAMDAVCEncode -boolean yes` forces AMD AVC accelerated encoder
- `defaults write com.apple.AppleGVA gvaForceAMDHEVCDecode -boolean yes` forces AMD HEVC accelerated decoder
- `defaults write com.apple.AppleGVA disableGVAEncryption -string YES` forces AMD HEVC accelerated decoder
- `defaults write com.apple.coremedia hardwareVideoDecoder -string force` forces hardware accelerated video decoder (for any resolution)

----

### USB mapping

- First read - [macOS and the 15 Port Limit][24]

Due to these limits disabled interfaces are `HS05, HS06, HS07, HS08 and HS13`. Those are the two usb2 ports near the PS/2 ports & two internal USB 2.0 headers. In addition, interface `HS14` used by `Fenvi FV-HB1200` for bluetooth and configured as internal. I'm using all 15 available ports due the limit but If you want to use other ports use this mapping table and [schema][101] to edit the `Info.plist` file inside the `USBH97-D3H-CF.kext`.

| Name | UsbConnector | port     |
| ---- | ------------ | -------- |
| HS01 | 0            | 01000000 |
| HS02 | 0            | 02000000 |
| HS03 | 0            | 03000000 |
| HS04 | 0            | 04000000 |
| HS05 | 0            | 05000000 |
| HS06 | 0            | 06000000 |
| HS07 | 0            | 07000000 |
| HS08 | 0            | 08000000 |
| HS09 | 0            | 09000000 |
| HS10 | 0            | 0A000000 |
| HS11 | 0            | 0B000000 |
| HS12 | 0            | 0C000000 |
| HS13 | 0            | 0D000000 |
| HS14 | 0            | 0E000000 |
| SS01 | 3            | 10000000 |
| SS02 | 3            | 11000000 |
| SS03 | 3            | 12000000 |
| SS04 | 3            | 13000000 |
| SS05 | 3            | 14000000 |
| SS06 | 3            | 15000000 |

*For Internal USB ports like Bluetooth - use UsbConnector = `255`*

----

### Enable HiDPI for Dell P2418D

![dell_p2418d_hidpi][106]

Use [one-key-hidpi][91] to Enable HiDPI on Dell monitor, and have a "Native" Scaled in System Preferences.

1. Turn **off** System Integrity Protection(SIP):
   1. Restart & enter into **Recovery**.
   2. Choose Utilities > Terminal.
   3. Run `csrutil disable`
   4. Restart & enter into **macOS**.
2. Run [one-key-hidpi][91] script and set resolution config to `2560x1440 Display`
3. Reboot and check that everthing is working currectly.
4. Turn **on** System Integrity Protection(SIP):
   1. Restart & enter into **Recovery**.
   2. Choose Utilities > Terminal.
   3. Run `csrutil enable`
   4. Restart & enter into **macOS**.
5. After reboot check everthing is working currectly and check SIP enabled by running `csrutil status` in Terminal.

#### Dual boot time sync fix

Please follow this guide - [Dual boot time sync fix][92]

## Issues

1. None :)



Thanks to [Andrii Korzh][90] for his repsotory, knowledge sharing and permission.

---------------

### Current System Kexts & Drivers Versions

**Kexts**

* [AppleALC.kext][2] - `AppleALC-1.6.0-RELEASE`
* [IntelMausi.kext][6] - `IntelMausi-1.0.6-RELEASE`
* [Lilu.kext][3] - `Lilu-1.5.3-RELEASE`
* [VirtualSMC.kext][4] - `VirtualSMC-1.2.3-RELEASE`
* [WhateverGreen.kext][5] - `WhateverGreen-1.4.9-RELEASE`

**Drivers**

* [HfsPlus.efi][7] - `Feb 29, 2020`

[1]: https://github.com/acidanthera/OpenCorePkg/releases
[2]: https://github.com/acidanthera/AppleALC/releases
[3]: https://github.com/acidanthera/Lilu/releases
[4]: https://github.com/acidanthera/VirtualSMC/releases
[5]: https://github.com/acidanthera/WhateverGreen/releases
[6]: https://github.com/acidanthera/IntelMausi/releases
[7]: https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi

[10]: https://www.asrock.com/mb/AMD/B550M%20Steel%20Legend/index.asp#BIOS
[11]: https://www.aliexpress.com/item/33034394024.html

[20]: https://dortania.github.io/OpenCore-Install-Guide/
[21]: https://dortania.github.io/Getting-Started-With-ACPI/ssdt-methods/ssdt-easy.html
[22]: https://dortania.github.io/OpenCore-Install-Guide/config.plist/haswell.html#platforminfo
[23]: https://dortania.github.io/OpenCore-Post-Install/universal/security.html#scanpolicy
[24]: https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit
[25]: https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html#setting-up-opencores-gui
[26]: https://github.com/acidanthera/OcBinaryData

[90]: https://github.com/korzhyk
[91]: https://github.com/xzhih/one-key-hidpi
[92]: dual_boot_time_sync_fix.md
[93]: https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md#drm-compatibility-on-macos-11 "WhateverGreen - Fix DRM on BigSur"
[100]: _static/images/about.png "Abount this mac"
[101]: _static/images/usb_mapping.png "USB Mapping"
[102]: _static/images/bios_features.png "BIOS Features"
[103]: _static/images/bios_peripherals.png "BIOS Peripherals"
[104]: _static/images/amd_power_tool.png "AMD Power Tool"
[105]: _static/images/config_device_properties_rx580.png "Config RX580 device properties"
[106]: _static/images/dell_p2418d_hidpi.png "Dell P2418D HIDPI enabled"