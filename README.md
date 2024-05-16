
# Kickstart PXE Boot Server with Apache and DHCP

This setup allows for provisioning virtual machines (VMs) using Kickstart technology, reducing setup time and manual intervention. It involves configuring DHCP and Apache to manage IP assignment and host kickstart files, respectively.

## Prerequisites

-   VMware Workstation
-   Empty VM and VM with a kickstart file
-   Apache web server
-   DHCP server
-   Kickstart file

## Setup

### VMware Configuration

1.  Create a host-only network (e.g., 192.168.42.0/24)
2.  Disable the local DHCP server in VMware

### VM1 (Server)

1.  Attach only the host-only adapter to VM1
2.  Create a connection for the host-only adapter:
```
sudo nmcli con add con-name internal type ethernet ifname ens192 ip4 192.168.42.1/24
```

3.  Install DHCP and Apache:


```
yum install dhcp-server httpd
```

4.  Create the kickstart file using the Red Hat web application or manually.


5.  Start the Apache server and host the kickstart file:


```
systemctl enable --now httpd  
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
 vi /var/www/html/ks.cfg
 ```

6.  Configure the DHCP server:


```
option domain-name "amr.com"; option domain-name-servers 4.4.2.2, 8.8.8.8; default-lease-time 86400; max-lease-time 86400; authoritative; log-facility local7;   subnet 192.168.42.0 netmask 255.255.255.0 {
 range 192.168.42.100 192.168.42.200; }
 ```

7.  Start the DHCP server:

```
systemctl enable --now dhcpd
firewall-cmd --add-service=dhcp --permanent
firewall-cmd --reload
```

### VM2 (Empty)

1.  Attach the installation ISO to VM2
2.  During boot, press the Tab key and type `inst.ks=http://192.168.42.1/ks.cfg` to initiate the Kickstart installation

You can monitor the DHCP leases and installation progress by checking the DHCP logs:
```
cat /var/lib/dhcpd/dhcpd.leases
```
