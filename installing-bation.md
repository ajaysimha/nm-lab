# Installing Bastion for OpenShift 4.3 IPI install
## BIOS (Drac/ILO Settings)
Please reference the sub-sections below to prepare the hardware for install.

## Disable IDRAC Passthrough (Dell Servers)
You can either do this via the cli or via webUI. See below for more information.
** These are not Dell Servers and don't apply **

## Ensure Disks are raided
** We will try without RAID initially **

## Installing RHEL 8 on Bastion Host
- Done

```
[root@dcl01 ~]# lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0  1.1T  0 disk 
├─sda1          8:1    0  600M  0 part /boot/efi
├─sda2          8:2    0    2G  0 part /boot
└─sda3          8:3    0  1.1T  0 part 
  ├─rhel-root 253:0    0    1T  0 lvm  /
  ├─rhel-swap 253:1    0   10G  0 lvm  [SWAP]
  └─rhel-home 253:2    0  1.1T  0 lvm  /home
sdb             8:16   0  1.1T  0 disk 
└─sdb1          8:17   0  1.1T  0 part 
  └─rhel-home 253:2    0  1.1T  0 lvm  /home
```

Set Root Password
During OS installation, set the root password to the standard password used in your environment.

Configure Initial IP address on Primary Interface (ens3f0)
Set IP address, netmask, and default route, and resolver (96.239.250.47 - In Tampa Lab) on ens3f0.

Reboot
Once RHEL 8 has installed, the bastion node will reboot. You should be able to SSH directly to the node via its IP address on the baremetal network.

Register the Bastion Node with Satellite
Configure the bastion host to use local Satellite server.  Run these as root or with sudo
# rpm -ihv http://tpavcpsat.vici.verizon.com/pub/katello-ca-consumer-latest.noarch.rpm
# subscription-manager register --org="Corona" --activationkey="OSP16_SandBox_AK"
# subscription-manager repos --disable='*' --enable='rhel-8-for-x86_64-baseos-rpms' --enable='rhel-8-for-x86_64-appstream-rpms' --enable='ansible-2.8-for-rhel-8-x86_64-rpms'

Install additional packages
Install libvirt and required packages Run these as root or with sudo.
# dnf install -y libvirt qemu-kvm mkisofs python3-devel jq ipmitool tmux tar bind-utils dnsmasq

Add the Corona User
Create user corona, allow passworless sudo and create a SSH key.
useradd corona
passwd # Use WebScale1
echo "corona ALL=(root) NOPASSWD:ALL" | tee -a /etc/sudoers.d/corona
chmod 0440 /etc/sudoers.d/corona
su - corona -c "ssh-keygen -t rsa -f /home/corona/.ssh/id_rsa -N ''"

Add user corona to libvirt group
usermod --append --groups libvirt corona

Configure Host Networking
Via NetworkManager, create two bridges: provisioning and baremetal. Add corresponding interfaces to the bridges. Be aware you may be disconnected. You should run this command in a tmux session to ensure that the script completes.
nohup bash -c '
    export PUB_CONN=ens1f0
    export PROV_CONN=eno1
    export PUB_IP=10.75.68.197/27
    export PUB_GW=10.75.68.193
    export PUB_DNS=96.239.250.47
    nmcli con down "$PROV_CONN"
    nmcli con down "$PUB_CONN"
    nmcli con delete "$PROV_CONN"
    nmcli con delete "$PUB_CONN"
    nmcli con down "System $PUB_CONN"
    nmcli con delete "System $PUB_CONN"
    nmcli con down "Wired connection 1"
    nmcli con down "Wired connection 2"
    nmcli con down "virbr0"
    nmcli con delete "virbr0"
    nmcli con delete "Wired connection 1"
    nmcli con delete "Wired connection 2"
    nmcli connection add ifname provisioning type bridge con-name provisioning
    nmcli con add type bridge-slave ifname "$PROV_CONN" master provisioning
    nmcli connection add ifname baremetal type bridge con-name baremetal
    nmcli con add type bridge-slave ifname "$PUB_CONN" master baremetal
    nmcli con down "$PUB_CONN"; nmcli connection modify baremetal ipv4.addresses ${PUB_IP} ipv4.dns ${PUB_DNS} ipv4.gateway ${PUB_GW} ipv4.method manual
    nmcli connection modify provisioning ipv4.addresses "172.22.0.1/24" ipv4.method manual
    nmcli con down provisioning
    nmcli con up provisioning
    nmcli con down baremetal
    nmcli con up baremetal
    '
