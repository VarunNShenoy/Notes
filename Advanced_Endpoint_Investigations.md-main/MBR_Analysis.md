MBR: Master Boot Record
GPT: GUID Partition Table

Seperate sections in DISK is called partitions. MBR and GPT are different partitioning schemes that act as map for all the partitions used in the disk.

**Boot Process**

The Boot process wakes up the whole system. It starts by initializing the system's hardware components, loading the OS into memory and finally allowing the user to interact with the system.

Power on the System --> Post Check --> Locate bootable Device --> Read MBR/GPT --> Load bootloader --> Load OS

1. **Power on System**: The first step of the boot process starts with pushing the power button, which sends electrical signals to the motherboard and initializes all the components. The CPU is the first component to get the electrical signals and needs some instructions to move further. The CPU fetches and executes these instructions from a chipset deployed on the motherboard. This chipset is known as BIOS/UEFI, and it contains instructions on how to get the boot process going.

**BIOS (Basic Input/Output System) and UEFI (Unified Extensible Firmware Interface)** are responsible for verifying whether all the hardware components work properly. A system can either use BIOS or UEFI firmware. The difference between them lies in their capabilities and features.

BIOS has been used for decades and is still used in some hardware. It runs in the basic 16-bit mode and supports only up to 2 terabytes of disks. The most important thing to note is that BIOS supports the MBR partitioning scheme, which we will discuss later in the task.

UEFI came as a replacement for BIOS, offering 32-bit and 64-bit modes with up to 9 zettabytes of disks. UEFI offers a secure boot feature to ensure integrity during the system boot process. It also offers redundancy, allowing us to recover from the backup even if the boot code is corrupted. UEFI uses a GPT partitioning scheme, unlike the MBR partitioning scheme used by BIOS.

2. **Power ON Self Test**
The system is now powered on, and the CPU has executed instructions from the firmware (BIOS/UEFI) installed. The BIOS/UEFI then starts a Power-On-Self-Test to ensure all the system’s hardware components are working fine. You may hear a single or multiple beeps during this process in your system; this is how the BIOS/UEFI communicates any errors in the hardware components and displays the error message on the screen, e.g., keyboard not found.

3. **Locate the Bootable Device:** : After the BIOS/UEFI has performed the POST check, it is time for the BIOS/UEFI to locate bootable devices, such as SSDs, HDDs, or USBs, with the operating system installed. Once the bootable device is located, it starts reading this device. Now, here comes the role of the MBR/GPT. The very first sector of the device would either contain the MBR (Master Boot Record) or the GPT (GUID Partition Table). The MBR/GPT would be taking control of the boot process from here.


**Analyzing MBR File**

The Master Boot Record takes up 512 Bytes of space at the very first sector of the disk. It starts from the very first sector. Analyzing the MBR File can be started from the first line.

For understanding the ending of the MBR file record - Every two digits coupled in hexadecimal represents 1 byte, once the first 512 bytes of the disk completes that means the MBR file record has ended.

In the Hexadecimal editor (HxD) we are using 16 bytes are present in each row meaning that the first 32 rows of the disk would be the whole MBR.

Another way to spot the end is by looking for MBR Signature. The MBR Signature is represented by **55 AA**, which marks the end of the MBR Code.

Structure of the MBR :

<img width="558" height="321" alt="image" src="https://github.com/user-attachments/assets/d2f7c8f7-244b-49f3-92af-289ca1ea3d07" />

**BootLoader Code: (Bytes 0-455)**

The first component of the MBR is the Bootloader Code, They cover 446 out of the total 512 Bytes of the MBR.
The Bootloader code contains the intial BootLoader. The initial bootloader is the first thing that executes in the MBR. This initial bootloader code has a primary purpose of finding the bootable partition from the partition table present on the MBR.

<img width="569" height="580" alt="image" src="https://github.com/user-attachments/assets/0d45f089-6ad3-4be3-9983-97c400473a5a" />


**Partitions Table (Bytes446-509)**

