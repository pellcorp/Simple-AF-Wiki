The Cartographer3D Survey-Touch is an eddy current induction sensor designed for 3D printing, offering high accuracy and repeatability. It is compatible with various 3D printer configurations and provides features like auto-Z offset and survey touch. The Cartographer3D comes in several orientations. The right angle, the low profile, and the flat pack, all of which are available for purchase on its official website http://cartographer3d.com

Before using the Cartographer3d with any K1 series printer decide which orientation you want to use. The table below lists your mount options and links to supported mounts. The Right Angle configuration is the default.

### Mount Options

!!! info

    The Mount column is the switch used during the Simple AF install process.
    
    /usr/data/pellcorp/k1/installer.sh --install cartotouch --mount Mount 
    
    When implemented, the install command will look like this if using the Right Angle probe for a stock K1.  
    
    /usr/data/pellcorp/k1/installer.sh --install cartotouch --mount Default


| Mount           |Carto| URL                                                                                                                  |Notes|
|-----------------|-----------|----------------------------------------------------------------------------------------------------------------------|----------|
| **Default**     |Right Angle| [Zerodotcmd Right Angle K1 series](<https://www.printables.com/model/1037606-cartographer-3d-right-angle-k1-series-mount>)           ||
| **D3vilStock**  |Flat Pack  | [BootyCall Jones K1 & K1Max Eddy Current Mount](<https://www.printables.com/model/684338-k1-k1max-eddy-current-mount-cartographer>)  ||
| **BootyGantry** |Right Angle| [Rail Carriage Mount](<https://github.com/tlace17/K1-Linear-Rail-Gantry/blob/main/STLs/Probe%20Mounts/Rail%20Carriage%20Carto%20Mount.stl>) |May require shimming for correct nozzle offset|
| **SkeletorMK7** |Low Profile| [tbkMaker Skeletor Collection](<https://www.printables.com/model/833769-the-skeletor-collection-a-creality-k1k1-maxk1c-coo>)                |This is only for the low profile cartographer version of the mount!!!|
| **PurcellV5**   |Right Angle| [Purcells Cartographer Mount Collection](<https://www.printables.com/model/1071493-cartographer-probe-side-mount-options-for-creality>)     ||

### Nozzle Offset


!!! WARNING

    ***You must verify the probe model to nozzle tip distance is within the valid range of 2.6 to 3mm.***  
    Anything out of this range will be problematic during the Touch Calibration steps. 
    
    These tools can be used to verify the nozzle offset:

    <https://www.printables.com/model/1060868-cartographer-probe-nozzle-offset-tool>

    <https://www.printables.com/model/1121309-cartographer-probe-nozzle-offset-tool-x-offset>

### K1M vs K1/K1C/K1SE

!!! info

    On a K1M you can use the lidar cable either directly by repinning it, or via the pass-through lidar port on the toolhead.  
    However, you cannot use the lidar port on the toolhead for K1, K1C, or K1SE.   The reason this does not work is because for Lidar on the K1M, Creality 
    routes a completely separate USB cable from the mainboard. 

!!! danger

    If you are not using a side mount you **must** verify config changes for cartotouch.cfg and cartographer-k1.cfg 
    or cartographer-k1m.cfg before **homing your printer**, using **Screws Tilt Calculate** or doing a **bed mesh**!  

    Ignoring these instructions has caused ***SIGNIFICANT*** damage to the build plate and/or probe.


