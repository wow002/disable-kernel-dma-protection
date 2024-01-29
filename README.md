# disable-kernel-dma-protection

method 1:
if you have an option in your BIOS to disable kernel dma protection, or if thats not an option maybe vt-d, or whatever virtualization you have will be there, disable that instead of doing these other methods. these methods are for people who truely don't have a option in their bios or don't have access to their bios

method 2:
downgrade to a windows version before 1803 (the version before that is 1709), 1803 was when kernel dma protection was added

method 3:
disabling virtualization based security (what includes kernel DMA protection) by corrupting the DMAR ACPI table

before we get started, you should double check everything about your computer to find if theres a way to disable vt-x or whatever virtualization you have
this is for people who either don't have access to their bios or truly don't have a option in their bios.

now lets get started by talking about what the DMAR ACPI table is, it's a in-memory reporting structure that reports the memory mapped location of IOMMU to the operating system.
if the OS can't read the configuration data from a valid DMAR table it cannot locate IOMMU and cannot enable virtualization features.
the DMAR table isn't signed nor protected.

It's possible to search for the DMAR table in memory by using PCILeech since it's starting with the signature DMAR.
while this would make it easy to make it automatic for everyone, this doesn't work for all systems so instead i'll teach you how to find the address yourself

Boot into any linux installation (running ubuntu from a bootable usb without installing it works) and run the following command

```
sudo dmesg | grep DMAR
```

note: the address changes when you make big changes to bios like enabling or disabling secure boot. the dmar acpi table will stay corrupted if you already wrote to it but if you decided to change your bios between the time you got the address and the time you booted and ran the command then you'd have a problem

don't worry about any of the other results besides the first line starting with "ACPI: DMAR" then the address, it will look similiar to this

```
"0x000000000000000"
```

now after we've gotten the address, all we have to do is run this command with pcileech on our second PC when we're booting our main PC, before windows boots

note: make sure to replace 0x00000 with your address you got above. also make sure you don't have any pre boot authenticators like a password before booting.

```
 pcileech.exe write -min 0x000000 -in 000000000000000000000000000000000000000000000000 
```

the result: by nulling the first 24 bytes with null data we corrupted the DMAR ACPI table making it impossible for the OS to find iommu, disabling all virtualization based security including kernel DMA protection
