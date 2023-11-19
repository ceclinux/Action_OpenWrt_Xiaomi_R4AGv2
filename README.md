### - Note: Welcome to try out it, You Agree to Proceed and Use at your own risk. **
- [Releases](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/releases)

```
openwrt-ramips-mt7621-xiaomi_mi-router-4a-gigabit-v2-squashfs-sysupgrade.bin

By the way, the password is password
```

### There are two types of firmware [releases](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/releases)
    1. CSW-LEDE-R22.10.1 Kernel 5.4.214 (use coolsnowwolf/lede R22.10.1)
    2. Openwrt
    
### Projects divided into several folders:
    1. LEDE and several version of opwrt-22.03.2 or  22.03.05 or 23.05, (v23.05 officially support R4AGv2)
    
![image](https://user-images.githubusercontent.com/117250841/200182184-1374ebfe-cca5-40fb-a7ca-4eaa1444f2f0.png)

### - Note: LED lights NOT working properly with 22.03.2 . (LED start working properly with/from Openwrt-v23.03.03 )

## How to built/Compile Openwrt firmware(bin)?
If you want to learn about it, you can visit the followings:
  - https://github.com/wbs306/Action_OpenWrt_Xiaomi_R4AG, Xiaomi R4A Gigabit Breed Flash (NOT for R4AGv2)
  - https://github.com/wbs306/lede
  
### Thanks to [wbs306](https://github.com/wbs306)

- Many Thanks to wbs306's codes for Xiaomi R4AG V2 hardware changes! [wbs306/lede@a2a3a4a](https://github.com/coolsnowwolf/lede/commit/a2a3a4a8d2b4c427e066200a8a64289fe4d6c281) 

    1. target/linux/ramips/dts/mt7621_xiaomi_mi-router-4a-gigabit-v2.dts
    2. target/linux/ramips/image/mt7621.mk
    3. target/linux/ramips/mt7621/base-files/etc/board.d/02_network
       
- NOTE: from 23.03.3, you can fix LEDs, check hw2add folder
    1. target/linux/ramips/mt7621/base-files/etc/board.d/01_leds
    2. /include/kernel-defaults.mk


## How to BREED flash Openwrt bin file?

###  Step#1 How to Telnet(talk) to R4AGv2 Router? 

Successfully used @LordPinhead's code [commit #99634522](https://github.com/acecilia/OpenWRTInvasion/pull/155/commits/996345221db8800a569093fd7ad5a642b160bcbc) to telnet XiaoMi R4A V2 Gigabit (RA4Gv2) 

```
#Run the script
    pip3 install -r requirements.txt # Install requirements 
    python3 remote_command_execution_vulnerability_v2.py
```

###  Step#2 How to install bootloader "BREED" onto R4AGv2 router?
```
    1. Refer to this instrution https://www.right.com.cn/forum/thread-4007071-1-1.html
    (This instruction is just a reference, make your own judgements and adjustments to what you need)
    
    2. Go to https://breed.hackpascal.net, For RA4Gv2, download breed-mt7621-pbr-m1.bin 
    After successfully Telnet 192.168.31.1, use ftp app, copy it over to router's tmp folder.
    
    3. run the following script
    mtd -r write /tmp/breed-mt7621-pbr-m1.bin Bootloader
```

![image](https://user-images.githubusercontent.com/117250841/200181357-1ba254f4-51ad-4442-974e-de9b38519f25.jpeg)

###  Step#3 How to Use BREED web to flash Openwrt Firmware(bin) onto R4AGv2 router?

```
1. Connect Ethernet Cable to LAN port rather than WAN port.
2. Hold & press reset, power on
3. Open browser, go to 192.168.1.1 for Breed Web Page
4. Flash these two bin files together at the same time! 
    - eeprom.bin
    - openwrt-ramips-mt7621-xiaomi_mi-router-4a-gigabit-v2-squashfs-sysupgrade.bin
```
![image](https://user-images.githubusercontent.com/117250841/200181428-b9628072-b1d6-4f23-a799-3b06c63c12bb.png)
![image](https://user-images.githubusercontent.com/117250841/200880220-1ed7a02a-c35b-4cd6-ba71-a6c85778a572.jpeg)

```
1. Connect Ethernet Cable to LAN port rather than WAN port
2. PLEASE NOTE: router IP might be changed to 192.168.31.1
3. Default wifi SSID: WiFi_R4AGv2, Defulat wifi Key: password
4. If Router doesn't have DHCP enabled, you will need to manually assign IP to talk to your router:
For example:
    If router's IP is 192.168.N.2
    Manually assign your computer's ip to be: 
    192.168.N.88
    255.255.255.0
```

## How to Fix Breed Endless reboot (infinite bootloop)
Breed doesn't officially support R4A that might cause endless reboot to 192.168.1.1

### Telnet to Breed
 - Chinese users can refer https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8298740&page=1&extra=#pid18865861
 - Hold & press reset, power on, boot into Breed, then open cmd or terminal to telnet breed,
 - Use command: telnet 192.168.1.1,
 - Setup your local webserver, if your local machine address is 192.168.1.2 port:8080, place the sysupgrade bin at local webserver folder,
 
 - Use command to copy bin over to router: wget http://192.168.1.2:8080/sysupgrade.bin
 - ![Connecting to 192 168 1 28080  connected](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/8604b1e3-045a-4e11-b1f0-e856cbe7abce)

 - Check and Verify the bin file size, Length: 10749035/0xA4046b (10MB), Saving to address 0x80001000
 - You MUST erase Enough Space for the bin file size from 0x180000 to 0xA80000.,Why 0xA80000? Big enought for 0xA4046b. Check yours! 
 - 0x180000 is set as the address where xiaomi would boot from in this case,
 - ![0x000000000000-0x000000030000 u-boot](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/e460c611-ccd2-4398-89e4-844cfc8032a7)

 - Use command: flash erase 0x180000 0xA80000
 - ![Pasted Graphic 1](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/5276b704-6fa5-41c2-b0b3-ad86447c91d7)
 
 - Use Command：flash write 0x180000 0x80001000 0xA80000
 - (Write from 0x180000 address, use data previously wget(saved) at 0x80001000, given writing space with size of 0xA80000)
 - ![Trying to boot firmware from 0x00180000 in flash bank 0 ](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/1a1aea69-10ad-4e9f-8062-7b763c6dad8e)
 
 - Use Command：boot flash 0x180000  (Boot from 0x180000)

### Next Restart to breed Web interface
 - You need to enable environment configuration:
 - ![159201975-98004f5e-14d9-42dd-a103-4e4fc4f924ec](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/f4f65dcb-7fc1-41cb-8161-86f5c9ddda9a)

 - Restart again to breed Web interface,then you can add autoboot.command，and value: boot flash 0x180000
 - ![159202017-cc32b481-e048-4fa1-85cb-97ec550e10f5](https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2/assets/117250841/3f816b1c-7577-4ce6-95c8-3dc3678d6137)

 - This is to tell breed boot system from 0x180000.
### If you want to flash New Openwrt Bin, you have to repeat above methods from..telnet and commands again.





