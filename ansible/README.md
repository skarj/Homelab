## Network Boot for Worker Nodes
Network boot combining PXE (Preboot Execution Environment) and iSCSI
https://zenn.dev/korosuke613/articles/raspberry-pi-pxe-iscsi-boot?locale=en

- Root volume is mounted using iSCSI, but /boot/firmware is mounted TFTP directory using NFS
- The Pi powers on, finds no SD card, and broadcasts a DHCP request on the network.
- DHCP server replies with an IP address and says to go to TFTP server to get boot files.
- Once the kernel is running in RAM, it reads the cmdline.txt and connects to the iSCSI server to mount the root (/) filesystem.

### The role of the NFS server — Why is /boot/firmware not iSCSI?
The iSCSI LUN appears to the NAS as a raw block device, and its internal filesystem cannot be accessed.
Since the TFTP server (dnsmasq) delivers boot files from a directory on the NAS's filesystem, even if you place boot files inside the iSCSI LUN, they cannot be served via TFTP.
Therefore, we export the TFTP directory via NFS and mount it as the Pi's /boot/firmware. When the kernel is updated via apt upgrade on Raspberry Pi OS, the new kernel image and initramfs are written to /boot/firmware.
Since this is directly reflected in the TFTP directory via NFS, the Pi automatically boots with the updated kernel from the next reboot onwards.

## Some Notes:
1. Control plane node uses custom Linux kernel built with iSCSI support and Kernel Features
3. DHCP configured at /etc/dhcp/dhcpd.conf
2. IP forwading and masquarading should be enabled on the control plane node

## Running Ansible
```
ansible-playbook playbook.yml --limit workers --diff --check
ansible-playbook playbook.yml --limit master --diff --check
```

## iSCSI (TODO validate)
```
sudo apt install open-iscsi
systemctl start open-iscsi

sudo touch /etc/iscsi/iscsi.initramfs
sudo update-initramfs -u

sudo iscsiadm -m discovery -t sendtargets -p 192.168.50.1 192.168.50.1:3260,1 iqn.2025-09.com.rpi1:storage.piserver
sudo iscsiadm -m node -l -T iqn.2025-09.com.rpi1:storage.piserver -p 192.168.50.1

# inform initramfs to include the iSCSI module by creating the following file:
sudo cat /etc/iscsi/initiatorname.iscsi
```

## Remove non-Rpi5 kernel
```
sudo apt remove --purge linux-image-rpi-v8 linux-headers-rpi-v8
```

## Check iSCIS units after Kernel rebuild
```
systemctl --failed
```

## TODO
- try NVMe/TCP
- dhcp configuration to ansible
