# Restore Numworks firmware (Original guide by RapidZapper)

# ⚠️ DOESN'T WORK ON N0120/EX, DO NOT TRY OR IT WILL PERMANENTLY BREAK YOUR CALCULATOR!

You own a Numworks Calculator? You're a tinkerer and want to use your Numworks as your development platform during the holidays? Great! But now your calculator doesn't boot or you wish to come back on a "stable" base, we're going to show you 2 methods to start again with you Numworks!

## 1) The "Official" Method | N0100 & N0110 with Epsilon Firmware < 16

This method is very probably the easiest to restore your Numworks, and it only requires a computer with a WebUSB-compatible browser such as Chrome, Chromium or recent Edge.

Requirements:
- Numworks Calculator N0100 or N0110 with Epsilon Firmware < 16
- microUSB cable to connect the calculator to the computer or equivalent device
- computer with any OS

Resources:
In order to install the firmware, you'll need a light "recovery" firmware that will allow installation of the normal firmware.
For that you have 2 possibilities:
- by Numworks' website (account needed) : [https://my.numworks.com/devices/](https://my.numworks.com/devices/)
- by Omega's website (no account needed!): [https://getomega.dev/install/latest](https://getomega.dev/install/latest)

In this tutorial, we're going to use the firmware form the Omega website, for its simplicity and the way more interesting features of the firmware!
In the end will still be able to come back to Epsilon if you wish.

1. Place the calculator in "Bootloader" mode by pressing 6 and reset (button at the back of the calculator)
2. Connect the calculator to the computer and go to Omega's website installation page (link above)
3. Select "RECOVERY" in order to install the recovery firmware
4. On the windows that pops up, select "STM32 BOOTLOADER", then the firmware will install

Warning: If the calculator reads "numworks.com/recsue" or is recognized as N???, it means you have Epsilon 16 installed on it, if you have a N0100 you can then proceed to the installation of Omega, but if you have a N0110, you can either install the official firmware on Numworks website (link above) or proceed to the second method below to install Omega.

5. On the website, a button reading "Install OMEGA" will show, you can click it to complete the installation.
Your calculator is now working again!

## 2) (Raspberry Pico) Lifebelt for <??> | N0110 >= E16

2022 New, in shortage context where prices and availability of the Raspberry Pi are low, it's now possible to use the Raspberry Pico, the little guy of the family in order to get back full control over your Numworks 😛.

Without any further ado, here are the requirements:

