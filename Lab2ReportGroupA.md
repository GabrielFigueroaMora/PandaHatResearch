# Research Group: Group A

## Laboratory Title: Exploiting an IP Camera

### Members:
- Ángel Vázquez
- Carolina Rodríguez
- Gabriel Figueroa
- Keishlyany Sanabria (Shadow)

## Lab Description:
### Objective and Goals:
##### Device Teardown
- Open the device
- Identify the chipset and their datasheet
- Find communication protocols used by different chips
- Analize System-on-Chip (SoC)
##### UART Pin Identification
- Find UART pads with the help of SoC pinouts
- Find UART pads/pins (pair of 4), which will provide the functionality for Transmit (Tx), Receive (Rx), GNG (Ground) and Vcc (Power)
- Find Tx, Rx, and Ground above the SoC
- Connect pins to Attify Badge and perform UART exploitation
##### Gain Root Access
- Find the baudrate of the device
- Interact over UART to obtain a root shell
- Find root password using the script and mac address
- Login to root user 

### Resources and Dependencies used:
##### Hardware:
- IP Camera
- Screwdriver kit
- Multimeter
- Attify Badge
- Jumper wires
- Micro USB Cable
##### Software:
- baudrate.py
- minicom


### Tasks (Lab Walkthrough):
##### Device Teardown (Setup):
1. Open up the device
2. Identify the various chipsets on the device (SoC, Network chip, Flash chip) 
3. Figure out their functionality, as well as locate their datasheets.
##### Pin Identification
1. Identify which pins correspond to the UART and see where the connection ends up. These pins are mostly assembled in groups of 3 or 4, and consist of connections for Rx, Tx, and Ground.
2. Perform a continuity test on the exposed pads 
##### Interacting with UART and Gain Root Shell Access
1. First, make sure that the camera board is properly connected to a power source, Attify Badge, and the breadboard (UART connection) to replace soldering
2. Once inside the Attify Virtual Machine, access the terminal and use the following commands in order to find the baudrate:

```
cd ~/tools/dev/tty*      ; lists the ttys available and make sure the badge is present

cd baudrate/             ; access the location of the baudrate.py script

sudo python baudrate.py  ; run the script, disconnect & connect the badge again
```


3. Press the **Up** arrow key to switch the preferred baudrate to 5500(?)
4. Let the script run; once you see readable data, hit **Ctrl+C** and wait for the script to ask you for the name you want to save the configuration with. Give it any name and hit **Enter**.
5. On a separate window, run the following script (```extract-private-key.py```) using the device mac address to get the needed password
```insert script here```

6. Enter the root login user and password (555718df)
7. Press ls to see all root files available


### Problems Encountered:
- Difficulty finding the correct datasheets for the chips.
- Difficulty finding the ```baudrate.py file```. Had to use the locate command to find the python file since a “no such file or directory exists” error popped up upon trying it for the first time.
- Device is not recognized by Computer. When looking for the entry names ttyUSB0 or ttyUSB1 to confirm if Attify Badge was connected, nothing was found, which meant it was not recognized.
- Port configuration made laptop crash and restart


### Conclusion:
