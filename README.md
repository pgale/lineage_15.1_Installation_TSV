# **Lenovo ThinkSmart View flashing process guide for Lineage 15.1** #

_Collated by @pgale_

![One of my ThinkSmart View devices running an HA dashboard via Fully Kiosk Browser](/IMG_0479.JPG)

> [!NOTE]
> I'm not a developer but someone who is passionate about home automation, Home Assistant and the amazing Lenovo ThinkSmart View devices. I now have seven of these running as my main display panels around my house. The super low-cost makes this affordable and results in a fantastic experience.
> Please do post in [discussions](https://github.com/pgale/lineage_15.1_Installation_TSV/discussions) if you have anything to add or have noticed any errors or omissions in this guide and I'll add them in for the benefit of others.
> I posted on the HA forum about my devices and the dashboards I run (a little old now as my design has moved on, but it will give you an idea of one way to create a dashboard and device to show the information you need to see.) Have a look at that post [here](https://community.home-assistant.io/t/my-take-on-a-home-dashboard-running-on-the-lenovo-thinksmart-view-and-managing-multiple-devices-running-fully-kiosk/718355).
>
> Original posts on the installation of various OS's (Android and Linux) can be found here:
> 
> [Mattmon's original Android 8.1 build](https://community.home-assistant.io/t/is-this-the-perfect-standalone-tablet-for-ha/658422)
> 
> [FelixKa's posmarketOS (PMOS) build (Alpine Linux)](https://community.home-assistant.io/t/lenovo-thinksmart-view-rom-os-development/676324)
>
> [Dinki's View Assist project (in beta)](https://github.com/dinki/View-Assist)
>
> Warning - these posts and discussions are VERY long!

> [!CAUTION]
> These instructions and the flashing process does require some technical knowledge and skill. NO warranty or support is provided. If you buy a ThinkSmart View and can't get it flashed for whatever reason, that is down to you. These instructions are provided as a collation of many resources and writings across various forums and Discord servers to save you time and save you reading many hundereds of posts.
>
> Lineage 15.1 is still based on core Android 8.1 but has a new custom recovery environment that allows sideloading directly. The open source LineageOS builds on Android (it isn’t just a skinned version of Android!). @Deadman’s build of Lineage for the TSV has carefully picked drivers and other components that are tested to work with this device and some other custom code to make it work better.
>Previously, I have tried and run (for quite some time) Mattmon’s 8.1 ROM, Android 11 with Google Go Apps and postmarketOS (PMOS) which is Alpine Linux based.
>All the other options had issues that caused me problems. Lineage OS 15.1 fixes audio not working at all through a browser, weird touchscreen timeouts and problems crashing when booting (A11) if another app is set as the launcher.
>So for me, it’s stable and just works with everything I’ve tried so far.
>
> Whilst I haven't encountered issues, this doesn’t mean that something breaking won’t be found in some odd situation or with other apps I've not tried. The device is still a hacked turnkey MS Teams device that has been built on by using a userdebug image that was leaked from Lenovo. There is **NO SUPPORT** whatsoever from Lenovo or anyone else. This is a discontinued device because it didn't do well as a MS Teams turn-key solution. The Lineage build is UNOFFICIAL so don’t expect any updates apart from anything that @deadman and the other devs working with him may or may not release out of the goodness of their hearts (if they even are able to fix something that crops up). That being said, this retailed for around $350 new and has a great screen, build quality and a good speaker. Getting one for $20-40, shrink-wrapped and brand new in a box is an amazing deal and they work really well as HA dashboards.
>They do only have 2GB DDR3 RAM though and that shows with more complex, heavy web pages or web apps which can be slow to load. For HA dashboards, and even running a camera feed or two, they work really well, reliably and are reasonably quick for most stuff. Having run five of these for a few months and having tried all the options, I'm still in love with them and Lineage 15.1 wins as the OS!


### Credit where credit is due ###
Credit to @deadman96385 and @electimon on Discord for making this happen and putting in a tonne of work, @mattmon who produced the original 8.1 install and instructions. These instructions build on what they have written. Thanks also to @mngarchow for the detailed instructions that are reproduced in part here too. Thanks to @ADHDSquir for the Linux instructions below.


## Introduction ##
This is the stable, unofficial build of Lineage 15.1 for the amazing Lenovo ThinkSmart View (CD-18781Y), based on Android 8.1. I've found no issues so far that have plagued me on other Android 8.1 or 11 builds for running Home Assistant dashboards with working audio and voice assist. This is by far the best OS I've tried and I recommend this over the other options. There is also a postmarketOS build based on Alpine Linux [here](https://community.home-assistant.io/t/lenovo-thinksmart-view-rom-os-development/676324) but for ViewAssist and general HA use, in my opinion, Android is the easier option to get running and the most usable, particularly if you rely on Fully Kiosk Browser and any other Android apps.
 
You will need a Windows PC or Linux build to run EDL/QFIL and ADB commands. Due to the difficulties of running these tools on Windows and USB faffs, I prefer to use a Raspberry Pi4 running Debian. If you are comfortable with Linux, I would recommend that route.

 
## Files and installation Prerequisites - WINDOWS ##

> [!WARNING]
>**WARNING: It is very common to encounter issues when trying to flash which are due to USB cables/ports. These failures show up as Sahara errors in the QFIL tool on Windows. Make sure to use a USB 2.0 port, black not blue, and you must use a USB 2.0 hub/bus in windows device manager too. If, when you start flashing, it seems to pause for around a minute and then returns a Sahara error, it is very likely you need to change your USB port and/or cable. Many very modern PC's have USB 3.1 and above and in my experience, these cause a lot of issues. If you have an old PC with USB 2 ports, that might be your best bet.**
 
1.	Download the [200628.084 Teams & Others](https://xdaforums.com/t/cd-18781y-lenovo-thinksmart-view-bootloader-firmware-zoom-teams-conversion-normal-android.4426029/) firmware provided on the XDA Forum thread (At the bottom of the first post under Downloads) 
2.	Create a directory on your desktop called ThinkSmartView.
3.	Extract the 'image' folder from the downloaded zip file to the ThinkSmartView folder. You will then have the folder structure /ThinkSmartView/image on your desktop with all the files in the image folder.
4.	Download the [Lineage file](https://drive.google.com/file/d/1m34JjwxukdH8Y5WEUCZuPSiyrQHrI6ke/view?usp=sharing)
5.	Download the [Lineage recovery file](https://s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img)
6.	Download the [OpenGapps package](https://sourceforge.net/projects/opengapps/files/arm/20220215/open_gapps-arm-8.1-pico-20220215.zip/download) (for google play store) 
7.	DO NOT Extract these files but copy them as zip/img files to the ThinkSmartView folder on your desktop
8.	Download the programs and tools to do the flashing: [here](https://bit.ly/3HOIr1g) or [here](https://mega.nz/file/EBQ3VSxK#X4DJmfPoFFEAxe8DxIqAIKQershp0Z_dVq-ZXFQ3Vyg)
9.	From this file, you will only need to install the following (ignore the other files):
10.	Install the Qualcomm Drivers

      a.	Double click the file QDLoader HS-USB Driver_64bit_Setup.exe

      b.	Yes to the User Account Control prompt

   	  c.	Choose WWAN-DHCP is not used to get IPAddress
 
      d.	Next

   	  e.	Accept license, Next

   	  f.	Install

   	  g.	Finish

   	  h.	Restart PC when prompted at the end.
11.	Install QPST 

      a.	Open the QPST 2.7.496 folder
      
      b.	Double click the file QPST.2.7.496.1.exe
      
      c.	Yes to the User Account Control prompt
      
      d.	If prompted, install the VC++ 2013 Redistributable by clicking Install
      
      e.	When prompted, click Next
      
      f.	Click I accept, then Next
      
      g.	Click Next two more times. Then click Install
      
      h.	Click Finish when done.
12.	Install ABD & Fastboot ++ 

      a.	Double click the file ADB-and-Fastboot++_v1.0.8.exe
     
      b.	Yes to the User Account Control prompt
     
      c.	Click I accept, then Next
     
      d.	Click Next again.
     
      e.	Leave the checkboxes for desktop shortcuts and Add to System Path options, then click Next.
     
      f.	Click Install.
      
      g.	Uncheck Open the Toolkit and Launch ADB & Fastboot++ options, click Finish.
13.	After installation, Windows should also be in Test Mode which can be seen in the lower left of the desktop.


## Flashing the image - WINDOWS ##
 
To start from a known state, flash the '200628.084 Teams & Others' firmware/OS to the device. Starting from other images can cause problems, particularly if you've experimented with other images, so it's worth taking this extra step.
 
1.	Open the QFIL tool (click Start then search for QFIL)

2.	Under Select Build Type, pick Flat Build

3.	Under Select Programmer, browse to the /ThinkSmartView/image folder on your desktop and select: prog_emmc_firehose_8953_ddr.mbn

4.	Under Select Flat Build, click the Load XML button.

5.	Navigate to the file rawprogram_unsparse.xml and click Open

6.	Another file prompt window will open, select patch0.xml and click Open

7.	Make sure the device is switched off at the plug. Plug the USB-C cable into the computer and the device. The USB-C port is under a rubber plug/foot on the bottom of the screen when in landscape orientation. Pull that out, it's just held in place with a little rubber adhesive. (Turning the power off/on at the plug can be easier than pulling out the power connector from the device which is fairly stiff, especially when holding the volume buttons down as in the next steps.)

8.	Hold down both volume buttons and then switch on the power to the device. You may hear windows make a sound to indicate it is connected (if system sounds are on). Let go of volume buttons when you hear the sound or after 5 seconds. The device is now in EDL mode (Emergency Download Mode). The screen will be black.

9.	In QFIL, click on Select Port.

10.	Select the Qualcomm device and click OK. (COM port number will be different depending on what port you have connected the device to.)

11.	Click the Download button and wait while the flasher downloads to the device. It will 'Download Succeed' when completed successfully.

12.	If the status pauses for around a minute and then you get an error, referencing Sahara, you will need to find an alternative USB 2.0 port as mentioned above or try a different USB cable.

13.	In QFIL, Make sure the programmer and rawprogram and patch are still selected (they should be).

14.	Switch OFF your device, wait 10 seconds and then while holding the vol+ and vol- buttons, switch it back on. QFIL will show it has lost the USB connection to the device and then reappear again.

15.	Select the Tools menu and then Partition Manager.

16.	Click OK.

17.	If all is well, you will see a list of partitions. Find recovery in the list and right click it then select "Manage Partition Data"

18.	Select Load image and navigate to your downloaded lineage recovery lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img

19.	The flash should finish within 5-10 seconds. Once done click close and then close again but wait before hitting the final ok.

20.	Hold Vol+ (One near mic mute switch) while you press the ok button to automatically boot into recovery. If you miss this, simply power cycle the device while holding Vol+ and it will boot into Lineage Recovery.
> [!NOTE]
> If you are on a stock rom it will overwrite your recovery if you boot up without holding vol+ so you will need to reflash the recovery partition again. 

21.	The touchscreen does not work in recovery. Navigation in this menu is performed with the vol+ and vol- buttons to move around and the camera privacy slider to activate your choice. Note that you only need to move the camera privacy slider in one direction for it to select the choice - don't push it forward and back in one go as you'll end up selecting something you didn't intend to.

22.	Go to "Factory reset" and then select "Wipe Data / Factory Reset". Select yes and the data and cache partitions will be wiped and formatted.

23.	Go back and select "Apply update" then "Apply from adb". When ADB Sideload is shown in red, the device is ready.

24.	Close QFIL and run ADB and Fastboot++. There should be an icon on your desktop.

25.	To check that your device is detected, type in (press enter after each):

```
adb devices
```

If the device is found, it will show something like HUAOBJNM sideload.
26.	Navigate to the ThinkSmartView folder. For example:

```
cd %USERPROFILE%\Desktop\ThinkSmartView
```

Now run these commands: 
 
```
adb sideload lineage-15.1-20240602-UNOFFICIAL-starfire.zip
adb sideload open_gapps-arm-8.1-pico-20220215.zip
```

> [!NOTE]
> You will need to re-select ADB Sideload after each one. The connection times out quite quickly so you will need to select the ADB Sideload just before you send the command. If it's working, you will see a progress update on both the command line and on the device. Don't worry if it sits at 47% for up to a minute or so, it's still working.

27. After Lenovo splash screen, LineageOS boot animation will play for up to 5 minutes as the OS sets up.

28. LineageOS may display the error **Speech Services by Google has stopped**. This is common and not a problem.

29. Proceed to [Setting up Android for HA use](#setting-up-android-for-ha-use)
 
  
## Files and installation Prerequisites - LINUX ##

1. Install EDL as described here: https://github.com/bkerler/edl

2. In your home dir, create a folder named ThinkSmartView

    `mkdir ThinkSmartView`
3. Download the [200628.084 Teams & Others](https://xdaforums.com/t/cd-18781y-lenovo-thinksmart-view-bootloader-firmware-zoom-teams-conversion-normal-android.4426029/) firmware provided on the XDA Forum thread (At the bottom of the first post under Downloads)

    `wget https://s3.wasabisys.com/filestash-buk/lenovo-thinksmart-view/CD-18781Y.200628.084.zip`

4. Extract the 'image' folder from the downloaded zip file to the ThinkSmartView folder. You will then have the folder structure /ThinkSmartView/image on your desktop with all the files in the image folder.

    `unzip -j ~/CD-18781Y.200628.084.zip "CD-18781Y.200628.084/image/*" -d ~/ThinkSmartView/image`

5. Download the [Lineage recovery file](https://s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img)

    `wget https://s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img`
6. Download the [Lineage file](https://drive.google.com/file/d/1m34JjwxukdH8Y5WEUCZuPSiyrQHrI6ke/view?usp=sharing) to ~/ThinkSmartView
7. Download the [OpenGapps package](https://sourceforge.net/projects/opengapps/files/arm/20220215/open_gapps-arm-8.1-pico-20220215.zip/download) (for google play store) to ~/ThinkSmartView
8. DO NOT Extract these files. Leave them as zip/img files in your ThinkSmartView folder. Your file/folder structure should look like this:

   ``` sh
   /home/pi
   └── ThinkSmartView
       ├── image
       │   ├── adspso.bin
       │   ├── boot.img
       │   ├── [108 other files]
       │   ├── zeros_1sector.bin
       │   └── zeros_33sectors.bin
       ├── lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img
       ├── lineage-15.1-20240602-UNOFFICIAL-starfire.zip
       └── open_gapps-arm-8.1-pico-20220215.zip
   ```


## Flashing the image - LINUX ##

> [!NOTE]
> These expanded Linux install instructions are based on two successful flashes using a Pi 5. One using a TSV with 8.1 already installed and functioning, the other using a fresh device, right out of the box booted into EDL the very first time it was powered on.

1. Connect USB cable to a **USB2** (not blue) port and the USB-C port on the bottom of the TSV.

2. Start EDL

    `~/edl/edl qfil ~/ThinkSmartView/image/rawprogram_unsparse.xml ~/ThinkSmartView/image/patch0.xml ~/ThinkSmartView/image --loader=/home/pi/ThinkSmartView/image/prog_emmc_firehose_8953_ddr.mbn`

3. Boot the device into EDL by holding both vol keys and plugging in power.
4. Release buttons once EDL starts writing to device
5. After EDL completes, copy boot files and recovery image to device using the following 3 commands one at a time:

    `edl w aboot ~/ThinkSmartView/image/emmc_appsboot.mbn`

    `edl w abootbak ~/ThinkSmartView/image/emmc_appsboot.mbn`

    `edl w recovery ~/ThinkSmartView/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img`

6. Boot TSV into recovery by removing power, holding Vol+, and powering back on
7. When Lenovo splash screen appears Release vol+ button
8. LineageOS recovery screen should appear
9. Use Vol+/- buttons to highlight **Apply update**; Slide camera shutter to select
10. Slide camera shutter again to select **Apply from ADB** (default selection); **ADB Sideload** is displayed
11. Sideload LineageOS image from the connected computer:
    `adb sideload ~/ThinkSmartView/lineage-15.1-20240602-UNOFFICIAL-starfire.zip`

> [!NOTE]
> If you get the error `adb: sideload connection failed: no devices/emulators found` the connection has likely timed out. Reboot the device into recovery again starting at step 6.
>
> It is common to see the error `E1001: Failed to update system image.` on the first attempt to sideload the image.  Reboot the device into recovery again starting at step 6.

12. Sideload OpenGapps package. Follow steps 9 and 10 above to place TSV in ADB Sideload, then:

    `adb sideload ~/ThinkSmartView/open_gapps-arm-8.1-pico-20220215.zip`

> [!NOTE]
> You will need to re-select ADB Sideload after each one. The connection times out quite quickly so you will need to select the ADB Sideload just before you send the command. If it's working, you will see a progress update on both the command line and on the device. Don't worry if it sits at 47% for up to a minute or so, it's still working.

13. Disconnect USB cable
14. Use Vol+/- buttons to select **Factory reset**; Slide camera shutter to select
15. Slide camera shutter again to select **Wipe data / factory reset** (default selection)
16. Use Vol+/- buttons to select **Yes**; Slide camera shutter to select
17. After reset completes, use Vol+/- buttons to select **back arrow**; Slide camera shutter to select
18. Use Vol+/- buttons to select **Reboot**; Slide camera shutter to select
19. After Lenovo splash screen, LineageOS boot animation will play for up to 5 minutes as the OS sets up.
20. LineageOS may display the error **Speech Services by Google has stopped**. This is common and not a problem.
21. Proceed to [Setting up Android for HA use](#setting-up-android-for-ha-use)


## Setting up Android for HA use ##
 
These setting are what I use on my devices. Feel free to modify as desired.
 
1.	On first boot, run through the setup process, select your Wi-Fi and set up as a new device.
2.	If you installed the OpenGapps, you will have a chance to sign in to Google.
3.	Protect your phone - I set Not Now, skip anyway.
4.	In Google Services, I deselect everything.
5.	Go to settings and set:

    Display:

        Brightness level - 100%
      	Adaptive brightness - OFF
        LiveDisplay - Display Mode - OFF 
        Style - Style – Dark

    Advanced:

        Sleep - 30 Minutes
        Rotation settings - Auto-rotate screen toggle off/on (overcomes a possible bug)
        Tap to Sleep - OFF

    Sound:
  
        Set initial volumes as required

  	Security & Privacy:

  	     Screen lock - none

    System:

        Languages & input - check keyboard is set for your region
        Date & time - set automatic and time zone if not already set
        Hardware Switch Settings - disable both Camera Block Switch settings (your preference)
        About Phone - Tap Build Number until it shows you are a developer
        Status Bar - System icons - Battery - Don't show this icon (There's no battery so don't show this)
  	    (disable anything else to your liking)
        Buttons - Volume buttons wake device - ON  
        Developer options:
           Root access - ADB only
           Android debugging - ON
6.	Install apps from the google app store (as desired).
7.	Arrange your icons as you like. By default, there are apps installed on the second page. I just drag everything across to the first page. Long tap and drag anything to 'remove' that you don't want on the desktop. As this won't be visible with Fully Kiosk running, it doesn't matter much but makes it a little quicker to navigate.
8.	Install a newer WebView if needed (with the benefit of potential performance gains) [Instructions below](#installing-a-newer-webview)
9.	Optional - turn off the annoying keyboard autocorrect (if you are typing in an email address and it autocorrects to some random words!) Settings - system - Languages & input - virtual keyboard - Android keyboard - Text correction - auto-correction - OFF

    
## Installing a newer WebView ##

At the time of writing, Lineage 15.1 ships with Android System WebView 100.0.4896.127. You can install a newer version if you like but you need to ensure it's compatible with Android 8.1. I've tried Android System WebView 125.0.6422.165 (arm64-v8a + arm-v7a) (Android 8.0+) which seems to work ok. In basic, non-scientific testing, loading a fairly complex web page app side-by-side, the newer version performed slightly faster. The mealie web app, running within an HA dashboard as a popup and loading the home page with recipe images, was around four seconds faster than the old WebView version. I swapped the WebView version over on both devices and saw the same performance increase, showing it wasn't some other setting affecting performance.

If you want to install a particular version from apkmirror, follow these instructions. You can also search for WebView in the Google Play Store (as reported by other users) although it never showed up for me. Apparently you can also install Google Chrome which may install a newer WebView but I've not tried this as I don't need Chrome on my devices.

# Method 1 - Easy #
1. On the device itself, download the Android System WebView. I tried [125.0.6422.165](https://www.apkmirror.com/apk/google-inc/android-system-webview/android-system-webview-125-0-6422-165-release/android-system-webview-125-0-6422-165-2-android-apk-download). Other versions can be found on that site but make sure it supports Android 8.1.
2. Open the files app.
3. Find where you downloaded the file to - possible downloads.
4. Tap the file to install it and follow the prompts.
5. On the ThinkSmart View, open settings - system - Developer options - Webview implementation.
6. You should see both the original and the new WebView versions.
7. Select the new WebView.
8. (optional) If using Fully Kiosk Browser, you can also check what version of WebView you are using by going to settings - advanced web settings - select webview implementation (at the bottom)

# Method 2 - ADB Install #
1. Download the Android System WebView. I tried [125.0.6422.165](https://www.apkmirror.com/apk/google-inc/android-system-webview/android-system-webview-125-0-6422-165-release/android-system-webview-125-0-6422-165-2-android-apk-download). Other versions can be found on that site but make sure it supports Android 8.1.
2. Copy the file to a folder on your Linux or Windows machine that has ADB installed.
3. WINDOWS - Run the ADB & Fastboot++ shortcut (installed as per the instructions above)
4. Install the WebView:

```
adb install com.google.android.webview_125.0.6422.165-642216501_minAPI26_maxAPI28(arm64-v8a,armeabi-v7a)(nodpi)_apkmirror.com.apk
```
5. On the ThinkSmart View, open settings - system - Developer options - Webview implementation.
6. You should see both the original and the new WebView versions.
7. Select the new WebView.
8. (optional) If using Fully Kiosk Browser, you can also check what version of WebView you are using by going to settings - advanced web settings - select webview implementation (at the bottom)

## Troubleshooting and other useful links ##

> [!NOTE]
> I will add to this section over time as I re-flash my existing ThinkSmart View devices to Lineage 15.1. Please do post in [discussions](https://github.com/pgale/lineage_15.1_Installation_TSV/discussions) if you have anything to add or have noticed any errors/omissions in this guide and I'll add them in for the benefit of others.
- Lenovo have a Windows based Rescue and Smart Assistant (RSA). This is good for re-flashing the original firmware in case you get into difficulty. In my experience, it can also suffer from USB issues as discussed in this guide, so you might want to try a USB 2.x port again if it doesn’t work.
The RSA can be found on the Lenovo support site [here](https://support.lenovo.com/us/en/downloads/ds101291-rescue-and-smart-assistant-lmsa)

- It seems that the three-button navigation bar at the bottom doesn't rotate to the bottom of the screen when in landscape orientation. One user also reported that he couldn't get apps to stay in landscape mode. I'll update here if I find out more/a fix.

- From @chewza on Discord: For those with secure boot enabled, they need to temporarily disable that inside windows first otherwise bitlocker asks for their recovery key on the subsequent boot.


## Original Lineage 15.1 release notes from @deadman ##

Bugs: 
Rotation can get stuck sometimes until you toggle auto rotation.

Wifi mac address is the same between devices so if you have multiple you will need to a modification to a file to fix it.

Camera shutter is the power button.

Mic switch is unused but does fire a keycode for 3rd party apps to hook.

OpenGapps Pico gapps package for android 8.1 is the largest you can go without expanding your system partition.

Gapps setup may be a bit unstable until you get fully logged in just keep retrying if it gets stuck with "Getting updates"
https://sourceforge.net/projects/opengapps/files/arm/20220215/ 

Tp change the "randomly" generated mac address seed value (Credit: @endlessbeard):
```
adb root
adb pull /vendor/firmware/wlan/qca_cld/WCNSS_qcom_cfg.ini
edit the file (change the value for Intf0MacAddress) and save
adb remount
adb push WCNSS_qcom_cfg.ini /vendor/firmware/wlan/qca_cld/
adb reboot
```
Lineage zip:
https://s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire.zip 

Lineage Recovery Image:
https://s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img 

Kernel Source:
https://github.com/Starfire-development/android_kernel_lenovo_apq8053 

Device Tree Source:
https://github.com/Starfire-development/android_device_lenovo_starfire 

Vendor Tree Source (Gatekeeper, DRM/SEE, Graphics, Audio helpers (Not calibration) are from Motorola G6 (Ali), everything else is from stock):
https://github.com/Starfire-development/proprietary_vendor_lenovo_starfire 
