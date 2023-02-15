# Sovol-SV06-firmware

This repository contains firmware for the SV06 3D printer from Sovol. You should not use this firmware on any other printer.

I am creating these files for my personal use and cannot be held responsible for what it might do to your printer.

⚠️ The instructions found on this page apply only to the `master` branch.

⚠️⚡☢️ USE AT YOUR OWN RISK! YOU HAVE BEEN WARNED! ☢️⚡⚠️

# Highlights

- Minimum configuration settings for Mainsail/Fluiddpi to work.
- Mechanical gantry calibration/`G34` macro.
- SuperSlicer config bundle that contains the printer configuration, as well as what are considered by many to be the best print settings available for an FDM printer. Find the differences between the different print setting profiles [here](https://github.com/AndrewEllis93/Ellis-SuperSlicer-Profiles/tree/master/SuperSlicer).
- Bed model and texture to use in SuperSlicer/PrusaSlicer.
- Macros
  - Misc macros: `PRINT_START`, `CANCEL_PRINT`, `PRINT_END`, `PAUSE`, `RESUME`.
  - Parking macros (parks the printhead at various locations): `PARKFRONT`, `PARKFRONTLOW`, `PARKREAR`, `PARKCENTER`, `PARKBED`.
  - Load/unload filament macros.
  - Purge line macro.

To do:
- Get the Ellis `TEST_SPEED.cfg` working.
# Installation Steps

💡 **Requirement**: klipper must be installed on the host Raspberry Pi for everything to work after the flash. Easiest is to use a Fluiddpi or MailsailOS image.

## Flash Firmware

1. Copy `klipper.bin` to a MicroSD card and rename to `anyNewFilename.bin`.
2. Make sure the printer is off.
3. Insert MicroSD into printer.
4. Turn on the printer and wait a minute (usually takes 10 seconds).
5. Turn off printer and remove MicroSD.

You may find this [video](https://youtu.be/p6l253OJa34) useful.

⚠️ **Caveat**: flashing will only work if current firmware filename (`anyNewFilename.bin` in this example) is different from previous flashing procedure. The `.bin` is also important.

## Clone the Repository

There are many ways to get started, the simplest being to clone this repo to the appropriate folder.

1. `cd ~/printer_data/config`
2. Empty entire `~/printer_data/config` folder. Unfortunately, for safety reasons I will not post this command here. However, in linux, you can delete files via `rm filename`.
3. `git clone -b master --single-branch git@github.com:bassamanator/Sovol-SV06-firmware.git .`

## Initial Steps

⚠️⚡☢️ **Your finger should be on the power switch for most of these steps** ☢️⚡⚠️

⚠️⚡☢️ **Power off if there is a collision/problem** ☢️⚡⚠️

💡 I would recommend searching for the word `NOTE` in this repository. There are about 6 short points amongst the various files that you should be aware of if you're using this `printer.cfg`.

I recommend no filament be loaded for any of these steps.

1. Do a `G28`; home all.
   1. Check to see if `X` and `Y` max positions (`G1 X223 F3000`, `G1 Y223 F3000`) can be reached, and adjust `position_max`, if necessary. You can probably go all the way up to `225` for `X` and `Y` both, however, I would not recommend it.
2. Do a `G34`; mechanical gantry calibration. After the controlled collision against the beam at the top, there will be a 10 second pause for you to verify that both sides of the gantry are pressed up agaisnt the `stoppers` at the top. You will hear a succession of beeps.
   1. Figure out your `Z` `position_max` by baby stepping your way up to the beam. The range is 250 to 261 from what I've seen, could be even higher for you. Adjust `position_max`, if necessary. I can go all the way to 258 over and over again, however, I would not print anything higher than 255 probably.
3. Pid tune the bed, but first move the printhead to the center. Ideally, all Pid tuning should occur at the temperatures that you print most at.
   1. `G28`
   2. `G1 X111 Y111 Z40 F6000`
   3. `PID_CALIBRATE HEATER=heater_bed TARGET=70`
   4. `SAVE_CONFIG` (once completed)
4. Pid tune the extruder while part cooling fan runs at 25%.
   1. `G28`
   2. `G1 X111 Y111 Z10 F6000`
   3. `M106 S64`
   4. `PID_CALIBRATE HEATER=extruder TARGET=245`
   5. `SAVE_CONFIG` (once completed)
5. Adjust `z_offset`. Make sure your nozzle if very clean. Paper test [reference](https://www.klipper3d.org/Bed_Level.html?h=probe_calibrate#the-paper-test).
   1. `SET_HEATER_TEMPERATURE HEATER=heater_bed TARGET=60`
   2. `SET_HEATER_TEMPERATURE HEATER=extruder TARGET=180`
   3. Proceed to next steps after both temperatures have been reached.
   4. `G28`
   5. `PROBE_CALIBRATE`
   6. `SAVE_CONFIG` (once completed)
6. Create a bed mesh.
   1. `SET_HEATER_TEMPERATURE HEATER=heater_bed TARGET=60`
   2. `SET_HEATER_TEMPERATURE HEATER=extruder TARGET=180`
   3. Proceed to next steps after both temperatures have been reached.
   4. `G28`
   5. `BED_MESH_CALIBRATE`
   6. `SAVE_CONFIG` (once completed)

## Sources

- https://www.klipper3d.org
- https://ellis3dp.com/Print-Tuning-Guide
- https://github.com/strayr/strayr-k-macros
- https://docs.vorondesign.com/build/software/miniE3_v20_klipper.html
- https://github.com/spinixguy/Sovol-SV06-firmware
- https://github.com/Pr20100/SOVOL-SV06-Klipper-profile
- https://www.printables.com/model/378915-sovol-sv06-buildplate-texture-and-model-for-prusas
- https://github.com/AndrewEllis93/Ellis-SuperSlicer-Profiles
