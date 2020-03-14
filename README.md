# nm-lab-info

## Introduction
This project deploy OpenShift 4.x (4.3 starting) cluster. We will use the following roles:



| Host          | Role               |
| ------------- | ------------------ |
| dcl01         | Bastian            |
| dcl02         | Master1            |
| dcl03         | Master2            |
| dcl04         | Master3            |
| dcl05         | Worker1            |
| dcl06         | Worker2            |
| dcl07         | Worker3            |
| dcl08         | Worker4            |
| dcl09         | Worker5            |
| dcl10         | Worker6            |
| dcstor01      | Storage1           |
| dcstor02      | Storage2           |
| dcstor03      | Storage3           |

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
| Bastion  | pool4-infra          | 192.168.68.192/27 |
