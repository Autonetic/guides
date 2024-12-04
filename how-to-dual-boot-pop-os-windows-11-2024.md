Dual Boot Pop OS 2024

To dual boot Pop!_OS 2024 with Windows, follow these steps:



**Preparation**

1. **Backup your data**: Ensure you have backed up all important files and data to an external drive or cloud storage, as the installation process will erase all data on the target partition.
2. **Free up space**: Shrink your Windows partition to create free space for Pop!_OS. search diskmgmt and run, Right-click on the Windows partition, select "Shrink Volume," and allocate at least 50 GB for Pop!_OS.
3. **Download Pop!_OS**: Obtain the Pop!_OS 22.04 LTS ISO file from the official website.
4. **Create a bootable USB**: Use a tool like balenaEtcher or my favorite, UUI ( Universal USB Installer ) to create a bootable USB drive from the Pop!_OS ISO file.

**Installation**

1. **Boot from the Pop!_OS USB**: Insert the USB drive and restart your computer. Enter the BIOS settings (usually by pressing F2, F12, or Del) and set the USB drive as the first boot device. Save the changes and exit the BIOS.
2. **Select language and keyboard**: Follow the Pop!_OS installation wizard to select your language and keyboard layout.
3. **Partitioning**: Choose the free space you created earlier and allocate it for Pop!_OS. You can use the default partitioning scheme or customize it to your liking.
4. **Install Pop!_OS**: Continue with the installation process, which will install Pop on the selected partition.

**Final Steps**

After a reboot, if you find you are booting straight into Pop and cant see a windows option, even after pressing esc, F2 or Del etc, then follow these final steps to ensure systemd-boot finds your windows partition and makes it an entry in the boot config.


1. **Install `os-prober`**:

```bash
sudo apt update
sudo apt install os-prober
```
This utility will automatically detect and add Windows to the systemd-boot menu.

2. **Run `os-prober`**:
```bash
sudo os-prober
```
This command will scan for operating systems and output a list of detected entries. You should see the Windows Boot Manager entry.

3. **Mount the Windows boot partition**:
```bash
sudo mount /dev/sda1 /mnt  # Replace sda1 with your Windows boot partition
```
4. **Copy the EFI/Microsoft folder**:
```bash
sudo cp -r /mnt/EFI/Microsoft /boot/efi/EFI/
```
5. **Reboot**:
```bash
sudo reboot
```

The idea behind this process is simply the PC doesnt need to ESP partitions and we basically want to copy all the files from windows boot manager to systemd-boot to allow us to easily swap OS!

Note: If you're still having issues, you can try installing GRUB instead of systemd-boot. However, this would replace systemd-boot as your default bootloader.

Remember to replace `/dev/sda1` with the actual partition device containing your Windows installation. Also, ensure that your BIOS is set to boot from the EFI partition (usually `/dev/sda1`) and not from the MBR (Master Boot Record).



