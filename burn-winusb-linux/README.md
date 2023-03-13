# burn-winusb-linux  
Burning a bootable Windows OS drive from within a Linux distribution  

## Prerequisites  
- a PC running a Linux desktop operating system with a file manager, terminal emulator, and a disk / partition editor (in my case, we will be using Pop!_OS 22.04 LTS with Nautilus, Gnome-Terminal, and GParted)  
- `sudo` or administrator privileges  
- a USB drive with 8GB of storage or greater (I personally recommend 16GB)  
- an internet connection  

## 1. Acquire Windows ISO  
- Open your default web browser and browse to the followling links depending on your desired version of Windows OS:  
  - [Windows 10](https://www.microsoft.com/en-us/software-download/windows10ISO)  
  - [Windows 11](https://www.microsoft.com/software-download/windows11)  
- From the drop-down, select the "multi-edition ISO" option, click "Confirm"  
- From the next drop-down, select your preferred language, click "Confirm"  
- Click on "64-bit Download" and wait for the .iso file to finish downloading  
- Expand the section for "Verify your download", and find the file hash (SHA256) for your language selection (make sure to find the 64-bit option)  
- Open your terminal emulator, and issue the following command: `sha256sum /path/to/Windows/disc-image.iso`; (by default, the filepath will be ~/Downloads/)  
- Compare the calculated output to the SHA hash value on the website; if the file hash matches, proceed to Step 2.

## 2. Wipe and partition the USB drive
- This step assumes you have GParted installed; if you do not, open a terminal and issue the following command: `sudo apt install gparted dosfstools mtools`  
- Take your USB drive and plug into your PC  
- Take care to back up any important files or data from this USB drive and we will be formatting and wiping it clean in the following steps (I AM NOT RESPONSIBLE FOR YOUR LOSS OF DATA)  
- In a terminal, verify which drive is the USB by issuing `lsblk`; the drive will register as "/dev/sdX", where "X" is the actual letter assigned to that drive  
- Open GParted, wait for the disk scan to complete, then select your USB drive ("/dev/sdX" in the upper-right drop-down menu)  
- If a partition is listed, right click and select "unmount"  
- In the top bar, click on "Device" > "Create Partition Table..."; select "gpt" from the drop-down menu, then click "Apply", and wait for finish  
- Right-click on the "unallocated" and select "New"; enter the "New size" as `1024`, set the "Partition name" and "Label" to `bootpart`, and set the "File system" to `fat32`; click "Add"  
- Right-click on "unallocated" again and select "New"; leave the "New size" as the default with all of the remaining space, set the "Partition name" and "Label" as `datapart`, and the "File system" as `ntfs`; click Add  
- In the top bar, click "Edit" > "Apply All Operations"; click "Apply, wait for completion; close GParted  
- To mount the partitions, simply unplug and plug the USB drive back into your PC  
- Launch your file browser and verify you now have two mounted partitions, "BOOTPART" and "datapart"

## 3. Mount and prepare the boot media
- In your file browser, browse to the location of the Windows disc image from earlier, and double-click to mount (if running Pop!_OS, by default the Popsicle USB writer will try to open; get around this by right-clicking, selecting "Open With Other Application", then picking "Disk Image Mounter"); wait for the mount to complete  
- Click into the mounted disc image, and select everything **BUT** the "sources" folder; click on the mounted "BOOTPART" partition, and paste; then create an empty folder named "sources"; from the mounted disc image, go into the "sources" folder, and copy `boot.wim` to the empty "sources" folder on "BOOTPART"  
- Click into the mounted disc image, select **ALL** files and folders, and copy them into the mounted "datapart"; wait for the operation to complete  
- Once finished, unmount the Windows disc image and both the "bootpart" and "datapart"; unplug the flash drive from your PC  

## 4. Test the boot media
- Take the USB drive and plug into the PC for which you want to load Windows OS  
- Power on the PC and begin repeatedly pressing the boot menu hotkey (will typically be either Esc, Del, F2 or F12 on most systems; consult your PC's documentation)  
- If all of the abovge steps were followed, you should see as Windows logo and the spinning circle of dots indicating boot into the "WinPE" (Windows Preboot Environment)  

## 5. Disclaimers (WIP) 
