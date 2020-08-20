# OpenCore EFI for TRX40 Threadripper - AMD Hackintosh Bare Metal

![Test Image 16](Images/AboutMac.jpg)

This repository provides the basic contents for an EFI folder to successfully boot "bare metal" MacOS on a __TRX40-based__ motherboard, using a Threadripper CPU, such as a 3960X, 3970X or 3990X. This repository will  be somewhat  generic and atttempt to discuss most TRX40 motherboards based on input from fabiosun's work at this [site](https://www.macos86.it/topic/3307-trx40-bare-metal-vanilla-patches-yes-it-worksbutproxmox-is-better/#comments). To date (see dates of this repository at bottom of  this Intro) the EFI should boot   _Catalina_ or _Big Sur_ (the latter in beta). The intended SMBIOS is _iMacPro1,1_ although provisions are available for running _MacPro7,1_ which will be described below.

This repository is only designed for the OpenCore bootloader. __OpenCore__ (OC) is best updated via Pavo's [OCBuilder](https://github.com/Pavo-IM/ocbuilder/releases) app. Accordingly, if you have a working EFI boot folder, based on this repository, you can update various components of it as you see fit using OCBuilder. But do be careful not to over write files or folders unique to this build. If updated, please study the Docs folder in OpenCore to see if the structure of the __config.plist__ file needs to be changed (this respository will attempt to be current with the most stable release). Keep in mind that OpenCore is evolving, and consequently, new versions can substantially effect the overall structure and functioning of your __config.plist__ file. I feel that editing is best done using PlistEdit Pro or [ProperTree](https://github.com/corpnewt/ProperTree). While [OpenCore Configurator](https://mackie100projects.altervista.org/download-opencore-configurator/) may be used, if it is out of date with your OpenCore commit, you can get corruption of the __config.plist__ file which you may not notice for some time (and even find difficult to track down and correct).

The EFI folder in this repository should be placed on the EFI partition of your boot drive. Don't make the mistake of placing the contents of the EFI folder of this repository on the EFI partition. Instead, the EFI partition contains an EFI folder, which in turn contains the Boot and OC folders.

OpenCore version numbers are not incremented for each minor adjustment, but incremented once stable. However, there are dates of commits indicated within each version before the version number is incremented. While there may  be only small changes within a version, the changes can have marked structural changes and yet not be fully documented. Accordingly, it is best to use final release versions. Due to the sometimes daily changes, this repository will only upload changes if the commit seems stable and then note the date of compilation along with the version number. 

The present EFI folder is: 

***v061 - 8/12/2020***

***Commentary Update - 8/20/20***


## A. Contents

### 1. ACPI

The SSDT files nomenclature and structure reflect the generic nature of this repository. The SSDT files are numbered, partlly to indicate the correct order of their loading, and partly to to separate them for different motherboards.

Those numbered 0 to 9 should apply for all TRX40 motherboards. Those numbered from 10-19 for MSI motherboards; 20-29 for Gigabyte motherboards and 30-39 for ASRock motherboards. 50-59 are reserved for add-in-cards (AIC). The one included example is for a Gigabyte Titan Ridge Thunderbolt AIC (GB-TR-TB3-AIC). This TB card is meant to be placed in Slot 4 (PCIe4), It  was flashed firmware NVM 33. The SSDT for this is _50-SSDT-TRX40-GB-TR-AIC-Slot4-v5.aml_. The internal USB of TB3 is assigned XHC5, which does not conflict with the renames discussed below.

VIrtually all SSDT files numbered greater than 9 are simpy to re-name devices and may not be essential for any functional improvement. An exception to this, are thunderbolt SSDT which are essential for proper functionality. 

While SSDT files can inject function, those are not presented here. The included SSDT files can have properties to inject added to them; an example will be described later. Some information that can be injected will help to populate System Information's PCI section as shown below.

The GPU SSDT files, _5-SSDT-TRX40-GFX0.aml_ and _5-SSDT-TRX40-GFX0-FGX1.aml_  provide correct re-naming of the devices (although much is provided by WEG). The first file is for single GPU located in slot 1; the second for dual GPU systems located in slots 1 and 3.

