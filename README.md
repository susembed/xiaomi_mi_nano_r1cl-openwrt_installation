### More detail intruction to install OpenWrt on Xiaomi Mi WiFi Nano / Youth (R1CL) router
1. Login, manual update firmware to development ver (miwifi_r1cl_all_59371_2.1.26.bin file)
2. Login or setup admin password, copy value stok=<>
3. Exploit, backup original firmware and upload OpenWrt binary file: Choose a method
  Method a: change ssh password
        a1. run on a Linux client machine: curl -d "oldPwd=00000000&newPwd=00000000" "http://192.168.31.1/cgi-bin/luci/;stok=<replace_me>/api/xqsystem/set_name_password"
        a2. check ssh is running, password is value of newPwd= above. If no ssh server is run, go to method b.
        a3. Back up stock firmware:
                a3.1. Dump flash: dd if=/dev/mtdblock0 of=/tmp/all.bin
                a3.2. From a Linux client: scp root@192.168.31.1://tmp/all.bin
                a3.3. Check md5sum to makesure no corruption on backup file.
                a3.4. Remove file: rm /tmp/all.bin
        a4. Upload OpenWrt to device : scp ./openwrt-23.05.0-ramips-mt76x8-xiaomi_miwifi-nano-squashfs-sysupgrade.bin root@192.168.31.1//tmp/

  Method b: start tenetd with command injection
        b1. On address bar of browser after login, replace part behide stok=<long_hex_value> with /api/xqnetwork/set_wifi_ap?ssid=whatever&encryption=NONE&enctype=NONE&channel=1%3B%2Fusr%2Fsbin%2Ftelnetd
        b2. telnet to devices user=root, pwd=<admin_dashboard_pwd>
        b3. Back up stock firmware:
                b3.1. Dump flash: dd if=/dev/mtdblock0 of=/tmp/all.bin
                b3.2. Start nc server on a linux client: nc -l  -p 1000 > ./all.bin
                b3.3. Send file to linux client: nc -w 3 192.168.31.x < /tmp/all.bin
                b3.4 Check md5sum to makesure no corruption on backup file.
                b3.5. Remove file: rm /tmp/all.bin
        b4. Send openwrt firmware to device:
                b4.1. Start nc server on device: nc -l  -p 1000 > ./openwrt.bin
                b3.3. Send file to device: nc -w 3 192.168.31.1 < ./openwrt-23.05.0-ramips-mt76x8-xiaomi_miwifi-nano-squashfs-sysupgrade.bin
4 Check md5sum to makesure no corruption on openwrt.bin.
5. Flash: mtd -r write /tmp/openwrt.bin firmware, device will reboot after writing done.
6. Check result at 192.168.1.1.
