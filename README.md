### More detail intruction to install OpenWrt on Xiaomi Mi WiFi Nano / Youth (R1CL) router
This is based on my experience, original intruction from: https://openwrt.org/toh/xiaomi/miwifi_nano#installation_via_ssh
#### 1. Login, manual update firmware to development ver (miwifi_r1cl_all_59371_2.1.26.bin file)
#### 2. Login or setup admin password, copy value stok=<very_long_hex_string> on address bar of browser.
#### 3. Exploit, backup original firmware and upload OpenWrt binary file: Try method a first.
- Method a: change ssh password
a1. On a Linux client machine:

```bash
curl -d "oldPwd=00000000&newPwd=00000000" "http://192.168.31.1/cgi-bin/luci/;stok=<replace_me>/api/xqsystem/set_name_password"
```

a2. ssh into router ```ssh root@192.168.31.1 ```, password is value of newPwd= above, in my case is 00000000. My experience with many devices, ssh server sometimes doens't run and it return error when try to connect. Then, go to method b.

a3. Back up stock firmware:

- a3.1. Dump flash: 
```bash
dd if=/dev/mtdblock0 of=/tmp/all.bin
```
- a3.2. From a Linux client: 
```bash
scp root@192.168.31.1://tmp/all.bin
```
- a3.3. Check md5sum to makesure no corruption on backup file.
```bash
# On router
md5sum /tmp/all.bin
# On computer
md5sum ./all.bin
```
- a3.4. Remove file: rm /tmp/all.bin
a4. Upload OpenWrt from computer to device : 
```bash
scp ./openwrt-23.05.0-ramips-mt76x8-xiaomi_miwifi-nano-squashfs-sysupgrade.bin root@192.168.31.1//tmp/
```

Method b: start tenetd with command injection
/// Credit: https://openwrt.org/toh/xiaomi/miwifi_mini#quick_openwrt_installation
b1. On address bar of browser after login to dashboard, replace part behide 
``` stok=<long_hex_value>``` with 

```
/api/xqnetwork/set_wifi_ap?ssid=whatever&encryption=NONE&enctype=NONE&channel=1%3B%2Fusr%2Fsbin%2Ftelnetd
```
b2. telnet to devices user=root, pwd=<admin_dashboard_pwd>

b3. Back up stock firmware:

- b3.1. Dump flash: 
```bash
dd if=/dev/mtdblock0 of=/tmp/all.bin
```

- b3.2. Start nc server on a linux client:
```bash
nc -l  -p 10000 > ./all.bin
```
- b3.3. Send file to linux client:
```bash
nc -w 3 192.168.31.x  10000< /tmp/all.bin
```
with 192.168.31.x is IP addr of your computer.
- b3.4 Check md5sum to makesure no corruption on backup file.

- b3.5. Remove file on the router, exactly to step ```a3.3```.
```bash
rm /tmp/all.bin
```
b4. Send openwrt firmware to device, similar to step b3.2 and b3.3 :
- b4.1. Start nc server on router: 
```bash
nc -l  -p 1000 > ./openwrt.bin
```
- b3.3. Send file to router: 
```bash
nc -w 3 192.168.31.1 1000 < ./openwrt-23.05.0-ramips-mt76x8-xiaomi_miwifi-nano-squashfs-sysupgrade.bin
```
4 Check md5sum to makesure no corruption on openwrt.bin, similar to step ```a3.3```.
5. Flash: 
```bash
mtd -r write /tmp/openwrt.bin firmware
```
, device will reboot when flash finish.
6. Check result at 192.168.1.1.
