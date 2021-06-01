# *DRAFT* - Still in progress

# Hackintosh on ASRock B550M Steel Legend with AMD RX 6800 XT via [OpenCore][1]

![About this mac][100]

![AMD Power Tool][102]

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

[ BIOS Features][102] and [Peripherals][103]

* *Exit* → Load UEFI BIOS Defaults [**Yes**]
* *Boot* → CSM (Compatibility Support Module) → CSM [**Disabled**]

### XMP

You can enable XMP if your memory supports it.

## Gathering files

- You must download all not bundled kexts and drivers from repositories by yourself.

### ACPI

You need to use `SSDT-CPUR.aml` and `SSDT-EC-USBX-DESKTOP.aml` files from here - [SSDTs: The easy way][21]

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
* [LucyRTL8125Ethernet.kext][6] - Realtek RTL8125 driver for Ethernet 
* [AppleMCEReporterDisabler.kext][8] - disable the AppleMCEReporter kext which will cause kernel panics on AMD CPUs
* [NVMeFix.kext][9] - Used for fixing power management and initialization on non-Apple NVMe.
* [AMDRyzenCPUPowerManagement.kext][10] - Power management and monitoring of AMD processors
* [SMCAMDProcessor.kext][10] - Publish readings to [VirtualSMC][4], which enables macOS applications like iStat to display sensor data.
* [ASRock-B550M-STEEL-LEGEND-USB.kext][91] - Plist-only kext for USB port mapping

### Resources

- [OcBinaryData][27] - For [Setting up OpenCore's GUI][26]

-----



### Config Property list

Please check `Config Example\config.plist` for post-install config example.

----

### USB mapping and Resolving Restart/Shutdown issue

The ASRock B550M Steel Legend contains two USB controllers. One of them  uses one of the ports for the RGB lighting controller. When you restart  or shutdown macOS, it will send a command to this RGB controller, which  will cause a bad CMOS error. To avoid this, you need to create a USB  mapping without this port.

- ASRock B550M Steel Legend contains two USB controllers, which means we can enable all USB ports because the macOS 15 ports limit is per controller. (Ref - [macOS and the 15 Ports Limit][25])
- You can use my kext, which enables all the USB ports - [ASRock-B550M-STEEL-LEGEND-USB.kext][91]
- [More information about ASRock B550M Steel Legend USB Ports, Mapping and Schema][93]

----

#### Dual boot time sync fix

Please follow this guide - [Dual boot time sync fix][92]

## Issues

1. Built in Mic on AMD  - [more information][400]
2. Any Virtual Machines that relying on Apple Hypervisor framework - [more information][401]
   - For example: VMWare, Parallels, Docker and Android Studios
   - VirtualBox is not based on it, so it can be used.



---------------

### Current System Kexts & Drivers Versions

**ACPI**

- [SSDT-CPUR.aml][21] - `Aug 9,2020`
- [SSDT-EC-USBX-DESKTOP.aml][22] - `Feb 3, 2021`

**Kexts**

* [AppleALC.kext][2] - `AppleALC-1.6.0-RELEASE`
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
[6]: https://github.com/Mieze/LucyRTL8125Ethernet
[7]: https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi
[8]: https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip
[9]:https://github.com/acidanthera/NVMeFix/releases
[10]:https://github.com/trulyspinach/SMCAMDProcessor
[10]: https://www.asrock.com/mb/AMD/B550M%20Steel%20Legend/index.asp#BIOS
[11]: https://www.aliexpress.com/item/33034394024.html
[12]: https://developer.apple.com/documentation/macos-release-notes/macos-big-sur-11_4-release-notes
[20]: https://dortania.github.io/OpenCore-Install-Guide/
[21]: https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml	"SSDT-CPUR.aml"
[22]: https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml "SSDT-EC-USBX-DESKTOP.aml"
[23]: https://dortania.github.io/OpenCore-Install-Guide/config.plist/haswell.html#platforminfo
[24]: https://dortania.github.io/OpenCore-Post-Install/universal/security.html#scanpolicy
[25]: https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit
[26]: https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html#setting-up-opencores-gui
[27]: https://github.com/acidanthera/OcBinaryData

[91]: USBKexts/iMacPro1,1
[92]: dual_boot_time_sync_fix.md
[93]: asrock_b550_steel_legend_usb_mapping.md "usb mapping"
[94]: https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md#drm-compatibility-on-macos-11 "WhateverGreen - Fix DRM on BigSur"
[100]: _static/images/about.png "Abount this mac"
[102]: _static/images/amd_power_tool.png "AMD Power Tool"
[105]: _static/images/config_device_properties_rx580.png "Config RX580 device properties"
[400]: https://dortania.github.io/OpenCore-Post-Install/universal/audio.html#no-mic-on-amd "no mic"



[401]: https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html#cpu-support	"AMD CPU Limitations in macOS"