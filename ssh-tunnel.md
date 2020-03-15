There is a NoMachine NX GUI that is running on the jump host server in the Intel lab that can be used to access the IPMI interface via a browser. The following instructions can be followed to connect to the NX GUI:

- Establish SSH tunnel
  The NX GUI server is listening to port 4000 on the jump host. The command below will open an SSH tunnel to the jump host and map local port 4001 to port 4000 on the jump server:
    ssh abokhari@207.108.8.164 -p 47410 -i ~/.ssh/intel_rsa_abokhari -L 4001:127.0.0.1:4000
    
      * Replace the username and SSH key filename with your own
      
- Use the NoMachine tool to connect to the GUI session on the jump host
  - Download and install NoMachine tool: https://www.nomachine.com
  - Launch the NoMachine tool and configure a connection with the following parameters:
    - Host: localhost
    - Port: 4001
    - Uncheck the "Use UDP Communication" button
    - Leave everything else to default
    
  - Once connected, use your username / password for NX to login
  - Launch the browser and connect to IPMI

** Ajay's ssh config file (Prepared with guidance from Ali :-)
```
Host *
  ServerAliveInterval 240
  ServerAliveCountMax 2
  StrictHostKeyChecking no

Host lab-switch
  Hostname 10.1.251.97
  User asimha

Host nm-jump
  Hostname 207.108.8.164
  IdentityFile ~/.ssh/intel
  User asimha
  Port 47410
  DynamicForward 22222


Host bastion
  Hostname dcl01
  IdentityFile ~/.ssh/id_rsa
  User asimha
  ProxyCommand ssh -CW %h:%p nm-jump
```
