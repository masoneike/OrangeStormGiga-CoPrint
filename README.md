# Coprint Color Upgrade for the Elegoo Orange Storm Giga 3D Printer 

> The purpose of this guide is to provide the steps necessary to add a CoPrint KCM Set color upgrade to your Elegoo Orange Storm Giga 3D Printer

![Finished upgrade overview](images/hero-shot.jpg)
<!-- Swap in a wide "final result" photo here. Keep images in an /images folder next to this file. -->

---

## Table of Contents
- [Overview](#overview)
- [What You'll Need](#what-youll-need)
- [Before You Start](#before-you-start)
- [Step 1: Adjust Z Offset](#step-1-adjust-z-offset)
- [Step 2: Enable root access](#step-2-enable-root-access)
- [Step 3: Print Mount Hardware](#step-3-print-mount-hardware)
- [Step 4: Update Configs](#step-4-update-configs)
- [Step 5: First boot](#step-5-first-boot)
- [Step 6: Set Z Offset](#step-6-set-z-offset)
- [Step 7: Perform Calibrations](#step-7-perform-calibrations)
- [Step 8: Orca Profile Usage](#step-8-orca-profile-usage)
- [Step 9: Staging Filament](#step-9-staging-filament)
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
- [ ] Config files (included in this repo under `/configs`)
- [ ] Slicer Profile (included in this repo under `/orcaprofile`)
- [ ] SSH Client - ex. Putty

## Before You Start 

> **Important** 
  
 Before making any hardware or software changes, back up your existing files through the UI.  Turn your machine on, connect to it through the UI via it's IP address, using your favorite browser, and navigate to the Configure section to see a list of files.  From here you can download each one by right clicking and choosing download.  Keep these files safe in case you want to go back to the original factory OEM setup.
       	 
	What I backed up:
	 fluidd.cfg
	 moonraker.conf
	 plr.cfg
	 printer.cfg
	 znp_mcu.cfg
	 

## Step 1: Adjust Z Offset
  
> **Important** 
  
  While your machine is on..  Adjust the Z offset of the existing extruder in preparation for configuring the new one
    
  LCD - Advanced settings - Printhead Selection -  Z Offset
  Raise it about an inch from the build plate.  Anywhere around there is fine.
	
  This step is essentinal for proper calibration once the Chromahead components are installed.
  If you don't do this step, you risk breaking the hotend when it comes to homing and calibration time, like I did.    
	

![Step 1 photo](images/step-1.jpg)

## Step 2: Enable root access

  Enable root access for your machine using the LCD menu
  
  LCD - Advanced Settings - Enable root?

  This step is required in order to get the username:password to log into your device using SSH.  This will allow you to  
  get the device IDs specific to your coprint hardware so you can update the configs.    

  
![Step 2 photo](images/step-2.jpg)

## Step 3: Print CoPrint models and Mount Hardware

  Feel free to do your own thing here to mount the motors, but here is what I did.
  
  AssemblyParts.zip from CoPrint github
  
  Print:
  3 L brackets 
  1 motor mount bar  
  1 Chromahead adapter plate
  
  I used 2 zip ties to strap this assembly to the top rear crossbar of the giga  


  Power off the GIGA 
  
  Remove the old Orange Extruder and set it aside for later use
  
  Install the printed adapter plate to the original giga mounting plate on the gantry
  
  Mount the Chromahead to the adapter plate
  
  Screw in the 8in1 feeder and attach it's connector and run the filament tubes to each motor, doesn't match which one

  Attach the Chromahead cable to the KCM, run a USB cable from the KCM to the USB Drive port on the front of the Giga
  
  Verify all of your connections

![Step 3 photo](images/step-3.jpg)



## Step 4: Update Configs
     
  Attach the old Orange Extruder, using the original cable, to one of the open plugs like it was Before
  - Rest the extruder on the gantry for now as this is temporary
	   
  Power your CoPrint on
     
  Power your Giga on
	 
  It will boot to an error on the LCD, but you can still access the device via the UI or SSH
	 
	 
  SSH into the giga device with username:password acquired from Step 2
	 
	#List Device IDs
	 	  
	     ls -al  /dev/serial/by-id/
		 
     Output:  	 
     
	 root@giga:/# ls -al /dev/serial/by-id
     usb-head_stm32f103xe_48FF6D067265545217210187-if00
	 usb-kcm_stm32f103xe_53FF6A067189564955502487-if00
	 
	#Copy these values into the config
	 
	 chromahead.cfg gets "usb-head" device-id from above, where noted in the file with "CHANGETOYOURHARDWAREIDHERE"
	 kcm.cfg  gets "usb-kcm" device-id from above, where noted in the file with "CHANGETOYOURHARDWAREIDHERE"
	
	 
	#Save your config changes
		
	 
![Step 4 photo](images/step-4.jpg)


## Step 5: First boot 

  From now on, you need both the chromahead AND the original giga print head connected during the first time the printer boots.	 Once the boot is successful, and you see 
  the normal Elegoo Home screen without errors, you can then remove the original print head for the next operations.  I just leave my original extruder dangling on the gantry.
 
  There is probably a way to bypass this in the config but I haven't researched that as it's a minor inconvenience to connect it at boot for now	 	  
	 
  At this point in Fluidd you will see this error in the UI and log:
	 
    Server configuration error: Error Reading Config: '/home/mks/klipper_config/moonraker.conf' Loaded server from most recent working configuration: '/home/mks/klipper_config/.moonraker.conf.bkp' Please fix the issue in moonraker.conf and restart the server.
		   
	Supplied path (/home/mks/printer_data/gcodes) for (gcodes) is invalid. Make sure that the path exists and is not the file system root.
    Supplied path (/home/mks/printer_data/gcodes) for (gcodes) is invalid. Make sure that the path exists and is not the file system root.
	

	 
 #Reset gcode permissions
	 
 SSH into the device
	 
 cd to /home/mks/printer_data	 
	 
 chmod --reference=/home/mks/printer_data/config /home/mks/printer_data/gcodes
	 
 sudo chown -R elegoo:elegoo /home/mks/printer_data/gcodes
     
 cd ~/moonraker/scripts
	 
 ./set-policykit-rules.sh


![Step 5 photo](images/step-5.jpg)


## Step 6: Set Z Offset

  Using the LCD, Home the printer, and then set the new Z offset for your chromahead
  You should have plenty of room to lower it thanks for the previous step.    
      

![Step 6 photo](images/step-6.jpg)

## Step 7: Perform Calibrations

  Perform platform measurement, Auto leveling and Input Shaping

  Use LCD settings to perform these steps
   
  Note: requires the bed_mesh.cfg and input_shaper.cfg files to be included properly in your printer.cfg


![Step 7 photo](images/step-7.jpg)     
  


## Step 8: Orca Profile Usage

  The profiles I use in OrcaSlicer, not ElegooSlicer, and can be found in the orcaprofile folder.
  There is a profile for how to using a single bed, and one for all 4 beds. 
  This is how you control what beds bet heated for your prints.
	
  # Choose profile for number of heated beds required	
	
  Two provided example profiles: 
  ALL :  All beds are turned on
  Bed 0: Only heats bed 0  
	
  Change these or create your own profiles as needed
	
  Note: When the printer starts your job, only the first bed will heat up first.   After this is complete the rest of your beds will heat up, as needed, and then the print job will proceed.
  Probably a way to fix this with the gcode macros but this is what I'm doing for now.
	
	
  # Filament order for the slicer and CX1 motors	 
	
 Set the colors in the slicer to match how they are loaded in the CX motors

 Example:
	
 OrcaSlicer has 4 filaments added as follows:
	
 1 = White, 2= Green, 3=Blue, 4=Black
   
 Load your filaments in the CX1 units so they match the slicer order, going from left to right:
		
	1st CX= White, 2nd CX= Green, 3rd CX= Blue,  4th CX=Black
				
   Push about a 1ft of filament through each one. 
   The next step will finish loading them properly for printing.


![Step 8 photo](images/step-8.jpg)


## Step 9:  Staging your filament

   
 # See if any filament is detected
  
 Use this console command to check if the sensor has detected any filament from a previous print:
   
 QUERY_FILAMENT_SENSOR SENSOR=filament_sensor
	
 If filament is detected then you will need to unload it using a console command:
   	
 Filament -> CX motor reference: 	
	1 -> T0
	2 -> T1
	3 -> T2
	4 -> T3	
      
   
 Example: Unload the 1st filament
   
	T0  
	UNLOAD_FILAMENT
	

 If after doing this you start a print and it jams, more than likely there may be a cut piece in the 8in1 feeder assembly to remove.  	
 Remove the connector plug, unscrew the feeder, pull the filament piece out, screw feeder back in and connect the plug.
 This cut piece is normally not there after a succesful print but it's worth checking if you're trying to stage filament or print and it's jamming.
    	
 # Stage each filament one by one     
   
 Run these console commands to make sure each filament is ready for printing in the chromahead.
 Each filament will get auto-loaded until the filament is detected in the chromahead, then retract a little and "Park" itself. 
 You will see this happen in the console for each filament.  
 
         
 The FIRST filament to print, should be staged LAST.
   
 Example:  If the first color in your slicer is the first color to print, you would stage 
 your filaments in this order:  2, 3, 4, 1   
       
   Like so...
   
   		T1
		LOAD_FILAMENT

 Note: Wait for "AutoLoad Finished" before proceeding to the next command

    	
		T2
		LOAD_FILAMENT

 Note: Wait for "AutoLoad Finished" before proceeding to the next command

    	
		T3
		LOAD_FILAMENT

 Note: Wait for "AutoLoad Finished" before proceeding to the next command
    
	
		T0
		LOAD_FILAMENT

 Note: Wait for "AutoLoad Finished" 
	
		

![Step 9 photo](images/step-9.jpg)


  Try a print!


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

  #Errors:
	TMC 'stepper_x' reports error: DRV_STATUS: 001f01c3 otpw=1(OvertempWarning!) ot=1(OvertempError!) ola=1(OpenLoad_A!) olb=1(OpenLoad_B!) t120=1 cs_actual=31
	Once the underlying issue is corrected, use the
	"FIRMWARE_RESTART" command to reset the firmware, reload the
	config, and restart the host software.
	Printer is shutdown		

  #Problem
	1.8 value for TMC stepper x resulted in the mobo getting hot on that chip during extended x/y movement for gyroid infill across flat plane
		
  #Solution
	adjusted value to 1.4, due to chroamead being heavier than the original, and it addressed this issue.  
    alternate is to put a fan on the mobo
	 - confirm your fans are working properly to begin with


## Notes


		