Your connections should appear as below.
[corona@bastion ~]$ nmcli d
DEVICE        TYPE      STATE         CONNECTION          
baremetal     bridge    connected     baremetal           
provisioning  bridge    connected     provisioning        
eno1          ethernet  connected     bridge-slave-eno1   
ens1f0        ethernet  connected     bridge-slave-ens1f0 
ens1f1        ethernet  disconnected  --                  
ens3f0        ethernet  disconnected  --                  
ens3f1        ethernet  disconnected  --               

Setup Chrony
Use the following internal ntp/chrony server 96.239.250.57 or 96.239.250.58.
See sample config below
server 96.239.250.57 iburst

Restart chronyd
systemctl restart chronyd
Verify chrony is syncing
[root@bastion ~]# chronyc sources
210 Number of sources = 2
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^+ tpavicad3.vici.verizon.c>     3   6    37     1  -3868us[-3868us] +/-  125ms

Set hostname on the bastion host.
Use hostname-ctl to set the hostname of the bastion host.
# hostnamectl set-hostname bastion.stablrebco2.vici.verizon.com

Verify hostname has been set properly
# hostnamectl
   Static hostname: bastion.stablcured.vici.verizon.com
         Icon name: computer-server
           Chassis: server
        Machine ID: d96fec2a81f54bf98447c9446de1a229
           Boot ID: 8285937af82f404980f57da232468008
  Operating System: Red Hat Enterprise Linux 8.1 (Ootpa)
       CPE OS Name: cpe:/o:redhat:enterprise_linux:8.1:GA
            Kernel: Linux 4.18.0-147.el8.x86_64
      Architecture: x86-64


Configure Firewalld
As shown below, open the required ports with firewalld
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --zone=public --add-service=dhcp --permanent
firewall-cmd --add-port=5000/tcp --zone=libvirt  --permanent
firewall-cmd --add-port=5000/tcp --zone=public   --permanent
firewall-cmd --reload

Enable and start libvirt service, create default storage pool and delete  default network
systemctl enable --now libvirtd
virsh pool-define-as --name default --type dir --target /var/lib/libvirt/images
virsh pool-start default
virsh pool-autostart default
virsh net-destroy default
virsh net-undefine default

Copy the pull secret file as /home/corona/pull-secret.json

Download a copy of your pull secret from [here] (https://cloud.redhat.com/openshift/install/metal/user-provisioned), or obtain directly from your local administrator.

Setup DHCP via DNSMASQ for Baremetal Nodes
In the Tampa Lab we are using dnsmasq to set static IP addresses on our OpenShift Nodes.
Create the DHCP configuration file in the file below.
/etc/dnsmasq.d/baremetal.conf
Include all master and worker nodes in your config. Obtain the MAC addresses (2) for the ports that will be used as the baremetal interfaces.
On HP nodes - capture the MAC addreses of the first two intel xxv710 ports - you can obtain this information via the ILO.
On Dell nodes - capture the MAC addresses of the Mellanox CX5 ports - you should be able to obtain this information via the DRAC or at boot time.
# dhcp for baremetal network
  no-dhcp-interface=lo
interface=baremetal
  dhcp-range=set:baremetal,10.75.68.198,10.75.68.222,24h
  dhcp-option=tag:baremetal,option:netmask,255.255.255.224
  dhcp-option=tag:baremetal,option:router,10.75.68.193
  dhcp-option=tag:baremetal,option:dns-server,96.239.250.47
  dhcp-option=tag:baremetal,option:ntp-server,96.239.250.57

# Master Nodes
  dhcp-host=48:df:37:4d:8f:c4,48:df:37:4d:8f:c5,master-0,10.75.68.198,set:baremetal
  dhcp-host=48:df:37:4d:91:88,48:df:37:4d:91:89,master-1,10.75.68.199,set:baremetal
  dhcp-host=48:df:37:4d:91:28,48:df:37:4d:91:29,master-2,10.75.68.200,set:baremetal

Enable and start dnsmasq
systemctl enable --now dnsmasq

Next Steps
Proceed to Obtaining the OC Binary and Baremetal Image
