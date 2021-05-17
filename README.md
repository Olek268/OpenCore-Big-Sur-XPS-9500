
# <div align='center'>Dell XPS 9500 OpenCore Big Sur </div>
\
![alt text](https://dl3.pushbulletusercontent.com/bjN7hX0e0c8yNVxAdMnfm04sLIcgzMoC/Screenshot%202021-05-17%20at%2017.33.25.png)
\
This is an OpenCore documentation and guide containing the EFI folder for installing and multibooting macOS 11.x Big Sur on your Dell XPS 9500 (2020).</br>
<b>Note:</b> Check if your M.2 Drive is compatible with macOS ([NVMeFix.kext](https://github.com/acidanthera/NVMeFix) not tested!)

## <div align='center'> Technical Specifications </div>

<div align='center'><b><u>OpenCore Version</b> </div>
 <div align='center'>0.7.0 </div>
 <div align='center'><b><u>macOS Version</b> </div>
 <div align='center'> Big Sur 11.3.1 </div>
 <div align='center'> <b><u>Bios Version</b> </div>
 <div align='center'>1.7.1 </div>
<div align='center'> <b><u>Processor</b> </div>
<div align='center'>i7-10750H Comet Lake </div> 
<div align='center'> <b><u>Integrated Graphics</b> </div>
<div align='center'> Intel UHD Graphics 630 </div>
<div align='center'> <b><u>Discrete Graphics</b> </div>
<div align='center'> NVIDIA Geforce 1650 Ti </div>
<div align='center'> <b><u>Storage Device</b> </div>
<div align='center'> M.2 Micron 2300 NVMe 1024GB </div>
<div align='center'> <b><u>Audio Card</b> </div>
<div align='center'>Realtek ALC3281-CG (rebranded ALC289) </div>
<div align='center'> <b><u>SD Card Reader</b> </div>
<div align='center'> Realtek RTS5260 </div>



## <div align='center'>Functional Status</div> 

|<b>Hardware/Function</b>|<b>Status</b>|
|-|-|
|CPU Power Management|Working -> 800MHz at idle - boosts to max Turbo frequency|
|iGPU UHD630|Working -> 350MHz at idle - 2048MB
|Keyboard|Working|
|Touchscreen| Working|
|Trackpad|Working|
|Headphones Jack|Working|
|Built-in Speakers|Working|
|Built-in Microphone|Working|
|Built-in Wifi|[Working](https://github.com/OpenIntelWireless/itlwm)|
|Built-in Bluetooth|[Working](https://github.com/OpenIntelWireless/IntelBluetoothFirmware)|
|Built-in Webcam|Working|
|Hotkeys|Working -> Brightness - fn+S/fn+B|
|USB 3.x|Working|
|USB 2.0|Working|
|Fingerprint Reader|[Not working](https://github.com/al3xtjames/NoTouchID)|
|SD Card Reader|[Not working](https://github.com/cholonam/Sinetek-rtsx/issues/17)|
|Sleep|[Not working](https://www.dell.com/community/XPS/XPS-9500-How-can-S3-sleep-not-be-working/td-p/7754559)|
|iServices|[Working](https://forums.unraid.net/topic/100416-big-sir-macinabox-icloud-works-but-imessage-error/)
|DualBoot/MultiBoot | Working|

## <div align='center'> Bios Settings </div>
<b>Note:</b> The states i starred* are needed just for the installation process and can be turned off after, the <b>bold</b> ones are mandatory throughout.
|<ins>Tab</ins> - Option|State|
|-|-|
|<ins>Boot Configuration</ins>|
|- Secure boot| <b>Off</b> -> <i>Audit Mode*</i>
|<ins>Integrated Devices</ins>|
| - Enable Thunderbolt Boot Support| On*
| - Enable Thunderbolt pre-boot modules| On*
| - Thunderbolt Security level| No Security*
| - Enable USB Boot Support| :ballot_box_with_check:* 
| - Enable Fingerprint Reader Device | Off -> <i>Windows Hello compatible
|<ins>Power</ins>|
| - Block Sleep | <b>On</b>
|<ins>Storage</ins>|
| - Enable MediaCard | On -> <i>for usage in windows/linux</i>
|<ins>Security</ins>||
| - TPM 2.0 Security | <b>Off</b> |
| - Intel Software Guard Extensions | Disabled*
| - SMM Security Mitigation | Off
| - UEFI Boot Path Security| <b>Never</b>
|<ins>Virtualization</ins>|| 
| - VT for Direct I/O | On ->  ``DisableIOMapper > True``|
|<ins>Performance</ins>| -> <i><ins>can</ins> be turned on after installation|
|- Intel SpeedStep | On
|- C-States Control | On
|- Intel Turbo Boost Technology | On
|- Intel Hyper Threading Technology | On

## Instructions 

 ### Create USB Installer
Follow [this](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html#downloading-macos) guide to create the installer using windows, if you have an apple computer available use [this](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html#downloading-macos-modern-os) one instead and here is [the one](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html) for linux.</b>
Now insert your bootable USB drive and start your XPS, enter BIOS and set the USB drive as first in the boot order, save settings and restart, you should be greeted by the OpenCanopy GUI but before we continue please complete the following steps:

1. #### Disable CFG Lock (via modGRUBShell.efi)

Select the modGRUBShell option at the OpenCore boot selection page.</br>
<b>Note:</b> tools are disabled on this plist. To activate tools at startup, set `Misc > Boot > HideAuxiliary = NO`.</br>
At the grub prompt, enter the following command lines: </br>
```
setup_var CpuSetup 0x3e 0x0
setup_var CpuSetup 0xda 0x0
exit
```
Reboot system and run VerifyMSR2.efi, look at the bottom for this line of code:  </br>
`````
This firmware has UNLOCKED MSR 0xE2 register!</b>
`````
If it states  ``LOCKED``  instead of  ``UNLOCKED``  please boot into BIOS and do a factory reset, then run VerifyMSR2 again.</br>
In case of BIOS update check with VerifyMSR2 and repeat steps if necessary. 


2. #### Adjust your PlatformInfo, Serial Number, MLB, and UUID to [MacBookPro16,4](https://everymac.com/ultimate-mac-lookup/?search_keywords=MacBookPro16,4) using [GenSMBios](https://github.com/corpnewt/GenSMBIOS) [Windows], <a name=OC></a>[OpenCore Configurator](https://mackie100projects.altervista.org/download-opencore-configurator/) [macOS] or [The Terminal](https://github.com/sickcodes/osx-serial-generator) [Linux].
<b>Note: </b> I removed the ones im using from the config.plist for obvious reasons, please add your own using the links above or add them manually using the [config.plist](https://dortania.github.io/OpenCore-Install-Guide/config.plist/#creating-your-config-plist) guide.

## Dualboot/Multiboot

The Config.plist is multiboot ready, OpenCore can install and boot Windows Boot Manager & GNU Grub directly from the same EFI partition on the same drive using the SSDTs with an ``if (_OSI("Darwin"))`` argument and the SSDT-XOSI updated to include latest Windows versions.</b>
\
<b>Note:</b> Update operating systems at your own risk! I recommend using the latest installers downloaded from the official manufacturer websites with all security updates preinstalled and disabling automatic updates.</b>
\
Depending on your config you might have to add boot entries in ``Misc > Entries > "Item 0"``. (I recommend using OpenCore Configurator for this) and remove BIOS Boot entries using OpenShell.</b>
You will have to find your PciRoot path and insert it into ``Misc > Entries``.

### PciRoot paths and BIOS Boot entries:

<b>Note:</b> Do this after installing all your OSs as you need to see which paths or entries you need to add/remove. I recommend installing macOS first as it automatically creates a 200MB EFI partition.(Don't forget to partition and format the Windows and/or Linux partitions using Diskutil to <b>FAT32</b> and macOS partition to <b>APFS at install</b>!) 

#### <u><b>Insert PciRoot path</b>

Boot into OpenCore and choose Openshell.efi, you will be greeted by a list FS0:, FS1:, FS#,.. these are partitions of all the drives connected to the system, now take each one and search for the EFI partition that you created with your installer using these commands:
```
FS#:
DIR
```
it will list all the folders inside that partition, now look for the blue EFI folder and note the ``FS#:`` partition number where your EFI is located and type:</b>
```
map > pciroot-map.text
reset
```
this command will create a text file containing all the FS# PciRoot paths in the EFI partition, open it and look for your FS# number and copy the path, it should look something like this:</b> 
```
PciRoot(0x0)/Pci(0x17,0x0)/Sata(0x4,0xFFFF,0x0)/HD(2,GPT,BDD47B32-75D8-4A58-A59A-F96037E62CED,0x109000,0x32000)
```
now you have to add the folder path of the <b>.efi</b> boot file at the end of the PciRoot path.</b>

<b>Example:</b>
```
PciRoot(0x0)/Pci(0x17,0x0)/Sata(0x4,0xFFFF,0x0)/HD(2,GPT,BDD47B32-75D8-4A58-A59A-F96037E62CED,0x109000,0x32000) /\EFI\Microsoft\Boot\bootmgfw.efi
```
<b>Note:</b> <i>This is just an example for <u>Windows Boot Manager</u></i>, now you need to add it to the ``Misc > Entries``  in OpenCore Configurator using the (+) symbol, click enable, save and reboot your system.

#### <u><b>Remove BIOS Boot entry</b></u>

Choose Openshell.efi again, at the grub prompt type the following command:</b>
```
bcfg boot dumb
```
You should have a list of boot options, now look for the ``Option: ##`` that you want removed from the boot picker, to delete type the following command line:</b>
<b>Note:</b> This will <b>permanently</b> delete the <b>BIOS</b> Boot entry!</b>
```
bcfg boot rm ##
reset
```
Now your OpenCore bootloader shows only the desired entries and you can keep ``Misc > Boot > PickerMode > External`` for a nice GUI interface at boot using OpenCanopy.

### <div align='center'><ins>Bootloader Interface(GUI)</ins></div>

With [OpenCanopy.efi](https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html) being integrated into [OpenCorePKG](https://github.com/acidanthera/OpenCorePkg/releases) i took the time and designed my "own" GUI using this [Set 3 LightGlow Icon Pack](https://github.com/blackosx/OpenCanopyIcons/blob/master/Set3/LightGlow/preview_sheet_LightGlow.jpg) and [OpenCanopy Generator](https://github.com/chris1111/OpenCanopy-Generator).</br>
\
\
![alt text](https://dl3.pushbulletusercontent.com/WJ10Atsfai2gmUUoOdMFnTUOa4WUyx34/IMG_1205.jpg)

## ACPI

The compiled SSDTs and .xml files were put together by using the already available [repositorys](https://github.com/search?q=XPS+9500) here on Github. The only one i personally changed was the XOSI.xml to include the latest windows versions. As of now system is fully functional with the ones included here and i have been running it as a daily driver without having any problems whatsoever.

## Kext 

For AppleALC.kext use layout id [99](https://github.com/leon0410898/XPS13-9300-hackintosh/issues/19) in your config.plist under:</b>
 ``DeviceProperties > Add > PciRoot(0x0)/Pci(0x1F,0x3) > layout-id > 99``
 and remove ``alcid=xxx`` under ``boot-args`` as the XPS 9300 uses the same rebranded Realtek ALC3281-CG.</br>
 [VoltageShift.kext](https://github.com/sicreative/VoltageShift)  comes preinstalled in the EFI, use kext to manually undervolt your system.([XCode12](https://developer.apple.com/xcode/) required)</b>
<b>Note: </b>Don't update [VoodooI2C.kext](https://github.com/VoodooI2C/VoodooI2C) past version 2.2.4 as newer releases break the Touchpad and Touchscreen also i added the [Synetek-rtsx](https://github.com/cholonam/Sinetek-rtsx/) and [NVMefix](https://github.com/acidanthera/NVMeFix) kexts to the ``EFI\OC\Kext`` folder but not to the config.plist entry as they might prove useful in the future.</br> 
The other kexts have been updated to the latest version as of 05/2021.

## Final Words

If everything checks out, you should have an up and running multiboot capable Hackintosh!</br>
<b>Note:</b> Please read everything carefully and you shouldn't have anything to troubleshoot but if you do please start searching here first: [Google](https://www.google.com/), [Tonymacx86](https://www.tonymacx86.com/), [InsanelyMac](https://www.insanelymac.com/), [Reddit](https://www.reddit.com/r/hackintosh/) and the [OpenCore configuration pdf](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf).</br>
If you didn't find anything helpful on the forums please create an issue on this repository or contact me directly.</br>
Thank you for reading!

## Credits

[Dortania](https://dortania.github.io)</br>
[zachs78](https://github.com/zachs78/MacOS-XPS-9500-OpenCore)</br>
[TheFireDragon](https://github.com/thefiredragon/MacOS-XPS-9500-4K-OpenCore)</br>
[Billthan](https://github.com/billthan/XPS9500-MacOS-BigSur-OC)</br>
[Hieplpvip](https://github.com/hieplpvip/XPS9500-Hackintosh)</br>
[blackosx](https://github.com/blackosx/OpenCanopyIcons)</br>
[custom90gt](http://forum.notebookreview.com/threads/enable-undervolting-on-your-dell-xps-9500-9700-or-others.835235/)</br>
[Ayushere](https://medium.com/macoclock/guide-multiboot-dualboot-opencore-with-windows-macos-linux-kextcache-131e96784c3f)</br>
[Superdean56](https://forums.unraid.net/profile/96329-superdean56/)
