# Brocade Switch Notes

- Firmware installation and configuration instructions from: https://github.com/Fohdeesha/lab-docu

## Brocade prerequisites

- Have brocade firmware downloaded
- SCP over to TFTP server: `scp -r brocade-08-03-2024\TFTP-Content\* ellenfieldn@10.36.42.1:/config/tftp/brocade`

## Configure Switches

### ICX6430-24p Setup

#### Fan Mod

Fan noise was really annoying, so replaced the stock fans with Arctic A4028-6K

- Brocade uses a 3-pin connector, but the 4-pin connector on the Arctic is physically compatible.
- Swap the 1st and 3rd pins on the Arctic fan to match the pinout on the Brocade
- Arctic fans were quieter than stock and had a much less annoying noise profile. Still not silent.
- Airflow and pressure are lower, so may be insufficient for higher workloads.

#### Firmware Update

This part is unfortunately manual.

- Connect to serial port, start switch and drop into bootloader by spamming `b`
- Configure to pull from TFTP
  ```sh
  setenv ipaddr 10.36.42.5
  setenv netmask 255.255.255.0

  #tell the switch the IP of your tftp server:
  setenv serverip 192.168.1.8
  setenv image_name brocade/ICX64xx/ICX64S08030u.bin
  setenv uboot brocade/ICX64xx/kxz10105.bin
  ```
- Update OS
  ```sh
  update_primary
  ```
- Update Bootloader
  ```sh
  update_uboot
  ```
- Perform a factory reset
  ```sh
  setenv ipaddr
  saveenv
  factory set-default
  ```
- Reset switch
  ```sh
  reset
  ```

#### Initial Configuration

- Plug into management port
- Disable DHCP and use static IP
  ```sh
  enable
  conf t
  no ip dhcp-client enable
  ip address 10.36.42.5/24
  ip default-gateway 10.36.42.1
  write mem
  ```

#### Update POE Firmware

Run the following:

```sh
inline power install-firmware stack-unit 1 tftp 10.36.42.1 brocade/ICX64xx/icx64xx_poeplus_02.1.0.b004.fw
# After a few seconds, hit enter to return to cli and save changes
write memory
# POE firmware update can take up to 10 minutes. Use show log to monitor progress and reload after 100%
show log
reload
```

#### Configure SSH

```sh
crypto key zeroize
crypto key generate rsa modulus 2048
username ellenfieldn password yourpasshere
aaa authentication login default local
aaa authentication web default local
no telnet server
no web-management http
```

#### POE Settings

For now, just entered these manually to quickly test some POE cameras.

```sh
no legacy-inline-power
interface ethernet 1/1/7
inline power
exit
interface ethernet 1/1/9
inline power
exit
interface ethernet 1/1/11
inline power
exit
interface ethernet 1/1/13
inline power
exit
interface ethernet 1/1/15
inline power
exit
```
