# nm-lab-info

## Introduction
This project deploy OpenShift 4.x (4.3 starting) cluster. We will use the following roles:



| Host          | Role                | External        | Baremetal    | Provisioning  |
| ------------- | --------------------|-----------------|--------------|---------------|
| dcl01         | bastian             | 36.101.16.26/16 | 192.168.1.26 | 172.22.0.1/24 |   
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
| 

Details of the servers such as MAC addresses, ports etc is contained in https://github.com/ajaysimha/webscale-nm/blob/master/server-info.md


## Subnets
IP Range: 36.101.80.1 - 36.101.99.255
Gateway: 36.101.255.1.

## Prerequisites

Reserved IP

| Usage    |      Hostname        |  IP Address       |
|----------|----------------------|-------------------|
| Nameserver | ns1.\<cluster-name\>.\<domain\>     | \<ip\> |
| Master-0 | master-0.\<cluster-name\>.\<domain\>      | \<ip\> |
| Master-1 | master-1.\<cluster-name\>.\<domain\>   | \<ip\> |
| Master-2 | master-2.\<cluster-name\>.\<domain\>   | \<ip\> |
| Worker-0 | worker-0.\<cluster-name\>.\<domain\>  | \<ip\> |
| Worker-1 | worker-1.\<cluster-name\>.\<domain\>  | \<ip\> |
| API | api.\<cluster-name\>.\<domain\>   | 192.168.68. |
| API (internal) | api-int.\<cluster-name\>.\<domain\>   | \<ip\> |
| Ingress LB (apps) |  *.apps.\<cluster-name\>.\<domain\>    | \<ip\> |
| Bastion  | dcl01          | 192.168.68.192/27 |
