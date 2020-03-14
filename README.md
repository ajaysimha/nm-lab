# nm-lab-info

## Introduction
This project deploy OpenShift 4.x (4.3 starting) cluster. We will use the following roles:



| Host/VIP      | Role                | External        | Baremetal    | Provisioning  |
| ------------- | --------------------|-----------------|--------------|---------------|
| dcl01         | bastion             | x.x.16.26/16    | 192.168.1.26 | 172.22.0.1/24 |   
| dcl02         | master-0            | none            | 192.168.1.10 | dhcp          |
| dcl03         | master-1            | none            | 192.168.1.11 | dhcp          |
| dcl04         | master-2            | none            | 192.168.1.12 | dhcp          |
| dcl05         | worker-13           | none            | 192.168.1.13 | dhcp          |
| dcl06         | worker-14           | none            | 192.168.1.14 | dhcp          |
| dcl07         | worker-15           | none            | 192.168.1.15 | dhcp          |
| dcl08         | worker-16           | none            | 192.168.1.16 | dhcp          |
| dcl09         | worker-17           | none            | 192.168.1.17 | dhcp          |
| dcl10         | worker-18           | none            | 192.168.1.18 | dhcp          |
| dcstor01      | storage-19          | none            | 192.168.1 19 | dhcp          |
| dcstor02      | storage-20          | none            | 192.168.1.20 | dhcp          |
| dcstor03      | storage-21          | none            | 192.168.1.21 | dhcp          |
| api           | api                 | none            | 192.168.1.50 | none          |
| api-internal  | api-internal        | none            | 192.168.1.51 | none          |
| ingress-lb    | ingress-lb          | none            | 192.168.1.52 | none          |

Details of the servers such as MAC addresses, ports etc is contained in https://github.com/ajaysimha/nm-lab/blob/master/server-info.md




