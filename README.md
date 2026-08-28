# Coprint Color Upgrade for the Elegoo Orange Storm Giga 3D Printer 

> The purpose of this guide is to provide the steps necessary to add a CoPrint KCM Set color upgrade to your Elegoo Orange Storm Giga 3D Printer

![Finished upgrade overview](images/hero-shot.jpg)
<!-- Swap in a wide "final result" photo here. Keep images in an /images folder next to this file. -->

---

## Table of Contents
- [Overview](#overview)
- [What You'll Need](#what-youll-need)
- [Before You Start](#before-you-start)
- [Step 1: Adjust Z Offset](#adjust-z-offset)
- [Step 2: Enable root access](#enable-root-access)
- [Step 3: Print Mount Hardware](#print-mount-hardware)
- [Step 4: Update Configs](#update-configs)
- [Step 5: First boot](#first-boot)
- [Step 6: Set Z Offset](#set-z-offset)
- [Step 7: Perform Calibrations](#perform-calibrations)
- [Step 8: Orca Profile Usage](#orca-profile-usage)
- [Step 9: Staging Filament](#staging-filament)
- [Configuration Files](#configuration-files)
- [Troubleshooting](#troubleshooting)
- [Credits / Notes](#credits--notes)

---

## Overview

 If you are looking to add a CoPrint KCM Set to your existing Elegoo Orange Storm Giga 3D printer, this should hopefully help.  Using these config files and the device ID's of your coprint hardware, you too can add multicolor capabilities to your printer.   Print some files, upload some files, mount your hardware and be on your way......

## What You'll Need

**Hardware**
- [ ] Original Factory Elegoo Orange Storm Giga
- [ ] CoPrint 4 Color KCM set - Chromahead, KCM, 4 extruder kit


**Software / Files**
- [ ] Firmware version X.X
- [ ] Config files (included in this repo under `/configs`)
- [ ] Slicer Profile (included in this repo under `/orcaprofile`)
- [ ] SSH Client - ex.  Putty

## Before You Start 

> **Important** 
  
 First, before making any hardware/software changes, back up your existing files through the UI.  Turn your machine on, connect to it through the UI via it's IP address, using your favorite browser, and navigate to the Configure section to see a list of files.  From here you can download each one by right clicking and choosing download.  Keep these files safe in case you want to go back to the original factory OEM setup.
       	 
	What I backed up:
	 fluidd.cfg
	 moonraker.conf
	 plr.cfg
	 printer.cfg
	 znp_mcu.cfg
	 

## Step 1: Adjust Z Offset
    
    LCD - Advanced settings?  - >  Offset
    Raise it about an inch from the build plate
	
	This step is essentinal for proper calibration once the Chromahead components are installed.
	If you don't do this step, you risk breaking the hotend when it comes to homing and calibration time, like I did.    
	

![Step 1 photo](images/step-1.jpg)

## Step 2: Enable root access

  Enable root access for your machine using the LCD menu

  This step is required in order to get the username:password to log into your device using SSH. 
  You will need to get the device IDs specific to your coprint hardware, and put the values where instructed in the configs
  
  ???can I issue some gcode to get a list of device ids so I don't have to do this step?


![Step 2 photo](images/step-2.jpg)

## Step 3: Print Mount Hardware

  Mount your kit.
  
  How I have it rigged for now..  4 brackets and 2 zip ties
  
  

![Step 3 photo](images/step-3.jpg)


## Step 4: Update Configs

     
     Power your coprint on
     Power your Giga on
	 
	 It will boot to an error on the LCD but you can still access the device via the UI or SSH
	 
	 
	 SSH into the giga device with username:password from Step 2
	 
	 list device ids  
	     ls -al  /dev/serial/by-id/
		 
     Output:  	 
     
	 root@giga:/# ls -al /dev/serial/by-id
     usb-head_stm32f103xe_48FF6D067265545217210187-if00
	 usb-kcm_stm32f103xe_53FF6A067189564955502487-if00
	 
	 -Change chromahead.cfg and kcm.cfg to your values
	 
	     chromahead.cfg gets "usb-head" device-id
	     kcm.cfg  gets "usb-kcm" device-id
	
	 
	 -Save your config changes
		
	 
![Step 4 photo](images/step-4.jpg)


## Step 5: First boot 

     Going forward, you need both the chromahead installed AND the original giga print head connected but just hanging on the gantry temporarily. 
	 Once the boot is successful, and you see the normal Elegoo Home screen without errors, you can then remove the original print head for the next operations.  
	 
	 NOTE:  You will need to do this every time you power your giga/coprint system up.
            There is probably a way to bypass this in the config but I haven't researched that as it's a minor inconvenience to connect it at boot for now

![Step 5 photo](images/step-5.jpg)

## Step 6: Set Z Offset

  Home the printer and then set the new Z offset for your chromahead

![Step 6 photo](images/step-6.jpg)

## Step 7: Perform Calibrations

  Perform platform measurement, Leveling, Input Shaping

  Use LCD settings to perform these steps
   
  Note: requires the bed_mesh.cfg and input_shaper.cfg files to be included properly in your printer.cfg


![Step 7 photo](images/step-7.jpg)     
  


## Step 8: Orca Profile Usage

    Set the colors in the slicer to match how they are loaded in the CX motors, with the first filament being the one your print starts printing first.  You can verify this in your slicer preview.
	
	Choose number of heat beds - All or Bed 0,  Bed 1, Bed 2, Bed 3
	
	Two provided example profiles:  ALL   - All beds are turned on
	Bed 0 Selection:  Only heats bed 0  - Change this or create your own profiles for what beds you want turned once
	
	
	Note:   When the printer starts your job, only the first bed will heat up first.   After this is complete the rest of your beds will heat up, then the print job will proceed.
	Probably a way to fix this with the gcode macros but this is what I'm doing for now
	
	

![Step 8 photo](images/step-8.jpg)

## Step 9:  Staging your filament


   - BEFORE EACH PRINT -
   
   # See if any filament is detected
   
   Use this console command to check if the sensor has detected any filament from a previous print:
   
    QUERY_FILAMENT_SENSOR SENSOR=filament_sensor
	
   Output:
    
         [Insert example here]	
			
   
   Unload the filament with these console commands:
   
	T0  
	UNLOAD_FILAMENT
	
	CX motor reference: 
	1 -> T0
	2 -> T1
	3 -> T2
	4 -> T3	


   If after doing this and you start a print, and it jams,  more than likely there may be a cut piece in the 8in1 feeder assembly to remove.  
	
   Remove the connector plug, unscrew the feeder, pull the filament piece out, screw feeder back in and connect the plug.

   This cut piece is normally not there after a succesful print but it's worth checking if you're trying to stage filament or print and it's jamming.     
		     
    	
   # Filament Order 
   
   Load your filaments in the CX1 units so they match the color order in your slicer  1, 2 , 3, 4 etc   
   
   "Stage" your filaments running a console command that feeds the filament into the chromahead until it senses it, retracts and parks itself
 
   Do the FIRST filament that will print, LAST. 
   
   Example:  If the first color in your slicer is the first color to print, you would stage your filaments in this order:  2, 3, 4, 1
     
    Command:  

		T1
		LOAD_FILAMENT

	Note: Wait for "AutoLoad Finished" before proceeding to the next command

    	
		T2
		LOAD_FILAMENT

	Note: Wait for "AutoLoad Finished" before proceeding to the next command

    	
		T3
		LOAD_FILAMENT

	Note: Wait for "AutoLoad Finished" before proceeding to the next command
    
	Command:  		

		T0
		LOAD_FILAMENT

	Note: Wait for "AutoLoad Finished" 
	
		

![Step 9 photo](images/step-9.jpg)


....Now you are ready to try a print..


## Configuration Files

| File | Purpose |
|------|---------|
| `configs/printer.cfg` | What it does |
| `configs/kcm.cfg` | Contains KCM device specific config - You make a change here| 
| `configs/chromahead.cfg` | Contains chromahead specific config - You make a change here |
| `configs/bed_mesh.cfg` | Contains bed mesh data |
| `configs/input_shaper.cfg` | Contain input shaper profile data |
| `configs/cp_macro.cfg` | Contains gcode for macros called during printer functions in the slicer or firmware |

## Troubleshooting

**Problem:** Something doesn't work
**Fix:** How to fix it

## Credits / Notes

Any acknowledgements, source links, or license info.