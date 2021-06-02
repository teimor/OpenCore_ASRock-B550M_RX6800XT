# Hackintosh on ASRock B550M Steel Legend with AMD RX 6800 XT via [OpenCore][1]

![About this mac][200]

![AMD Power Tool][201]

### **Hardware configuration**

* AMD Zen 3 Ryzen 7 5800x
* ASRock B550M Steel Legend
  * Audio: Realtek ALC1200
  * LAN: Dragon RTL8125BG
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
* macOS Big Sur 11.4+ [(Support for AMD Navi RDNA2 architecture added on 11.4)][12]

## Installation

### BIOS Settings

* *Exit* → Load UEFI BIOS Defaults [**Yes**]
* *Boot* → CSM (Compatibility Support Module) → CSM [**Disabled**]
* *Advanced* → PCI Configuration → Above 4G Decoding [**Enabled**]
* *Advanced* → PCI Configuration → Re-Size BAR Support [**Disabled**]
* *Advanced* → Storage Configuration → SATA Mode [**AHCI**]
* *Security* → Secure Boot → Secure Boot [**Disabled**]
* *Boot* → Fast Boot [**Disabled**]

### XMP

You can enable XMP if your memory supports it.

## Gathering files

- You must download all not bundled kexts and drivers from repositories by yourself.

### ACPI

- [SSDT-CPUR.aml][21]
- [SSDT-EC-USBX-DESKTOP.aml][22]

### EFI drivers