- N0110 Numworks Calculator (with Epsilon firmware >= 16)
- microUSB cable to connect the calculator to the computer or equivalent device
- computer with any OS (the pack supplied is for Windows, but could be used on Linux with a little tweaking
- Raspberry Pi Pico (preferably H or WH model or standard model with cable soldering)

In order to avoid any problem it is highly recommended to have a header to connect the female-male jumpers to the Pico or to solder male-male jumpers to it, because the contact must be perfect in order to avoid any problem with the procedure.

This method targets Windows, but it should be possible to adapt the `openocd` command to Linux (not tested)

Video (in french)  [https://www.youtube.com/watch?v=ByPRX63qQMY](https://www.youtube.com/watch?v=ByPRX63qQMY)

1. Download the ZIP pack and extract it: [https://bit.ly/3dpwQu8](https://bit.ly/3dpwQu8)
2. Connect your Pico while maintaining the button pressed, you should get access to the Pico from your file explorer. Drag and drop the "picoprobe.uf2" file to your Pico, it should reboot and be ready.
3. Connect the cables as shown in the picture: ![Pico to Numworks connections](pico-connections.png)
4. For an optimum operation, go the the recovery menu by pressing 6+reset, and continue from there when the recovery screen shows up
5. Connect the Pico to the computer and go to the folder you just extracted, then start "unlock.bat"
6. Proceed to reboot the calculator by launching "reset.bat" and verify that the operation went successfully with "readInfo.bat". If it went well you should get
```
stm32f2x user_options 0xCFC, boot_add0 0x0080, boot_add1 0x0040
stm32f2x optcr2_pcrop 0x00000000
```
If `boot_add1` is 0x0080 the the operation hasn't worked, you have to restart at step 5.

Now you can unplug the Pico from the calculator and plug the calculator via USB to your computer. Press 6+reset and you should be able to install Omega (follow method 1) and start again your experiments with the calculator!

## 2, alternative) Any Raspberry Pi 2/3/4/0 on Linux | N0110 >= E16

After an update to Epsilon 16, any modification of the system, and thus development on the platform have been made almost impossible, but we're going to see how to bring back your dear Numworks to a state where it can obey you! That's why you bought it for, right?

Requirements:
- Numworks Calculator N0100 or N0110 with Epsilon Firmware < 16
- microUSB cable to connect the calculator to the computer or equivalent device
- computer with any OS
- Raspberry Pi 2/3/4/0 with preferably Raspberry OS

1. First we're going to install OpenOCD, let's go for a few commands 😉
First we need XPM:
```bash
sudo npm install -y -global xpm@latest
```
Then we install OpenOCD from XPM:
```bash
xpm install --global @xpack-dev-tools/openocd@latest
```
In a standard system, the installation should be here: `/home/pi/.local/xPacks/@xpack-dev-tools/openocd/0.11.0-5.1/.content/bin/openocd`.

Try to start `openocd`, and if you see a screen showing "OpenOCD 0.11.0" you're good to go!
Otherwise try to locate OpenOCD and try to start it again.
2. Create some files for the calculator:
We're going to create 2 files, the first with the configuration to connect to the calculator and the second containing the commands to unlock it.

In the directory of your choice, create:
n0110.cfg
```bash
source [find interface/sysfsgpio-raspberrypi.cfg]

transport select swd

source [find target/stm32f7x.cfg]

reset_config none separate
```
forcedfu.run
```
mww 0x40023C08 0x08192A3B
mww 0x40023C08 0x4C5D6E7F
mww 0x40023C18 0x00400080
mww 0x40023C14 0xC0FFAAFE
```
We'll come back to explain these by the end.
3. The connections
Here is a diagram showing the connections, it should be compatible with PI 2/3/4, no soldering is required if you're deft 🙂
For any other Raspberry Pi, you'll probably have to adapt the configuration files and the connections, but nothing has been tested outside of Pi 2/3/4.
Here I put the GND on the microUSB port shield in order to maintain the 2 cables with one hand on the pads.
The cables used are simple jumpers used with Arduino or Raspberry Pi.
![Connections for Raspberry Pi 2/3/4](rpi-connections.png)
You can also solder the wires but at your own risk, you won't be able to enforce the guarantee in case of damage.
4. Abracadabra, the commands!
Now that everything is wired, go back in a terminal in the folder where you created the 2 files earlier, and execute:
```bash
openocd -f "n0110.cfg" -c "init" -f "forcedfu.run"
```
Now you can unplug your calculator and plug it via USB to your computer while pressing 6, and you should be able to install Omega (see method 1) and start again your experiments!

### Explanations
Since the calculator is open source, a large number of info are available directly from Numworks' website: [https://www.numworks.com/resources/engineering/hardware/electrical/schematics/](https://www.numworks.com/resources/engineering/hardware/electrical/schematics/)

We can thus find the documentation of the STM32 MCU, with the instructions to reset its Flash memory to restore a "factory" configuration, see [this PDF](https://www.numworks.com/resources/engineering/hardware/electrical/parts/stm32f730-arm-mcu-reference-manual-1b6e1356.pdf)
Here is some detail about the commands in "forcedfu.run:
- The first 2 commands derive directly from 3.7.3, allowing to unlock write and erase operations on the Flash
- the 3rd changes back the second bootloader address (`boot_add1`) from the custom bootloader used by Numworks after the E16 update to the stock STM32 bootloader (3.7.7)
- The 4th puts the Flash into the RDP0 protection level (= "no protection") and erases all the content of the calculator, allowing the re-write of the Flash.

Now all the magic of the calculator is back!

Shout-out to RapidZapper for the original guide!
