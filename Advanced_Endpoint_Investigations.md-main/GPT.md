UEFI firmware with the GPT partitioning scheme replaced BIOS firmware with the MBR partitioning scheme. This replacement occured due to some limitations in the BIOS and MBR.
The GPT supports up to 9 zettabytes of hard disks unlike MBR which supports maximum of 2 terabytes. The GPT also supports up to 128 partitions, unlike the MBR, which only supports 4. There are some other differences between both of the partitioning schemes. 
GPT is now a preferred partitioning scheme in most modern systems. It is compatible with systems that have UEFI firmware.

Structure of GPT

1. Protective MBR
2. Primary GPT Header
3. Partition Entry Array
4. Backup GPT Header
5. Backup Partition Entry Array


**Protective MBR**: 

Ideally, if the disk is GPT partitioned, the system should have UEFI firmware to handle it.  However, some legacy systems still use BIOS firmware even though the disk is GPT partitioned. This can be a problem as the BIOS firmware is designed to work with the MBR and UEFI firmware is designed to work with the GPT. To solve this problem, the GPT has Protective MBR. The Protective MBR is in the disk's first sector, partitioned with the GPT. The purpose of the Protective MBR  is to signal the BIOS system that this disk is using the GPT, so please don't mess up with it thinking that it is the MBR.

The Protective MBR also has three components, just like the general MBR, but with a few differences. Below is a screenshot of the Protective MBR found in the GPT, with its three components highlighted with different colors.

<img width="643" height="550" alt="image" src="https://github.com/user-attachments/assets/95d02e1b-5f44-4d27-b150-5a89dc59beab" />

Details of the components are given below:

1. Bootloader Code: This bootloader code is not the same as it is in the general MBR. This bootloader code does not perform any function during the boot process. It is just there to look like it's the same standard MBR bootloader. This would be all 00s in most scenarios; however, sometimes, this can contain some placeholder code for legacy compatibility.
2.  Partition Table: This partition table contains only one partition (the first 16 bytes), and this partition has one job; to redirect the system to the EFI Partition (which we will discuss later). The screenshot of the protective MBR above shows that it only has one partition in the table, and the other partitions are labeled with 0s. In this single partition, there is only one important thing: the 4th byte. This byte is set to EE, indicating that this is a GPT-formatted disk.
<img width="618" height="64" alt="image" src="https://github.com/user-attachments/assets/6fcb8993-4021-4ed3-a39d-7a8caeaa69fd" />
3. MBR Signature: The MBR signature is the same as in the standard MBR. It is set to 55 AA and marks the end of the Protective MBR.

**Primary GPT Header:**

The GPT header starts right from the next byte (the start of sector 1) after the Protective MBR ends at 55 AA (the end of sector 0). It acts as a blueprint of the partitions on the disk. All the bytes in the GPT header have a specific meaning. Below is a screenshot of the GPT header with these bytes highlighted with different colors. It has the whole 512 sector space but occupies the first 92 bytes of space, and after these 92 bytes, there would be all 00 bytes for padding purposes to complete the sector's total bytes. So, for the Primary GPT header, you only need to focus on the first 92 bytes, which can give you some meaningful information.

<img width="658" height="119" alt="image" src="https://github.com/user-attachments/assets/0273045d-54cb-44ef-823b-bee663649b02" />

The table below shows the position of these bytes and their field names.

<img width="1030" height="821" alt="image" src="https://github.com/user-attachments/assets/e977037d-e8e0-4262-822c-5a68611838dc" />

Each of these 14 fields has a specific purpose and meaning. Below is an explanation of all these fields. Before starting with the explanation of these fields, it is important to note that some of the fields represent Logical Block Addresses (LBA). To calculate the exact address through these LBAs, you must follow the same steps we performed while locating the partition in the MBR task. These steps included reversing the bytes stored in a little-endian format, then converting them to decimals and searching them on the HxD tool to jump to the exact LBA location.


1. Signature: This field has a value 45 46 49 20 50 41 52 54 which recognizes it as a GPT header. This value is always at the start of the GPT header.
2. Revision: The revision number is of 4 bytes and it represents the version of the GPT. Most of the times it would be 00 00 01 00 which means the GPT version is 1.0.
3. Header Size: This field represents the size of the GPT header. It is typically 5C 00 00 00 in hex and if you convert it to decimal (after reversing the order of bytes as they are in little-endian), it is 92 bytes which is the length of the GPT header.
4. CRC32 of Header: This is the CRC32 checksum of the GPT header, which if changed, would indicate that either the GPT header is tampered or corrupted.
5. Reserved: These are reserved bytes. The purpose of having them is to utilize them for any future changes in the GPT header.
6. Current LBA: The Current Logical Block Address (LBA) indicates the location of the GPT header. We know that its location is in sector 1, and we can verify this by converting the 8 Current LBA bytes 01 00 00 00 00 00 00 00 into decimal.
7. Backup LBA: In the GPT partitioning scheme, we have a backup of the GPT header as well, which we will be studying later on in this task. This field indicates the LBA of the backup GPT header.
8. First Usable LBA: This LBA address indicates the first address from which the partition can start on the disk.
9. Last Usable LBA: This LBA address indicates the last address to which the partitions on the disk can be written. Any partitions cannot occupy the disk space after the last usable LBA.
10. Disk GUID: This field is of 16 bytes and it presents a Globally Unique Identifier of the disk. The purpose of this GUID is to distinguish the disk from any other disks present in the system. In the current GPT header that we are analyzing, these bytes are 1D F1 B0 D6 43 BE 37 4E B1 E6 38 66 EC B1 73 89. We can convert them to the standard GUID format of the disk by just reformatting them as 1DF1B0D6-43BE-374E-B1E6-3866ECB17389.
11. Partition Entry Array LBA: This LBA address indicates the start of the Partition Entry Array which we are going to discuss ahead as the 3rd component of the GPT.
12. Number of Partition Entries: This field indicates the number of partitions that are on the disk. The GPT supports 128 partitions, unlike the MBR, which supports 4 partitions only. The value of this field is 80 00 00 00 which if converted to decimal will be 128.
13. Size of Each Partition Entry: This field indicates the size occupied by each partition entry array. In this example, it set to 80 00 00 00 which is 128 in decimal. It is important to note that this is not the size of the partition itself. This is just the size of partition entry array that we would be discussing next.
14. CRC32 of Partition Array: This is the CRC32 checksum of the whole partition entry array, which if changed, would indicate that either the partition entry array is tampered or corrupted.

Partition Entry Array:




