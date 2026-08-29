# Coprint Color Upgrade for the Elegoo Orange Storm Giga 3D Printer

> The purpose of this guide is to provide the steps necessary to add a CoPrint KCM Set color upgrade to your Elegoo Orange Storm Giga 3D Printer

---

## Table of Contents
- [Overview](#overview)
- [What You'll Need](#what-youll-need)
- [Before You Start](#before-you-start)
- [Step 1: Adjust Z Offset](#step-1-adjust-z-offset)
- [Step 2: Enable Root Access](#step-2-enable-root-access)
- [Step 3: Print CoPrint Models and Mount Hardware](#step-3-print-coprint-models-and-mount-hardware)
- [Step 4: Update Configs](#step-4-update-configs)
- [Step 5: First Boot](#step-5-first-boot)
- [Step 6: Set Z Offset](#step-6-set-z-offset)
- [Step 7: Perform Calibrations](#step-7-perform-calibrations)
- [Step 8: Orca Profile Usage](#step-8-orca-profile-usage)
- [Step 9: Staging Your Filament](#step-9-staging-your-filament)
- [Step 10: Your First Print](#step-10-your-first-print)
- [Configuration Files](#configuration-files)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

If you are looking to add a CoPrint KCM Set to your existing Elegoo Orange Storm Giga 3D printer, this should hopefully help. Using these config files and the device IDs of your CoPrint hardware, you too can add multicolor capabilities to your printer. Print some files, upload some files, mount your hardware, and be on your way.

## What You'll Need

**Hardware**
- [ ] Original factory Elegoo Orange Storm Giga
- [ ] CoPrint 4 Color KCM set — Chromahead, KCM, 4-extruder kit

**Software / Files**
- [ ] Config files (included in this repo under `/configs`)
- [ ] Slicer profile (included in this repo under `/orcaprofile`)
- [ ] SSH client — e.g. PuTTY

## Before You Start

> **Important**
>
> Before making any hardware or software changes, back up your existing files through the UI. Turn your machine on, connect to it through the UI via its IP address using your favorite browser, and navigate to the Configure section to see a list of files. From here you can download each one by right-clicking and choosing Download. Keep these files safe in case you want to go back to the original factory OEM setup.

What I backed up:
- `fluidd.cfg`
- `moonraker.conf`
- `plr.cfg`
- `printer.cfg`
- `znp_mcu.cfg`

## Step 1: Adjust Z Offset

> **Important**
>
> While your machine is on, adjust the Z offset of the existing extruder in preparation for configuring the new one.

**LCD → Advanced Settings → Printhead Selection → Z Offset**

Raise it about an inch from the build plate — anywhere around there is fine.

This step is essential for proper calibration once the Chromahead components are installed. If you don't do this step, you risk breaking the hotend when it comes to homing and calibration time, like I did.


## Step 2: Enable Root Access

Enable root access for your machine using the LCD menu.

**LCD → Advanced Settings → Enable Root?**

This step is required in order to get the username/password to log into your device using SSH. This will allow you to get the device IDs specific to your CoPrint hardware so you can update the configs.



## Step 3: Print CoPrint Models and Mount Hardware

Feel free to do your own thing here to mount the motors, but here is what I did.

`AssemblyParts.zip` from the CoPrint GitHub repo.

Print:
- 3 L brackets
- 1 motor mount bar
- 1 Chromahead adapter plate

I used 2 zip ties to strap this assembly to the top rear crossbar of the Giga.

1. Power off the Giga.
2. Remove the old Orange extruder and set it aside for later use.
3. Install the printed adapter plate to the original Giga mounting plate on the gantry.
4. Mount the Chromahead to the adapter plate.
5. Screw in the 8-in-1 feeder, attach its connector, and run the filament tubes to each motor (it doesn't matter which one).
6. Attach the Chromahead cable to the KCM, and run a USB cable from the KCM to the USB drive port on the front of the Giga.
7. Verify all of your connections.



## Step 4: Update Configs

Attach the old Orange extruder, using the original cable, to one of the open plugs like it was before.
- Rest the extruder on the gantry for now — this is temporary.

Power your CoPrint on, then power your Giga on.

It will boot to an error on the LCD, but you can still access the device via the UI or SSH.

SSH into the Giga device with the username/password acquired in Step 2.

List device IDs:
```bash
ls -al /dev/serial/by-id/
```

Output:
```
root@giga:/# ls -al /dev/serial/by-id
usb-head_stm32f103xe_48FF6D067265545217210187-if00
usb-kcm_stm32f103xe_53FF6A067189564955502487-if00
```

Copy these values into the config:
- `chromahead.cfg` gets the `usb-head` device ID from above, where noted in the file with `CHANGETOYOURHARDWAREIDHERE`
- `kcm.cfg` gets the `usb-kcm` device ID from above, where noted in the file with `CHANGETOYOURHARDWAREIDHERE`

Save your config changes.



## Step 5: First Boot

From now on, you need both the Chromahead **and** the original Giga print head connected during the first time the printer boots. Once the boot is successful and you see the normal Elegoo home screen without errors, you can then remove the original print head for the next operations. I just leave my original extruder dangling on the gantry.

There is probably a way to bypass this in the config, but I haven't researched that — it's a minor inconvenience to connect it at boot for now.

At this point, in Fluidd you will see this error in the UI and log:

```
Server configuration error: Error Reading Config: '/home/mks/klipper_config/moonraker.conf'
Loaded server from most recent working configuration: '/home/mks/klipper_config/.moonraker.conf.bkp'
Please fix the issue in moonraker.conf and restart the server.

Supplied path (/home/mks/printer_data/gcodes) for (gcodes) is invalid. Make sure that the path exists and is not the file system root.
Supplied path (/home/mks/printer_data/gcodes) for (gcodes) is invalid. Make sure that the path exists and is not the file system root.
```

**Reset gcode permissions:**

SSH into the device, then run:
```bash
cd /home/mks/printer_data
chmod --reference=/home/mks/printer_data/config /home/mks/printer_data/gcodes
sudo chown -R elegoo:elegoo /home/mks/printer_data/gcodes
cd ~/moonraker/scripts
./set-policykit-rules.sh
```



## Step 6: Set Z Offset

Using the LCD, home the printer, and then set the new Z offset for your Chromahead. You should have plenty of room to lower it thanks to the previous step.



## Step 7: Perform Calibrations

Perform platform measurement, auto leveling, and input shaping using the LCD settings to perform these steps.

> **Note:** requires the `bed_mesh.cfg` and `input_shaper.cfg` files to be included properly in your `printer.cfg`.



## Step 8: Orca Profile Usage

The profiles I use are in OrcaSlicer, not ElegooSlicer, and can be found in the `orcaprofile` folder. There is a profile for using a single bed, and one for all 4 beds. This is how you control which beds get heated for your prints.

**Choose a profile for the number of heated beds required:**
- **ALL** — all beds are turned on
- **Bed 0** — only heats bed 0

Change these or create your own profiles as needed.

> **Note:** When the printer starts your job, only the first bed will heat up first. After this is complete, the rest of your beds will heat up as needed, and then the print job will proceed. There's probably a way to fix this with gcode macros, but this is what I'm doing for now.

**Filament order for the slicer and CX1 motors**

Set the colors in the slicer to match how they are loaded in the CX motors.

Example — OrcaSlicer has 4 filaments added as follows:
1. White
2. Green
3. Blue
4. Black

Load your filaments in the CX1 units so they match the slicer order, going from left to right:
- 1st CX = White
- 2nd CX = Green
- 3rd CX = Blue
- 4th CX = Black

Push about a foot of filament through each one. The next step will finish loading them properly for printing.


## Step 9: Staging Your Filament

**Check if any filament is detected**

Since you haven't printed anything yet, there shouldn't be anything to detect yet, but it's a good habit to get into so I've put it here for now.
Until you have performed a print, skip down to "Stage each filament one by one"

Use this console command to check if the sensor has detected any filament from a previous print:
```gcode
QUERY_FILAMENT_SENSOR SENSOR=filament_sensor
```

If filament is detected, you will need to unload it using a console command.

Filament → CX motor reference:
| Filament | CX Motor |
|---|---|
| 1 | T0 |
| 2 | T1 |
| 3 | T2 |
| 4 | T3 |

Example — unload the 1st filament:
```gcode
T0
UNLOAD_FILAMENT
```

If after doing this you start a print and it jams, there may be a cut piece in the 8-in-1 feeder assembly to remove. Remove the connector plug, unscrew the feeder, pull the filament piece out, screw the feeder back in, and reconnect the plug. This cut piece is normally not there after a successful print, but it's worth checking if you're trying to stage filament or print and it's jamming.

**Stage each filament one by one**

Run these console commands to make sure each filament is ready for printing in the Chromahead. Each filament will auto-load until it's detected in the Chromahead, then retract a little and "park" itself. You will see this happen in the console for each filament.

The **first filament to print should be staged last**. For example, if the first color in your slicer is the first color to print, you would stage your filaments in this order: 2, 3, 4, 1.

```gcode
T1
LOAD_FILAMENT
```
> Wait for "AutoLoad Finished" before proceeding to the next command.

```gcode
T2
LOAD_FILAMENT
```
> Wait for "AutoLoad Finished" before proceeding to the next command.

```gcode
T3
LOAD_FILAMENT
```
> Wait for "AutoLoad Finished" before proceeding to the next command.

```gcode
T0
LOAD_FILAMENT
```
> Wait for "AutoLoad Finished".


## Step 10: Your First Print

1. Import a model into OrcaSlicer.
2. Choose the profile related to the heated beds to activate.
3. Paint the model as you see fit.
4. Print!

## Configuration Files

| File | Purpose |
|------|---------|
| `configs/printer.cfg` | What it does |
| `configs/kcm.cfg` | Contains KCM device-specific config — you make a change here |
| `configs/chromahead.cfg` | Contains Chromahead-specific config — you make a change here |
| `configs/bed_mesh.cfg` | Contains bed mesh data |
| `configs/input_shaper.cfg` | Contains input shaper profile data |
| `configs/cp_macro.cfg` | Contains gcode for macros called during printer functions in the slicer or firmware |

## Troubleshooting

**Error: TMC stepper_x**
```
TMC 'stepper_x' reports error: DRV_STATUS: 001f01c3 otpw=1(OvertempWarning!) ot=1(OvertempError!) ola=1(OpenLoad_A!) olb=1(OpenLoad_B!) t120=1 cs_actual=31
Once the underlying issue is corrected, use the "FIRMWARE_RESTART" command to reset the firmware, reload the config, and restart the host software.
Printer is shutdown
```

**Problem:** A value of 1.8 for TMC `stepper_x` results in the motherboard getting too hot on that chip during extended gyroid infill movements across a flat plane.

**Solution:** Adjust the value from 1.8 to 1.4 for `stepper_x` in the `printer.cfg` file. This issue is caused by the Chromahead being heavier than the original.

```
[tmc2209 stepper_x]
	uart_pin: PE5
	run_current: 1.4  
	hold_current: 1.0
	interpolate: False
```

## License
	
This project is licensed under the MIT License — see the LICENSE file for details.