## Obtaining BMC MAC Addresses
To obtain BMC MAC address use:

```
[asimha@dln4 ~]$ ipmitool lan print -I lan -U admin -P P@ssw0rd -H dcl01i 
Set in Progress         : Set Complete
Auth Type Support       : MD5 PASSWORD 
Auth Type Enable        : Callback : MD5 PASSWORD 
                        : User     : MD5 PASSWORD 
                        : Operator : MD5 PASSWORD 
                        : Admin    : MD5 PASSWORD 
                        : OEM      : 
IP Address Source       : DHCP Address
IP Address              : 36.101.116.26
Subnet Mask             : 255.255.0.0
MAC Address             : a4:bf:01:34:36:04
SNMP Community String   : public
IP Header               : TTL=0x40 Flags=0x40 Precedence=0x00 TOS=0x10
BMC ARP Control         : ARP Responses Enabled, Gratuitous ARP Disabled
Gratituous ARP Intrvl   : 2.0 seconds
Default Gateway IP      : 36.101.255.1
Default Gateway MAC     : 00:00:00:00:00:00
Backup Gateway IP       : 0.0.0.0
Backup Gateway MAC      : 00:00:00:00:00:00
802.1q VLAN ID          : Disabled
802.1q VLAN Priority    : 0
RMCP+ Cipher Suites     : 1,2,3,6,7,8,11,12,15,16,17
Cipher Suite Priv Max   : XXXXXXXXXXaXXXX
                        :     X=Cipher Suite Unused
                        :     c=CALLBACK
                        :     u=USER
                        :     o=OPERATOR
                        :     a=ADMIN
                        :     O=OEM
Bad Password Threshold  : 3
Invalid password disable: yes
Attempt Count Reset Int.: 30
User Lockout Interval   : 10
```
