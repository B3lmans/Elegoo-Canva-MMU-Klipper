



##### It took me time, frustration episodes and broken canvas/ad5m mainboards to get here, a donation will help my work, thanks!!!



# Elegoo CC1 CANVAS on AD5M that's running Klipper

 ## Mechanical testing and calibration HAS NOT BEEN DONE

## What to use


1. `canvas-klipper-flasher` — replaces the stock CANVAS application with the
   verified CANVAS Klipper firmware.
2. `ad5m-canvas-host-addon` — installs the matching CANVAS/AFC support on the
   AD5M without reinstalling Klipper.


## Requirements

- Regular Flashforge Adventurer 5M, not AD5M Pro.
- Standard AD5M Klipper Mod v00.05 already installed and working.(GUPPY SCREEN)
- Windows computer on the same network as the printer.
- Windows OpenSSH client installed (`ssh.exe` and `scp.exe`).
- Printer IP address and SSH login (`root`; the usual password is `klipper`).
- One CC1 Elegoo CANVAS with STM32F401XC.
- CANVAS powered by validated 24 V and GND wiring. (CHECK PICTURES)
- Modified USB data cable connected from CANVAS to the AD5M USB port. ( Please make sure it is a USB A (to usb a/b/c/etc doesn't matter))



## | CANVAS wire -> USB wire  (check pictures)

## | DM | White |

## | DP | Green |

## | GND | Black | Common USB/power ground |

## 24 V supply

Do not connect the USB red 5 V conductor to the CANVAS 24 V input. Disconnect
other USB serial/ACM devices while installing.


## 1. Start the hardware

1. Turn off the AD5M and CANVAS power.
2. Recheck DM, DP, GND and 24 V before connecting them.
3. Connect the CANVAS USB data cable to the AD5M USB port.
4. Apply the validated 24 V supply to CANVAS.
5. Turn on the AD5M and wait until Klipper and the network are ready.

CANVAS connects to the AD5M. You will use your pc to flash the canvas over the wifi


## 2. Flash CANVAS

Keep this guide and both complete installer folders together in the same
extracted folder. Open Windows PowerShell in that folder and run the command
below. Replace the IP address if the printer uses a different one.

```powershell
powershell -ExecutionPolicy Bypass -File ".\canvas-klipper-flasher\Upload-And-Flash-CANVAS.ps1" -PrinterIp 192.xxx.x.xxx
```

1. Enter the printer SSH password when requested (klipper I think). It may be requested once for
   upload and again for the remote flashing session.
2. Read the permanent-change warning.
3. Type exactly `FLASH CANVAS` and press Enter.
4. Keep both the AD5M and CANVAS powered. Do not unplug USB.
5. Wait for `SUCCESS` and confirmation that CANVAS reports USB ID `1d50:614e`.

This flash overwrites the original Elegoo CANVAS application. 

## 3. Install the CANVAS host add-on

Keep CANVAS connected and powered. Confirm the printer is idle, then run:

```powershell
powershell -ExecutionPolicy Bypass -File ".\ad5m-canvas-host-addon\Install-CANVAS-Host.ps1" -PrinterIp 192.xxx.x.xxx
```

1. Enter the printer SSH password when requested. (klipper I think)
2. Type exactly `INSTALL CANVAS HOST` and press Enter.
3. Do not turn off the printer or CANVAS while Klipper restarts.
4. Wait for:

```text
SUCCESS: Klipper is ready with the CANVAS MCU and AFC loaded.
```

If it instead says the add-on is already installed and matches the bundle,
this step is also complete.

## 4. Confirm the ready state without moving anything

Run these read-only checks from Windows PowerShell:

```powershell
(Invoke-RestMethod "http://192.xxx.x.xxx:7125/printer/info").result.state
```

The result must be `ready`.

```powershell
$objects = (Invoke-RestMethod "http://192.xxx.x.xxx:7125/printer/objects/list").result.objects
$objects | Where-Object { $_ -eq "mcu canvas" -or $_ -eq "AFC" -or $_ -like "AFC_lane CANVAS_*" }
```

The list must contain:

```text
mcu canvas
AFC
AFC_lane CANVAS_1
AFC_lane CANVAS_2
AFC_lane CANVAS_3
AFC_lane CANVAS_4
```

At this point CANVAS firmware and the AD5M host integration are installed and
communicating. All four lanes remain commissioning-locked. Stop here; sensor,
motor-direction, filament-distance, probe and mechanical calibration are a
separate procedure that I didn't had time to do, there is much work left but this is a starting point.


Automatic loading to the hub: disabled
Automatic homing to the hub/toolhead: disabled
Motor assistance and assisted unloading: disabled
Hub and toolhead runout handling: disabled
Automatic unloading after runout: disabled
Cutter operation: disabled
Park, purge/poop, kick, wipe and tip-forming routines: disabled
Automatic temperature-command remapping: disabled
AFC replacement of AD5M pause, resume and unload macros: disabled