There are SSDT files to rename NVMe drives such a _12-SSDT-TRX40-NVMe-ANSx.aml_. This files, since it's numbered "12" is meant for an MSI mobo. It will rename 3 NMVe drives as written. Unless data to inject is added, the SSDT file will not contribute information to the System Information/PCI section. However, by simply renaming the device, the desktop disk icons are forced to be updated and changed from external to internal icons. 


This section shows 2 NVMe re-names along wtih the D0B8 rename (this and the D1B8 USB devices are common to all TRX40 mobos). Also shown are the effects of the EC-USBX-MCHC-SBRG SSDT, which creates the PMCR device for better PowerManagement.

__PCI0 Section:__
![Test Image 0](Images/PCI0.jpg)

The SSDT file _12c-SSDT-TRX40-NVMe-ANSx-Inject.aml_ is provided as an example of how to inject data about a device, in this case an NVMe drive, to System Information's PCI section. If you open it to edit (use MaciASL), you'll see near the top a reference to "DTGP". This external method is kept in the ACPI folder and is the 2nd one called in the entries (_1-SSDT-DTGP.aml_); this file must be present for everything to properly  work.

The information changes in these files should be changed to reflect the brand and sizes of NVMe drives that you are using in your build. For example, if ANS1 presently indicates that the NVMe SSD is "Corsair Force 600 NVMe 1TB SSD". And if your SSD is a Samsung, then change the name (and model) to something like "Samsung PRO 970 NVMe 512GB SSD ". Such editing can be accompished by studying the files (don't expect much hand-holding with these modifications).

There are sets of SSDT files which rename the various USB devices on the TRX40 builds. The ones named DOB8 and D1B8 are common to all builds and are renamed to XHC1 and XHC2, respectively.


This image shows at the bottom, the first (or only) GPU in slot 1.

__S0D1 Section:__
![Test Image 1](Images/S0D1.jpg)


This image shows the USB devices XHC3 (which may vary with different mobos), XHC-XHCI (which seem common to all TRX40s), and at the bottom, a 2nd GPU (slot 3).

__S0D2 Section:__
![Test Image 2](Images/S0D2.jpg)



The result of using the above USB SSDT files can be verified by using the Hackintool app (shown below) or with IOReigstryExplorer. WIthout the use of these SSDT files, the Hackintool USB section would be empty of all devices; with their use, this section is now properly populated.

__Hackintool - USB Devices:__
![Test Image 3](Images/Hackintool-USB.jpg)



__Cancelling a device with an SSDT__

With Hackintoshes, we all personalize our builds. One area where this is apparent is with BT/Wifi: some don't it use at all, some try to use the built-in device (which will be discussed later in the Kexts section), some use an AIC and some swap out the built-in card for a Mac compatible model. Each of these can require different SSDT and kext files. We'll ignore the first two cases, but if an AIC is used, it is best to deactivate the internal module which is an AX200 for most TRX40 mobos). This can be done via an SSDT. (Of course, no deactivation is desired if the BT module is swapped out.)

Before deactivating the BT module, we need to locate which USB device port is powering the internal module using IORegitryExplorer. This can be done with or without the BT AIC installed. Once that USB device port is located, in this case it was PRT5 under XHC on the GB Designare mobo, we re-define all of the ports except that port with the SSDT. The image below shows an except from the SSDT illustrating how the internal BT is disabled. If no PRT5 is being defined: it disappears and so the internal BT module also disappears.
![Test Image 14](Images/SSDT-NoBT.jpg)


### 2. Kexts

The contents of the Kexts folder can be broken down into various groups.

The first grouping are the essential kexts: AppleALC, AppleMCEReporterDisabler, Lilu, SmallTreeIntel82576-I211, VirtualSMC and WhateverGreen (WEG). Within the __config.plist__ file, in the Kernel section, Lilu must be first in order, followed by VirtualSMC. Similarly, WEG should be present before other graphics related kext files.

The second groupding are the BT/Wifi kexts: AirportBrcmFixup, BrcmBluetoothInjector, BrcmFirmwareData, and BrcmPatchRAM3. If you've swapped out the stock Intel BT module for a Mac-compatible version you'll want all of these enabled within the __config.plist__ file. On the other hand, if you've added a PCIe BT/WiFi card such as the Fenvi FV-T919 (with a Broadcom 94360CD), then most of these kext files are optional.

