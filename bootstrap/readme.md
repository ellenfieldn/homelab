# Bootstrap

The goal of this section is to take care of the manual setup steps required to provision the rest of the infrastructure.

## End-Result

The end result of the bootstrap process should be:

- A WAN router of some sort that supports IPv4 NAT and IPv6 prefix delegation
- Basic firewalling capability
- Ability to do DHCP, DNS, TFTP, PXE, etc.
- Ability to easily provision any foundational bare-metal services -- including most or all of the above

### My Setup

To achieve the above goals, I am using:

- Ventoy to create a USB drive to setup baremetal machines.
- VyOS for all the routing, firewalling, DHCP/DNS/TFTP, etc.
  - Before digging deeper into setting up my home network, I was already using VyOS as my router, so I will just continue using it for now to make my life easier.

### Other Options

Other approaches could include:

- Running a local container with dnsmasq to setup pxe boot with netboot.xyz to provision bare metal
- Some combination of your ISP router, OpenWrt, pfSense, OPNsense, dnsmasq, keadhcp, bind, etc for the rest of the networking functionality.

## Setup Ventoy to provision bare metal services

1. Create a flashdrive with ventoy and the following contents
   - VyOS iso
1. Optionally also add additional ISOs:
   - Talos metal-amd64 iso
   - Hypervisor such as proxmox or XCP-ng
   - OS images for any bare-metal work station / personal OS
   - Live images for troubleshooting / debugging / fun and profit
1. Setup VyOS - It will probably take a while for me to flesh this out.