The second component of the MBR is the partition table, which comprises 64 bytes (Bytes 446-509). This table contains the details of all the partitions present on the disk. One of the partitions in the disk contains all the operating system files necessary for booting, known as a bootable partition. The initial bootloader that was started from the bootloader code of the MBR  finds the bootable partition from this partition table, and it loads the second bootloader from it. The second bootloader then loads the Operating System's Kernel. 

<img width="575" height="685" alt="image" src="https://github.com/user-attachments/assets/b17daff2-eee3-4478-b112-b3f91f5a86eb" />


The MBR Disk has a total of 4 partitions and each partitions is represented by 16 bytes in the partition table. 

Unlike the Bootloaded coder - the partition table gives a lot of information. All the bytes have some specific meaning

Check the Hexadecimal Values as per below screenshot.

<img width="397" height="33" alt="image" src="https://github.com/user-attachments/assets/8ce05aa8-ee33-4264-91c3-1d923b0414c2" />

<img width="904" height="514" alt="image" src="https://github.com/user-attachments/assets/eba1a729-5cc9-47c5-8bb9-5c199df47ce4" />


Boot Indicator: This byte tells you whether the partition is bootable or not. A bootable partition contains files necessary for the operating system to boot. This boot indicator can only have one of the two values: 80 or 00. If it's 80, it means that the partition is bootable; else, if it's 00, it means that the partition is not bootable.  In Windows based systems, C: is the partition that is typically bootable. If visualized through the partition table, this partition would have the boot indicator set to 80.

Starting CHS Address: Cylinder Head Sector (CHS) is the 3 bytes that tell you where this partition is starting from on the disk. It will give you the starting physical address of the partition, such as the cylinder, head, and sector number. This field is not that important as we now have the simplified logical address of the partition in the form of the Starting LBA Address discussed ahead.

Partition Type: Every partition uses a filesystem such as NTFS, FAT32, etc. This byte indicates the filesystem of the partition. The partition we are taking as a reference has this byte as 07, which means it is an NTFS partition. Every filesystem has its own unique byte.

Ending CHS Address: The last 3 bytes at the end of the CHS Address indicates the physical location where the partition ends on the disk. This field is also not that important, just like the stated CHS address, as we mostly use the logical address (LBA) instead of the physical address.

Starting LBA Address: Logical Block Addressing (LBA) is the logical address that indicates the start of the partition. We saw that the Starting CHS Address also gives us the starting address of the partition, but because CHS gives you the physical address of the partition, it becomes difficult for us to locate it. However, the Starting LBA Address gives you the logical address of the partition rather than the physical address. You can use it to easily find the start of the partition on the disk in a hexadecimal editor. By locating the partition on the disk, you can also carve data from a disk's hidden or deleted partitions. Based on the partition we are analyzing as a reference with 00 08 00 00 LBA, we will use this logical address to locate our partition later in this task.

Number of Sectors: These last 4 bytes of a partition tell you the number of sectors in the partition. We will calculate the sector's size by using this field ahead.

Byte 0 — Boot Indicator (80 = bootable, 00 = not bootable)
Bytes 1–3 — Starting CHS (Cylinder, Head, Sector) address — an old-school physical addressing format.
Byte 4 — Partition Type (e.g., 07 means NTFS)
Bytes 5–7 — Ending CHS address
Bytes 8–11 — Starting LBA (Logical Block Addressing) — more modern and used today
Bytes 12–15 — Total number of sectors in the partitio

Locating the Partition:

The Parttion table starts from 446 Byte. In the following example its 80, They the first 16 bytes from 80 gives you the first partition.

<img width="409" height="131" alt="image" src="https://github.com/user-attachments/assets/890f8e7f-5455-444c-8945-768e4b6e4059" />

Now to calculate the size of the the partions, consider the number of sectors, which is the last 4 Hexa decimal Values of the partition in this case it is **00 F0 BF 03**

Now convert it to Decimal by highlighting thr hexadecimal values - in the right side you get the INT32 Value which is in decimal, or use an converter, In this case it is 62910464 sectors. Since each sector is 512 bytes, multiply with 512 bytes to get the size of the partion.

62910464 * 512 bytes  --> 32210157568 Bytes 

Convert to GB - 32 GB.
