Note, there are recent concerns with sleep issues, jitterey mouse and computer freezes with Radeon VII and Radeon 5700XT graphics cards. It appears to be an issue with WEG. If you are using these cards (and perhaps even any 5x00 card), try disabling WEG and re-booting the system to see if the issues are resolved. When WEG is disabled, you might notice some cosmetic glitches, such as pink/purple lines at the top of the screen, during boot (while the Apple logo progress bar is on-going). These are  inconsequential, so don't worry about them (WEG normally surpresses them without you're knowing it). This problem does not seem to be an issue with older grapics cards.

On the TRX40 build, WEG (as of 8/20/20), prevents booting into Big Sur, so either disable it, or if enabled, pass "_-wegbeta_" in boot arg to allow booting into both Catalina and Big Sur.

MacProMemoryNotificationDisabler is only to be enabled when using SMBIOS _MacPro7,1_ (which requires Catalina). __Please note: the most stable setup is SMBIOS iMacPro1,1.__ This SMBIOS is closest to our build. If you have trouble while using MacPro7,1, you were warned and don't ask for help; instead, sign out of iCloud and change the SMBIOS to iMacPro1,1, and after re-booting sign in again to iCloud.

[SMCAMDProcessor](https://github.com/trulyspinach/SMCAMDProcessor) is useful for providing CPU temperature and frequency information.  The kexts were designed for the X570 AMD chip, and not surprisingly, it is unstable with Catalina and Big Sur with the TRX40 running bare metal. Please refer to its GitHub for the latest updates and for downloading the associated AMD Power Gadget app which presents the data. However, _AMDRyzenCPUPowerManagement.kext_ and AMD Power Gadget app is stable with Catalina with the TRX40 build, but it is not stable under Big Sur and Big Sur won't boot if either of these kexts are enabled.

The above kext files may be updated independent of this repository using [Hackintool](https://www.insanelymac.com/forum/topic/335018-hackintool-v286/), [Kext Updater](https://bitbucket.org/profdrluigi/kextupdater/downloads/) or [OCBuilder](https://github.com/Pavo-IM/ocbuilder/releases). 


### 3. System Information / PCI

This section shows the current status of the System Information section in Mac OS. The image shown represents the various devices being re-named by the DevicesProperties section of OC, some of which are included in the _config.plist file_.

![Test Image 4](Images/SystemInfo-PCI.jpg)

Aside from providing information to the PCI section (which is mostly costmetic, so nothing to worry about if allowed to remain empty), from SSDT files, the OpenCore DeviceProperties (DP) section can inject this data as well. Here is a section of DP showing injection of NVMe drives for the MSI TRX40 Creator mobo. The arrow points to a _#_ symbol. This symbol in OpenCore inactivates an entry. All DP entries in the supplied _config.plist_ file are inactivated. Simply delete the _#_ symbol to activate.

![Test Image 5](Images/DP.jpg)

The device pathway is derived from Hackintool by selecting the PCIe tab at top then the red high-lighted button at the bottom of the window:

![Test Image 5](Images/Hackintool-DerivePath.jpg)

After clicking on the button, a window will open with a highlighted text file. In this example, the drives are highighted. By copying and pasting into OpenCore's DP section shown above (and editing the actual entry data), you can custom this section for your own build. After rebooting, this data will show up in the System Information PCI section.

![Test Image 6](Images/CopyDevicePath.jpg)


### 4. Drivers

Only a few drivers are required with OpenCore: HSSPlus, OpenCanopy and OpenRuntime. AudioDxe, CrScreenshotDxe and OpenUsbKbDxe are optional. CrScreenshotDxe is a useful tool to record screen shots when the OC menu is being used: press <F10> to record an image (example shown below). The image will be stored on the EFI partition. 

If you choose to use the OpenCanopy drive for the menu system, some items may be hidden, based on a setting in Misc/Boot/ called HideAuxillary. If this is enabled, all items marked as Auxillary in the Misc/Tools are enabled as long as they are also Enabled. In the example below, the ResetSystem.efi was set up for Shutdown behavior.

![Test Image 7](Images/Shutdown.jpg)

![Test Image 8](Images/Misc-Auxillary.jpg)


### 5. BIOS Settings

Reportedly, the Gen3 setting is better for maxmizing performance from currently available GPUs. Experiment with the Gen3 vs Auto setting and see what works best for your build. Most other BIOS settings are default. IOMMU and SMT need to be enabled should you wish to boot via VM such as with Proxmox (not discussed in this repository; see fabiosun's thread on Proxmox listed at bottom). ErP and Above 4G decoding are best left off.

|                    |              |
| ------------------ | ------------ |
| ErP Power                 |  Disabled     |
| IOMMU     |  Enabled |
| SMT     |  Enabled |
| CSM                |  Disabled    |
| Above 4G decoding  |  Disabled     |

It is also probably best to keep _npci=0x2000_ in the boot arg no matter the setting of _Above 4G decoding_. It is said that if _Above 4G decoding_ is enabled that _npci=0x2000_ should be removed from the boot arg; I've not seen this as a problem, so I've been leaving in the boot arg.


### 7. SMBIOS - How to Easily Update in OC

As mentioned above, the SMBIOS iMacPro1,1 is probably the best for this build and is very stable when running under Mojave.

The SMBIOS data can be generated using an old copy of Clover (but do NOT use Clover to edit the __config.plist__ files for OpenCore), or using the recommended [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS).

If you already have SNs and UUIDs values in an existing OpenCore config file, then cloning that SMBIOS data is easy. OC allows you to simpliy copy and paste sections, such as the PlatformInfo section, between config files. (However, keep in mind that if you _change_ your SMBIOS, first log out of iCloud, perform the SMBIOS change, reboot, and then re-log into iCloud.)

The images below show the steps. When editing the __config.plist__ file, the recommended editors are PlistEdit Pro or [ProperTree](https://github.com/corpnewt/ProperTree). It is probably bes to avoid using XCode for editing OC files as it sometimes corrupts the __config.plist__ file by changing i_nteger_ values into _real_ values, which generates errors during boot.

- Backup the config files before starting.
- Open both files you're to copy between.
- Highlight and copy the old SMBIOS section (_PlatformInfo_) that has your working SNs, etc.
- Go to the new config file that has a SMBIOS with no SNs and highlight its PlatformInfo section.
- Do a paste, which leaves you with a file like the one shown in the image below.
- Delete the PlatformInfo section marked "PlatformInfo" (highlight and press the delete key). 
- Highlight and click into the remaining section marked _PlatformInfo 2_, editing out the space and 2 (" 2").
- Then save the file.

![Test Image 9](Images/OC_copy.jpg)

![Test Image 10](Images/OC_paste.jpg)


### 7. Stability Issues

There are presently two basic concerns with this build. The first is the inability to perform a shutdown from with the macOS. If Shutdown is selected from the Apple menu, the computer reboots (panic restart) immediately after 'clicking' off.

The second item concerns sleep and sudden shutdowns from sleep (but with the above panic restart). The best method to avoid this problem is to turn off all settings within Energy Saver, leaving only "Prevent computer from sleeping..." as shown below.

![Test Image 11](Images/EnergySaver.jpg)

When a system behaves poorly and won't boot well, consider re-flashing BIOS. BIOS can become corrupted with repeat crashes and forced re-starts; things that happen during a Hackintosh setup.

But before that happens, do perform a full backup. I recommend using [CCC](https://bombich.com). 

Also, have a spare drive or USB that contains an alternative EFI folder for emergency booting. You might consider for this backup EFI folder to remove all un-necessary kexts, turn off or remove all but the basic SSDT files. This can help for trouble-shooting.

Finally, if all else fails, consider re-installing macOS from Recovery. To boot into Recovery, Big Sur in particular requires setting Misc/Security/ScanPolicy to 0.

The Hackintool app can help with sleep issues using it's Power tool (image below). Click on the screw driver tool (arrow); when prompted, enter your pw; and then the red high-lighted bars (which are green in this image), will change from red to green. Simple.

![Test Image 15](Images/Hackintool-Power.jpg)


### 7. Native NVRAM

Native NVRAM was successfully implemented. There are several steps required. These are outlined in detail on fabiosun's thread. These steps include determining MmioWhitelist and the optimum slide value (both of these are discussed in this [post](https://www.macos86.it/topic/3307-trx40-bare-metal-vanilla-patches-yes-it-worksbutproxmox-is-better/?do=findComment&comment=82868).

Next, the NVRAM is activated by following these steps: 

- Adjust Quirks as shown below and set WriteFlash to Yes (enable; while leaving LegacyEnable and LegacyOverwrite disabled). In the screenshot below, you can see _#MmioWhitelist_ (the _#_ comments out this iitem), which contains a brief example of what the MmioWhitelist contains, as a guide for making your own (this item can be deleted as you wish):
![Test Image 12](Images/NVRAM-1.jpg)

- Within the NVRAM section, see that csr-active-config is set to FF0F0000 under Add and csr-active-config is entered under delete: 
![Test Image 13](Images/NVRAM-2.jpg)


### 8. Performance

Since the first successful TRX40 system to run macOS was based on Proxmox VM (thanks to fabiosun [here](https://www.macos86.it/topic/2509-guide-trx40-osx-bare-metal-proxmox-setup62-1-updated-25-07-2020/), any performance on bare metal must be compared to VM performance.

So far, the performance between the two methods are close. The difference may be somewhat related to the tests themselves.

A typcial resposne in Cinebench 20 (using an MSI TRX40 Creator with 3970X CPU) gives a resullt from 17,000 to almost 17,900. The fastest speed to date was on bare metal, but on average, the results are the same between the two.

![Test Image 17](Images/Cinebench20.jpg)



## B. Usage
 
- To build OpenCore using Pavo's OCBuilder, it is recommended to use the Release version with or without kexts update.
- Update the repository EFI folder with part of the folder created by OCBuilder. Do not directly copy the ACPI, Driver, Kexts or Resources folder. Instead, select from within those folders the ideas you wish to update. Note that within the Resources folder is an Audio folder. Do not update this as it contains ~90MB of audio files which we're not using.
- Verify that the proper driver efi files are in place, based on what is indicated within the __config.plist__ file.
- NOTE: the __config.plist__ file does not contain SNs or UUIDs but place-holders that say "FILL-IN". You must supply these 
        values on your own (see section A8 above for details).
- Again, editing of __config.plist__ files should only be done with PlistEdit Pro or [ProperTree](https://github.com/corpnewt/ProperTree).
- Remember, the EFI folder, containing the Boot and OC folders, goes onto the EFI partition of your boot drive. 
        Don't make the mistake of placing the Boot and OC folders directly onto the EFI partition: this won't boot.
                        

## E. Discussion

- [OpenCore Discussion](https://www.insanelymac.com/forum/topic/338516-opencore-discussion/?page=1) for general OC issues
- [TRX40 Bare Metal Discussion](https://www.macos86.it/topic/3307-trx40-bare-metal-vanilla-patches-yes-it-worksbutproxmox-is-better/#comments) for general TRX40 bare metal issues (started by fabiosun)

### Credits

- [AlGrey](https://github.com/AlGreyy) for the idea and creation of the AMD [patches](https://github.com/AMD-OSX/AMD_Vanilla/tree/opencore)
- [Original TRX40](https://www.macos86.it/topic/2675-proxmox-ve-osx-guide-discussion/) VM started by fabiosun
- [Hackintool](https://www.insanelymac.com/forum/topic/335018-hackintool-v286/) for Hackintool utility
- [Kext Updater](https://bitbucket.org/profdrluigi/kextupdater/downloads/) for Kext Updater utility
- [Dortania's OC Guide](https://dortania.github.io/OpenCore-Install-Guide/) OC Install and Guild Book
- [Pavo](https://github.com/Pavo-IM) for OCBuilder and AGPMInjector
- [CorpNewt](https://github.com/corpnewt) for many things such as GenSMBIOS and ProperTree editor
- [trulyspinach](https://github.com/trulyspinach/SMCAMDProcessor) for CPU Temp/Freq monitoring
- [vit9696](https://github.com/vit9696) for OpenCore and many of the kexts we use
