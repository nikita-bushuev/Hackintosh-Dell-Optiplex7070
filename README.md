# OpenCore EFI for Dell Optiplex 7070 SFF

Modified [OpenCore EFI from webleon for Dell Optiplex 7070 SFF](https://github.com/webleon/Hackintosh-OptiPlex-7070-SFF) with fixes for WiFi, Bluetooth and GPU (RX Radeon 550 Lexa Core).  
What is working: everything.

<img src="https://raw.githubusercontent.com/nikita-bushuev/Hackintosh-Dell-Optiplex7070/main/Images/About-OS.png?token=GHSAT0AAAAAABUOJVFXJ6ZMQFOSOXE52JX4YUKVVNQ" />

## Verified Specification

| **Component**    | **Model**                                   |
| ---------------- | ------------------------------------------- |
| CPU              | Intel Core i7-9700 @ 3800 MHz               |
| Motherboard      | Dell OptiPlex 7070                          |
| RAM              | 32GB (4 x 8GB) DDR4 @ 2666 MHz              |
| GPU              | Radeon RX 550 (Lexa Core)                   |
| Audio Chipset    | Realtek ALC255                              |
| Ethernet         | Intel(R) Ethernet Connection (7) I219       |
| WiFi & Bluetooth | Intel(R) Wireless-AC 9560 160MHz            |
| OS Disk (SATA)   | *Corsair Force LE SSD (SATA-III) 256GB      |

*Bundled Samsug NVMe PM891a SSD was replaced with Corsair Force SATA SSD. PM891a is NOT supported in macOS BigSur/Monterey and causes bootloops during installation.

**macOS version**: 12.4 (21F79) \
**OpenCore version**: 0.8.0

## Installation

### BIOS Settings
- `General` → `Advanced Boot Options` → `Disabled`
- `System Configuration`:
   - `Serial Port` → `Disabled`
   - `SATA Operation` → `AHCI`
- *`Security` → `TPM 2.0 Security` → `Disabled`
- `Secure Boot` → `Disabled`
- `Intel® Software Guard Extensions™` → `Intel® SGX™ Enable` → `Disabled`
- `Virtualization Support` → `VT for Direct I/O` → `Disabled`

*TPM 2.0 Security could be working on your system (Enabled). I recommend you to leave it enabled and if it causes bootloops or stange behavior set disabled.

### BIOS Settings with GRUB

- Set Pre-Allocated DVMT to 64M: `setup_var 0x8DC 0x02`
- Disable CFG lock: `setup_var 0x5BE 0x00`

### Bootable USB

1. Follow [this guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) to create your bootable USB.

2. Clone this repository and copy "BOOT" & "OC" directories to your "EFI" directory on your bootable USB. The structure should look somewhat like this: `EFI -> BOOT, OC`.

### GPU configuration
- Radeon RX 550 (with Lexa core):  
Everything should be working fine. I used [this fix](https://www.reddit.com/r/hackintosh/comments/tdz65y/rx_550_lexa/) from reddit to get it working in macOS.

- Other GPU:  
Remove GPU node from `EFI/OC/config.plist`: `DeviceProperties` → `Add` → `PciRoot(0x0)/Pci(0x1,0x)/Pci(0x0,0x0)`


### SMBIOS

4. Use [this tool](https://github.com/corpnewt/GenSMBIOS) to generate your unique SMBIOS info.

- SMBIOS has to be unique, you cannot use one present in this repository.

- Run the tool and select `Generate SMBIOS`.
- Select the appropriate model for your hardware (e.g. `iMac19,1`).
- Go to [Apple Coverage](https://checkcoverage.apple.com/) and paste generated _Serial_. You need "Invalid Serial" or "Purchase Date not Validated" message. If you get something another you have to generate SMBIOS data and check it again.
- Open _config.plist_ and search for `PlatformInfo -> Generic` and replace these values:
  - _SystemProductName_ - Model
  - _MLB_ - Board Serial
  - _SystemSerialNumber_ - Serial
  - _SystemUUID_ - SmUUID
- _ROM_ entry should be set to your [network card's MAC address](https://www.wikihow.com/Find-the-MAC-Address-of-Your-Computer), without separators (e. g. `:`, `-`).

### After Installation

Follow [this guide](https://dortania.github.io/OpenCore-Post-Install/universal/update.html#_2-mount-your-efi) to mount your hard drive's EFI partition. However Dell motherboard doesn't see it automatically. We need add it manually through the BIOS.  
Go to `General` -> `Boot Sequence` -> `Add Boot Option`.  
In the opened window:
- Set `Boot Option Name` (e.g. `OS EFI`).
- In `File Name` pick `\EFI\BOOT\BOOTx64.efi` file from your mounted EFI partition (click three-dots button).

After reboot EFI should be visible.