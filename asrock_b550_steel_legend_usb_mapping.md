# ASRock B550M Steel Legend USB Ports, Mapping and Schema



## Schema

![usb_mapping][1]



## Information

**USB Controllers**

- `XHC0`
  - `IOService:/AppleACPIPlatformExpert/PCI0@0/AppleACPIPCI/GP13@8,1/IOPP/XHC0@0,3/XHC0@60000000`
- `PTXH`
  - `IOService:/AppleACPIPlatformExpert/PCI0@0/AppleACPIPCI/GPP1@1,2/IOPP/PTXH@0/PTXH@00000000`

**Map table**

| Controller | Name | Port     | UsbConnector | Personality | Location                          | Notes                                                   |
| :--------- | ---- | -------- | ------------ | ----------- | --------------------------------- | ------------------------------------------------------- |
| PTXH       | POT1 | 01000000 | 3            | USB 3.0     | Rear USB Type C                   |                                                         |
| PTXH       | POT2 | 02000000 | 3            | USB 3.0     | Rear USB A, 3.0, Near the USB-C   |                                                         |
| PTXH       | POT3 | 03000000 | 3            | USB 3.0     | USB3 Bottom Header                |                                                         |
| PTXH       | POT4 | 04000000 | 3            | USB 3.0     | USB3 Bottom Header                |                                                         |
| PTXH       | POT5 | 05000000 | 0            | USB 2.0     | Rear USB Type C                   |                                                         |
| PTXH       | POT6 | 06000000 | 0            | USB 2.0     | Rear USB A, 3.0, Near the USB-C   |                                                         |
| PTXH       | POT7 | 07000000 | 0            | USB 2.0     | USB3 Bottom Header                |                                                         |
| PTXH       | POT8 | 08000000 | 0            | USB 2.0     | USB3 Bottom Header                |                                                         |
| PTXH       | PO9  | 09000000 | 0            | USB 2.0     | Rear USB 2.0 Type A - Near PS2    |                                                         |
| PTXH       | PO10 | 0A000000 | 0            | USB 2.0     | Rear USB 2.0 Type A               |                                                         |
| PTXH       | PO11 | 0B000000 | 255          | USB 2.0     |                                   | Both USB 2.0 Headers (Internal Hub), Used for Bluetooth |
| PTXH       | PO12 | 0C000000 | -            |             | -                                 | Led Controller, Should be ignored and not used.         |
| XHC0       | PRT2 | 02000000 | 0            | USB 2.0     | Rear USB 3.0 Type A,  All 4 Ports | Using Internal Hub                                      |
| XHC0       | PRT3 | 03000000 | 0            | USB 2.0     | USB3 Header at the middle right   |                                                         |
| XHC0       | PRT4 | 04000000 | 0            | USB 2.0     | USB3 Header at the middle right   |                                                         |
| XHC0       | PRT6 | 06000000 | 3            | USB 3.0     | Rear USB 3.0 Type A,  All 4 Ports | Using Internal Hub                                      |
| XHC0       | PRT7 | 07000000 | 3            | USB 3.0     | USB3 Header at the middle right   |                                                         |
| XHC0       | PRT8 | 08000000 | 3            | USB 3.0     | USB3 Header at the middle right   |                                                         |

*For Internal USB ports like Bluetooth - use UsbConnector = `255`*



[1]: _static/images/usb_mapping.png
