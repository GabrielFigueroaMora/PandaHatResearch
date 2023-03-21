# PandaHat Research - Group A
Repo for PandaHat Research Team WriteUps and Related Notes / Code.

## Lab 1 Report - Exploiting a Smart Plug

### Members:
- Ángel Vázquez
- Carolina Rodríguez
- Gabriel Figueroa
- Keishlyany Sanabria (Shadow)

## Lab Description:

### Objective and Goals:
- Reverse mobile application to find encryption type and key
- Network traffic sniffing to capture the traffic of the Smart Plug
- Exploit Smart Plug
- Decrypt the network capture using identified encryption key
- Control the Smart Plug without using the mobile device
### Tasks (Lab Walkthrough):
##### Setup:
- Download mobile application for Orvibo and register an account; add device on app and put it into configuration mode

##### Mobile Application Analysis:

- Start the mobile application reversing to analyze the smart plug using the JADx tool by skylot

   ```$ jadx-gui homemate.apk```
    
    Navigate to ```SecurityAes.java``` located in ```homateap.orvibo.com.securitylibrary``` package
    
    Locate code for encryptByte() function by typing the following:
    
         $ unzip HomeMate.apk -d homemate
      
         $ cd homemate/lib/armeabi/
         
         $ ls libHomeMate_Security.so

- Find native libraries by extracting the application binary and analyze it by performing reverse engineering with Binary Ninja
- Analyze encryptByte() function
- Obtain encryption key and identify type of encryption being used
##### Creating and Capturing Network Traffic 
- Create network hotspot to analyze network communication. This will differ based on the OS the user has
- Perform network traffic analysis of the target smart plug
- Connect device to hotspot 
- Pair socket to access point and start Wireshark capture
- Set up display filter on Wireshark to show only relevant packets
- Perform DNS lookup on the IP obtained from Wireshark
- Decrypt obtained packet using previously found key using the following script
    ```from Crypto.Cipher import AES
    
       aeskey = [previously obtained key here]
       
       wiresharkpacket = "copied-wireshark-packet".decode("hex")
       
       aesobj = AES.new(aeskey, AES.MODE_ECB)
       
       print str(aesobj.decrypt(wiresharkpacket{42:]))
       
- Identify important packets that instruct socket to turn ON or OFF

### Controlling the Smart Plug:
- Create a proxy tool to relay packets between the socket and server and allow the injection of custom messages.
- Ensure the injected messages are using a similar encryption to the ones the socket is designed to recieve.
- Perform an Evel Twin attack to take control of the smart plug via the the web server setup by the relay tool.

### Resources and Dependencies used:
#### Hardware:
- Orvibo Smart Plug
- Mobile Application (Android/IOS)
#### Software:
- Attify Hardware Pen-Testing Kit Manual PDF FIle
- LInux VM 
- HomeMate.apk
- Binary Ninja
- Wireshark
- aesdecrypt.py
- orvibocontrol.py

### Problems Encountered:
- Mobile Application Analysis:
- Difficulty with obtaining the encryption key when analyzing the encryptByte() function
- Creating and Capturing Network Traffic:
- Recurring issues with the hotspot configuration. The group could not setup a working hotspot from a laptop, so a phone hotspot was used instead. 
- Controlling the Smart Plug:
- In order to control the On and Off commands, the group had to deal with an out-dated Python 2 Script. We worked around it by installing and running Python 2 instead of Python 3. 
- The Python 2 script activated an unresponsive web server that didn’t register the given commands. After careful analysis, we worked around it after figuring out there was a small misconfiguration in the data it was being given.

### Conclusion

The research group (Group A) conducted a lab to exploit a Smart Plug. The objective of the lab was to reverse engineer the mobile application used to control the Smart Plug, capture and analyze its network traffic, identify the encryption key and type being used, and control the Smart Plug without using the mobile device. In conclusion, Group A was unable to complete the Exploiting a Smart Plug lab due to difficulties controlling the smart plug. The outdated Python script used to create a proxy server to redirect traffic from the plug to the group's system was not functioning properly, which prevented the group from controlling the plug. Despite attempts to work around the issue, the group was unable to overcome this obstacle within the timeframe of the lab.



