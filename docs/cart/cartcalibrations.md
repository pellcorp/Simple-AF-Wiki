# Cartographer3D Calibrations



#### Manual Cartographer Calibrate

It is a good idea to heat the nozzle to 150c for this step first!

1. Home X Y (`G28 X Y`)
2. Heat Nozzle to 150c (`M109 S150`) so that any filament can be removed from nozzle
3. Make sure nozzle is centred on bed
4. Run `CARTOGRAPHER_CALIBRATE METHOD=manual`
Follow the [Paper Test Method](https://www.klipper3d.org/Bed_Level.html#the-paper-test)
<br />Upon completion *`SAVE_CONFIG`*

!!! warn

    Do not use a metal feeler gauge for this step, it could interfere with calibration!!!

After the save config you have to do the cartographer threshold scan (see next)

#### Cartographer Threshold Scan

**WARNING:** For this next step, it is really important to be near your printer for this step, because if there
is any issue with the printer configuration or your carto probe, its possible the nozzle will dig itself into the bed, so be hovering over that e-stop button!

6. Home All (`G28`)
7. Make sure nozzle is centred on bed
8. Heat Nozzle to 150c (`M109 S150`) so that any filament can be removed from nozzle
9. Execute `CARTOGRAPHER_THRESHOLD_SCAN SPEED=2 MIN=1500 MAX=5000`
<br />Upon completion *`SAVE_CONFIG`*

After the save config you have to do the touch calibration.

#### Cartographer Touch Calibration

!!! danger

    For this next step, it is really important to be near your printer for this step, because if there is any issue with the printer configuration or your carto probe, its possible the nozzle will dig itself into the bed, so be hovering over that e-stop button!

10. Home All (`G28`)
11. Heat Nozzle to 150c (`M109 S150`) so that any filament can be removed from nozzle
12. Execute `CARTOGRAPHER_CALIBRATE`
<br />Upon completion *`SAVE_CONFIG`*

!!! tip

    If this fails after 3 tries, you should check to make sure there is not filament stuck to the bottom of your nozzle!

**Source:** <https://docs.cartographer3d.com/cartographer-probe/survey-touch>

### Axis Twist Compensation

Next it is highly recommended to perform axis twist compensation calibration **if you are using a rear mount** before doing anything else, this will affect the quality of
your bed mesh, so best to do it before.

1. Home All (`G28`)
2. Run `AXIS_TWIST_COMPENSATION_CALIBRATE` The calibration wizard will prompt you to measure the probe Z offset at a few points along the bed
<br />Upon completion *`SAVE_CONFIG`*

!!! warn

    Do not use a metal feeler gauge for this step, it could interfere with calibration!!!

**Source:** <https://www.klipper3d.org/Axis_Twist_Compensation.html>

### Tuning

At least PID tuning (bed and extruder) and input shaping is required for acceptable printing.  If you try and print after running the installer.sh and a power cycle but before any calibration you will most likely have horrendous quality, the worst you have ever seen on the k1.   After PID tuning and input shaping you should see the same kind of quality as you get with stock k1 + input shaper fix.

#### Quick Start

You can use the QUICK_START Macro to automatically complete Bed and Nozzle PID Tuning and Input Shaping Automatically.

#### Pid Tuning

**Source:** [Calibrate Pid Settings](https://www.klipper3d.org/Config_checks.html?h=pid#calibrate-pid-settings)

For example you might run these:

```
PID_CALIBRATE_BED BED_TEMP=65
PID_CALIBRATE_HOTEND HOTEND_TEMP=230
```

!!! note

    The `PID_CALIBRATE_BED` and `PID_CALIBRATE_HOTEND` macros are located in the `useful_macros.cfg` file and they have defaults values for BED_TEMP and HOTEND_TEMP so you can just run them by clicking on them if you want that same temperature.

#### Input Shaping

There is no default configuration for input shaping so it is essentially disabled out of the box.

You can use the `SHAPER_CALIBRATE` macro to run input shaping, just be sure to `SAVE CONFIG` at the end, to choose the automatically selected shaper config, be aware though that the shaper chosen might be sub-optimal due to a slight difference in vibrations between two options.  So you should probably review the output and potentially choose an alternative if it gives you higher recommended max acceleration for minimal increase in vibration.

[Input Shaper Auto Calibration](https://www.klipper3d.org/Measuring_Resonances.html#input-shaper-auto-calibration)

Please make sure to remove any scanner related config from the save config section at the bottom of printer.cfg and save and restart before re-calibrating.

#### Recalibrating

If you are recalibrating, the follow SAVE_CONFIG sections should be removed:

- [scanner model default]
- [scanner]

!!! warn

    Please do not remove the `[scanner]` section defined at the end of printer.cfg above the save config section.


### Other Calibrations

!!! info

    The default value for pressure advance is set to `0.04`

Refer to [Orcaslicer Calibration](https://github.com/SoftFever/OrcaSlicer/wiki/Calibration) for more calibrations

Refer to the [Ellis Print Tuning Guide](https://ellis3dp.com/Print-Tuning-Guide/) for more great tuning ideas.

## Troubleshooting

<https://docs.cartographer3d.com/cartographer-probe/survey-touch/survey-troubleshooting>

### Repo has diverged from remote

![image](assets/images/cartographer_repo_diverged.png)

You need to click RECOVER and then run from ssh:

```
/usr/data/cartographer-klipper/install.sh
```

### Error during probe mcu identification, check connection

If you get the following error, it means that the cartographer is not connected to the printer.   This is either because its physically not connected, the wiring is wrong, the usb subsystem has disconnected the carto during a restart or the serial id is wrong

![image](assets/images/carto_check_connection.png)

So from ssh run a `lsusb` and make sure you can see:

![image](assets/images/carto_lsusb.png)

If you cannot see it in `lsusb`, then it very likely means either the carto is wired incorrectly, or if was working, it just means that an attempt to restart the carto failed because the K1 refused to recognise it, there is no recourse except to Reboot the printer via Fluidd / Mainsail in this case.

If you can, then verify that the serial id matches:

![image](assets/images/carto_serial.png)

Make sure it matches the `serial` in `cartotouch.cfg`:

![image](assets/images/carto_scanner_serial.png)

### Manual Cartographer Serial Device configuration

You can run the following command to fix your serial if you forgot to plug your cartographer in during the installation or update:

```
/usr/data/pellcorp/k1/installer.sh --fix-serial
```

### Timer too close and microsteps

For cartographer you cannot use more than `microsteps: 32`, the MCU cannot handle high microsteps and cartographer, it puts too much pressure on the system and it will cause stuttering during bed meshes.