* [HfsPlus.efi][7] - Needed for seeing HFS volumes(ie. macOS Installers and Recovery partitions/images).
* OpenRuntime.efi - Must have to work with native NVRAM
* OpenCanopy.efi - For [OpenCore's GUI][25]

### Kexts

* [VirtualSMC.kext][4] - A advanced replacement of FakeSMC, almost like native mac SMC.
* [Lilu.kext][3] - Dependency of `VirtualSMC.kext` and `WhateverGreen.kext`
* [WhateverGreen.kext][5] - Need for GPU support
* [AppleALC.kext][2] - Getting audio to work as easy-peasy.
* [LucyRTL8125Ethernet.kext][6] - Realtek RTL8125 driver for Ethernet 
* [AppleMCEReporterDisabler.kext][8] - disable the AppleMCEReporter kext which will cause kernel panics on AMD CPUs
* [NVMeFix.kext][9] - Used for fixing power management and initialization on non-Apple NVMe.
* [AMDRyzenCPUPowerManagement.kext][10] - Power management and monitoring of AMD processors
* [SMCAMDProcessor.kext][10] - Publish readings to [VirtualSMC][4], which enables macOS applications like iStat to display sensor data.
* [ASRock-B550M-STEEL-LEGEND-USB.kext][100] - Plist-only kext for USB port mapping

### Kernel patches

- [Ryzen/Threadripper(17h and 19h)][13] - This is where the AMD kernel patching magic happens.

### Resources

- [OcBinaryData][27] - For [Setting up OpenCore's GUI][26]

-----



### Config Property list

Please check `Config Example\config.plist` for post-install config example.

#### Pre-Install

**Set AMD RX 6800 XT config**

- Add `boot-args` :
  - Under `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> boot-args`
  - Add `agdpmod=pikera` for fixing black screen at boot for Navi 20 GPUs. ([More information about Navi20][103])

**Set audio with AppleALC**

- Set AppleALC, under `DeviceProperties` add `PciRoot(0x0)/Pci(0x8,0x1)/Pci(0x0,0x4)` dictionary with:
  - `layout-id` = `1` [Integer/Number]

**Platfom information**

- Populated `PlatformInfo > Generic` section in `config.plist`, can be easily done with `GenSMBIOS` please follow [OpenCore Desktop Guide][23].
- For Navi20 dGPU will work properly, We must use `iMacPro1,1` SMBIOS.

#### Post-Install

- `Misc -> Boot`
  - Set `PickerMode` as `External` and add files from [Setting up OpenCore's GUI][26]
- `Misc -> Security`
  - Set `ScanPolicy` to `0` - for dual boot, [Scanpolicy Docs][24]
- `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> boot-args`:
  - Remove `-v` from your config.plist
- `NVRAM -> Add -> D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14 -> UIScale`:
  - One-byte data defining boot.efi user interface scaling. Should be `01` for normal screens and `02` for HiDPI screens. (When using Dell P2418D set it to `02`)

*Optional - Minimal theme for picker - [opencanopy-minimal-theme by tekteq][14]*

**Set hard-drives as internal**

For some reason the NVMe drives are set as external. In order to fix this issue, We will to get the hard-drives id and set them as internal drives.

- Download and open [Hackintool][28]. Go to PCIe Tab

![Hackintool Hard Drives][202]

- In this tab, search in **Class** column for **Mass storage controller** devices, right click on the row and **Copy Device Path**
- Under `DeviceProperties` add will add this id as dictionary with:
  - `built-in` = `01000000` [Data]

![Config Hard-drives][203]



**DRM Compatibility on macOS 11**

[*Source: WhateverGreen documentation*][104]

- Find the id of dGPU by using `gfxutil`:

  ```bash
  path/to/gfxutil -f GFX0
  ```

  For example:

  ```bash
  ./gfxutil -f GFX0
  09:00.0 1002:73bf /PCI0@0/GPP8@3,1/SWUS@0/SWDS@0/GFX0@0 = PciRoot(0x0)/Pci(0x3,0x1)/Pci(0x0,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)
  ```

  My dGPU id is `PciRoot(0x0)/Pci(0x3,0x1)/Pci(0x0,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)`

- Under `DeviceProperties` add this id as dictionary with:

  - `unfairgva` = `01000000` [Data]

  

  ![dGPU DRM][204]

In addition, I used those overrides:

- `defaults write com.apple.AppleGVA gvaForceAMDKE -boolean yes` forces AMD DRM decoder for streaming services (like Apple TV and iTunes movie streaming)
- `defaults write com.apple.AppleGVA gvaForceAMDAVCDecode -boolean yes` forces AMD AVC accelerated decoder
- `defaults write com.apple.AppleGVA gvaForceAMDAVCEncode -boolean yes` forces AMD AVC accelerated encoder
- `defaults write com.apple.AppleGVA gvaForceAMDHEVCDecode -boolean yes` forces AMD HEVC accelerated decoder
- `defaults write com.apple.AppleGVA disableGVAEncryption -string YES` forces AMD HEVC accelerated decoder
- `defaults write com.apple.coremedia hardwareVideoDecoder -string force` forces hardware accelerated video decoder (for any resolution)

----

### USB mapping and Resolving Restart/Shutdown issue

The ASRock B550M Steel Legend contains two USB controllers. One of them  uses one of the ports for the RGB lighting controller. When you restart  or shutdown macOS, it will send a command to this RGB controller, which  will cause a bad CMOS error. To avoid this, you need to create a USB  mapping without this port.

- ASRock B550M Steel Legend contains two USB controllers, which means we can enable all USB ports because the macOS 15 ports limit is per controller. (Ref - [macOS and the 15 Ports Limit][25])
- You can use my kext, which enables all the USB ports - [ASRock-B550M-STEEL-LEGEND-USB.kext][100]
- [More information about ASRock B550M Steel Legend USB Ports, Mapping and Schema][102]

----

#### Dual boot time sync fix

Please follow this guide - [Dual boot time sync fix][101]

## Issues

1. Built in Mic on AMD  - [more information][400]
2. Any Virtual Machines that relying on Apple Hypervisor framework - [more information][401]
   - For example: VMWare, Parallels, Docker and Android Studios
   - VirtualBox is not based on it, so it can be used.



---------------

### Current System Kernal patches, ACPI, Kexts & Drivers Versions

**Kernel patches**

- [Ryzen/Threadripper(17h and 19h)][13]  - `Feb 4, 2021`

**ACPI**

- [SSDT-CPUR.aml][21] - `Aug 9,2020`
- [SSDT-EC-USBX-DESKTOP.aml][22] - `Feb 3, 2021`

**Kexts**

* [AMDRyzenCPUPowerManagement.kext][10] - 0.6.6``
* [SMCAMDProcessor.kext][10] - `0.6.4`
* [AppleALC.kext][2] - `AppleALC-1.6.0-RELEASE`
* [AppleMCEReporterDisabler.kext][8]
* [Lilu.kext][3] - `Lilu-1.5.3-RELEASE`
* [LucyRTL8125Ethernet.kext][6] - `LucyRTL8125Ethernet-V1.1.0`
* [NVMeFix.kext][9] - `NVMeFix-1.0.7-RELEASE`
* [VirtualSMC.kext][4] - `VirtualSMC-1.2.3-RELEASE`
* [WhateverGreen.kext][5] - `WhateverGreen-1.4.9-RELEASE`

**Drivers**

* [HfsPlus.efi][7] - `Feb 29, 2020`

[1]: https://github.com/acidanthera/OpenCorePkg/releases
[2]: https://github.com/acidanthera/AppleALC/releases
[3]: https://github.com/acidanthera/Lilu/releases
[4]: https://github.com/acidanthera/VirtualSMC/releases
[5]: https://github.com/acidanthera/WhateverGreen/releases
[6]: https://github.com/Mieze/LucyRTL8125Ethernet
[7]: https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi
[8]: https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip
[9]:https://github.com/acidanthera/NVMeFix/releases
[10]:https://github.com/trulyspinach/SMCAMDProcessor
[10]: https://www.asrock.com/mb/AMD/B550M%20Steel%20Legend/index.asp#BIOS
[11]: https://www.aliexpress.com/item/33034394024.html
[12]: https://developer.apple.com/documentation/macos-release-notes/macos-big-sur-11_4-release-notes
[13]: https://github.com/AMD-OSX/AMD_Vanilla/tree/opencore/17h_19h
[14]: https://github.com/tekteq/opencanopy-minimal-theme



[20]: https://dortania.github.io/OpenCore-Install-Guide/
[21]: https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml	"SSDT-CPUR.aml"
[22]: https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml "SSDT-EC-USBX-DESKTOP.aml"
[23]: https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html#platforminfo
[24]: https://dortania.github.io/OpenCore-Post-Install/universal/security/scanpolicy.html
[25]: https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit
[26]: https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html#setting-up-opencores-gui
[27]: https://github.com/acidanthera/OcBinaryData
[28]: https://github.com/headkaze/Hackintool/releases/tag/3.5.7



[100]: https://github.com/teimor/OpenCore_ASRock-B550M_RX6800XT/tree/main/USB%20Kexts/iMacPro1%2C1
[101]: dual_boot_time_sync_fix.md
[102]: asrock_b550_steel_legend_usb_mapping.md "usb mapping"
[103]: https://elitemacx86.com/threads/how-to-enable-amd-rx-6800-rx-6800xt-and-rx-6900xt-on-macos-big-sur.709/	"Enable Navi 20 on macOS Big Sur"
[104]: https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md#drm-compatibility-on-macos-11 "DRM Compatibility on macOS 11"



[200]: _static/images/about.png "Abount this mac"
[201]: _static/images/amd_power_tool.png "AMD Power Tool"
[202]: _static/images/hard_drives_hackintool.png "Hackintool Hard-drives"
[203]: _static/images/hard_drives_config.png	"config Hard-drives"
[204]: _static/images/dgpu_drm_config.png	"dGPU DRM"



[400]: https://dortania.github.io/OpenCore-Post-Install/universal/audio.html#no-mic-on-amd "no mic"

[401]: https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html#cpu-support	"AMD CPU Limitations in macOS"
