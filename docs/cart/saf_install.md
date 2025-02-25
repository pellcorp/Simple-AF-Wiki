
# Simple AF Firmware requirements

!!! warning

    This guide assumes you have a K1, K1C or K1 Max and you are running stock creality firmware 1.3.3.5 or higher, or alternately you are using  [my prerooted firmware](https://github.com/pellcorp/creality/wiki/Prerooted-K1-Firmware).   Any other pre-rooted firmware is explicitly not supported and the installer.sh will validate this and refuse to proceed if you try to use it on different firmware.

## Probe Installation

You will have chosen and printed a mount for your style probe and mounted it accordingly to your toolhead.

## Cartographer Firmware

!!! warning

    You must have flashed your cartographer with `CARTOGRAPHER K1 5.1.0` survey firmware **before** starting the installation:

    ![image](assets/images/cartographer_k1_510.png)

    There is a guide for this [here](firmware_flashing.md)

## Simple AF Installation

!!! warning
    The installation can only be performed on a printer which has been rooted and ssh granted.

This guide assumes you have a K1, K1C or K1 Max and you are running stock creality firmware 1.3.3.5 or higher and enabled root access, if you need instructions on how to root your print you can follow [Guilouz's excellent instructions](https://guilouz.github.io/Creality-Helper-Script-Wiki/firmwares/install-and-update-rooted-firmware-k1/#enable-root-access.Alternatively you can use [Pellcorp's pre-rooted firmware](https://github.com/pellcorp/creality/wiki/Prerooted-K1-Firmware). Any other pre-rooted firmware is explicitly not supported and the installer will validate this and refuse to proceed if you try to use it on different firmware.

!!! tip

    ZeroDotCmd (aka Zero on discord) has provided an excellent Cartographer installation video, you can find it <https://www.youtube.com/watch?v=GuxMITM9o4I>. As Simple AF is ever evolving, please refer back to the wiki for updates.

### 1. Factory Reset

You **must** do a factory reset before running the installer.sh. Follow these steps to do a factory reset, which retains root access and skips the startup calibration checks:

    ```bash
        wget --no-check-certificate  https://raw.githubusercontent.com/pellcorp/creality/main/k1/services/S58factoryreset
    ```
    ```bash
        chmod +x S58factoryreset
    ```
    ```bash
        ./S58factoryreset reset
    ```

!!! warn

    It is really important you do not close the ssh session until you get this message:

    ![image](assets/images/factory_reset.png)

### 2. Clone the Repo

    ```bash
        git config --global http.sslVerify false
        git clone https://github.com/pellcorp/creality.git /usr/data/pellcorp
        sync
    ```

??? note "RPC Timeouts, try SSH Git Clone"
    ```bash
         mkdir -p /root/.ssh
         wget --no-check-certificate "https://raw.githubusercontent.com/pellcorp/creality/main/k1/ssh/git-ssh.sh" -O /root/git-ssh.sh
         chmod 777 /root/git-ssh.sh
         wget --no-check-certificate "https://raw.githubusercontent.com/pellcorp/creality/main/k1/ssh/pellcorp-identity" -O /root/.ssh
         pellcorp-identity
        export GIT_SSH_IDENTITY=pellcorp
        export GIT_SSH=/root/git-ssh.sh
        git clone git@github.com:pellcorp/creality.git /usr/data/pellcorp
        cd /usr/data/pellcorp && git remote set-url origin https://github.com/pellcorp/creality.git && cd
    ```

!!! Info
== Config Overrides ==
    If you have pellcorp-overrides in github but not stored locally, [you need to recreate the /usr/data/pellcorp-overrides directory](extras/config_overrides.md#create-local-repo) before running the installer.sh!

### 3. Run the installer

To run the script enter the following command replacing MOUNT with the designation from the table under [Mount Options](preparation.md#mount-options). Example:  */usr/data/pellcorp/k1/installer.sh --install cartotouch --mount BootyGantry* if you are using the rail carriage mount or  */usr/data/pellcorp/k1/installer.sh --install cartotouch --mount Default* if you are using Zerodotcmd's Right angle mount

    ```bash
        */usr/data/pellcorp/k1/installer.sh --install cartotouch --mount Mount*
    ```

!!! tip

    If you are using a non-supported mount you should skip the `--mount` option and adjust your configuration after installation before trying to perform a bed mesh or Screws Tilt Calculate!

#### NOTE "RPC failed; curl 18 transfer closed"

    You might get this error:

   `error: RPC failed; curl 18 transfer closed with outstanding read data remaining
    fatal: the remote end hung up unexpectedly
    fatal: early EOF
    fatal: index-pack failed`

    Run the installer.sh script (with the same probe mount) again, it will start from the stage that failed (most of the time this
    will be the Installing Klipper stage!)
    
    You can also prefix the installer command with `AF_GIT_CLONE=ssh` to force git to clone via ssh, this will take a **lot** longer, but it will never time out, so its good in a pinch if you are getting repeated klipper repo clone failures.

## Post Installation

== MCU Firmware updates are pending ==

At the end of the installer process if you get this message:

    `WARNING: MCU Firmware updates are pending you need to power cycle your printer!`

It means that new MCU firmware updates need to be applied and this can only be done by power cycling the printer (turning your printer off at the switch).  After your printer is power cycled you can verify firmware was updated with the `CHECK_FIRMWARE` macro from Fluidd or Mainsail. You should see the following message message if your MCU was updated successfully:
    'INFO: Your MCU Firmware is up to date!'

If you still see the `MCU Firmware updates are pending you need to power cycle your printer!` message after a power cycle, check the `/tmp/mcu_update.log`, you may be asked to provide this file on Discord if you need additional assistance. An additional power cycle can  sometimes solve the problem. There is a very short window of time (aproximately 15 seconds) in which the MCU firmware can be updated, so there is a chance it will work after a second power cycle.

### Verify USB Key

!!! tip

    You should verify your USB key often by typing `CHECK_USB_KEY` in the Fluidd / Mainsail console command or by clicking the Check_USB_Key macro

It is important to make sure you have a way to [emergency factory reset](extras/misc.md#emergency-factory-reset) the printer should the worst happens. The `CHECK_USB_KEY` macro wait for you to plug a USB key in and tell you if it was able to be successfully mounted.

![image](assets/images/check_usb_key.png)

- If you get the message: `INFO - USB Key was recognised and mounted correctly (/tmp/udisk/sda1)`, your USB is perfect to use for an emergency factory reset.

- If you get no message at all before the script ends (after 60 seconds), your USB is defective. You can check the `messages.log` file in the logs section of your UI to get more details about why the usb key could not be mounted!

==Congratulations you are now ready to perform probe calibratons!==

