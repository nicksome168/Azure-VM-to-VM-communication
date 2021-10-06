# Azure-VM-to-VM-communication
Demonstrate provisioning Linux and Windows two Azure VMs in two different VNets and connect two VNets with virtual network peering. Lastly, Remote connect to two VMs in terminal and ping each other. <br>

In this tutorial, you learn how to:
- [] Deploy a virtual machine (VM) into each virtual network
- [] Connect two virtual networks with a virtual network peering
- [] Remote connect to VMs
- [] Communication between VMs 

## Environment
On-Premise
- Env1: MacOS 11.6 MBA M1(2021)
- Env2: Microsoft 10 Lenova

Cloud
- Azure portal

## Scenario
Many enterprises split VMs across differnt VNets for secruity and high availability reasons. However, this creates difficulties in VM-to-VM communication. There are three ways that you connect applications in two different Vnets:<br>
- Route via the Internet
- VNet peering
- Site-to-site VPN<br>

In this tutorial, I will demonstrate **VNet peering**.

## Steps
1. Create Linux VM in Vnet1
2. Create Windows VM in Vnet2
3. Connect two virtual networks with a virtual network peering
4. Remote connect to Linux VM and ping to aother VM
5. Remote connect to Windows VM and ping to aother VM
6. Clean all resources

## Create Linux VM in Vnet1
Enter Azure portal and type "virtual machines" in the search bar<br>
[Picture]

### a. Basics
- Click Create button and a new panel will show. Enter "Resource Group", "Virtual machine name", "Region", "Image". For demo purpose, choose *Password* in "Authentication type" and type in "Username" and "Password" which later will be used in remote connect<br>
[Picture]

### b. Disks
- leave all settings default<br>
[Picture]

### c. Networking
- Select *Advanced* in "NIC network security group" and click *Create new* button in "Configure network security group"<br>
[Picture]
- Click *Add an inbound rule* in "Inbound rules" and a new panel will show up. Select *ICMP* in "Protocol" and edit "Name" to *Port_ICMP*<br>
[Picture]

### d. Management
- leave all settings default<br>
[Picture]

### e. Managemnet
- leave all settings default<br>
[Picture]

### f. Review
- Confirm that all settings are correct and click *Create*<br>
[Picture]

## Create Windows VM in Vnet2
### a. Basics
- Click Create button and a new panel will show. Choose the same "Resource Group" you just created. Enter "Virtual machine name", "Region", "Image". Type in "Username" and "Password" which later will be used in remote connect<br>
[Picture]

(The repeated steps are omitted. Only the different ones are shown below)<br>

### c. Networking
- By default, VMs in the same security group use the same Vnet. Hence, you need to create a new one manually. Click *Create new* in "Virtual network", type "Name" and click *OK*<br>
[Picture]

## Connect two virtual networks with a virtual network peering
- Enter Azure portal and type the name of Vnet1 (here here is *test-1006-vnet*) in the search bar<br>
[Picture]
- Click *Peerings* on the sidebar. Then click *Add* on the top left corner<br>
[Picture]
- Since peering link is reciprocal, two links will be created. Type in "Peering link name" in "This virtual network" section and "Peering link name" in Remote virtual network section. Then, select Vnet2 (here is *test-1006-vnet2*) in "Virtual network"<br>
[Picture]

## Remote connect to Linux VM and ping to aother VM
1. Enter Azure portal and type *Resource groups* in the search bar<br>
[Picture]
2. Click the resource group you just created (here is *test-1006*) and click the Windows VM (here is *test-1001-windows*)<br>
[Picture]
3. Copy *Private IP address* under "Networking" property (here is *10.2.0.4*). Leter you will ping to this private IP on Linux VM<br>
[Picture]
4. Go back to resource group and click Linux VM (here is *test-1001-linux*)<br>
[Picture]
5. Click *Connect* on the top left corner and click *SSH*<br>
[Picture]

### a. Env1: MacOS
- Since no credentials is created in provision, you can directly connect with command in Terminal without private key<br>
`ssh <your-linux-vm-name>@<your-linux-vm-public-ip>`<br>
[Picture]
- Type command and you will see the messages has been trasmitted and received<br>
`ping <windows-vm-private-ip>`<br>
[Picture]

### b. Env2: WindowsOS
- WindowsOS by default does not have SSH connection port, so you have to either download [OpenSSH](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse) or [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or other clients. Since no credentials is created in provision, you can directly connect without private key.
[Picture]
- [Putty]: Enter "Host Name" and "Port". Enter account and password<br>
[Picture]
- [OpenSSH]: Enter the following command in PowerShell<br>
`ssh -p 22 <your-linux-vm-name>@<your-linux-vm-public-ip>`<br>
[Picture]
- Type command and you will see the messages has been trasmitted and received<br>
`ping <windows-vm-private-ip>`<br>
[Picture]

## Remote connect to Windows VM and ping to aother VM
1. Repeat steps 1-5 in the previous section, but this time you will get Linux VM's private IP (here is *10.1.0.4*) in step 1-3 and click *RDP* in step 5<br>
[Picture]
[Picture]

### a. Env1: MacOS
- [SSH]: You need to first connect to the VM with other clients and have an SSH Server, such as Open SSH, installed on it. See the [doc](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse) for SSH Server installation on VM.
- [Virtual Desktop Client]:
    - Download [Microsoft Remote Desktop Client](https://apps.apple.com/tw/app/microsoft-remote-desktop/id1295203466?l=en&mt=12)<br>
    [Picture]
    - Click the downloaded *.rdp* file and it will open *Microsoft Remote Desktop Client*<br>
    [Picture]
    - Type in account and password that you previously set<br>
    [Picture]
    - Connect to the Windows VM<br>
    [Picture]
    - Open Powershell and type command and you will see the messages has been trasmitted and received<br>
    `ping <linux-vm-private-ip>`<br>
    [Picture]

### b. Env2: WindowsOS
- [Virtual Desktop Client]:
    - Click the downloaded *.rdp* file and it will open *Remote Desktop Connection*<br>
    [Picture]
    - Click *more option* and type in the account and password that you previously set<br>
    [Picture]
    - Connect to the Windows VM<br>
    [Picture]
    - Open Powershell and type command and you will see the messages has been trasmitted and received<br>
    `ping <linux-vm-private-ip>`<br>
    [Picture]

## Clean all resources
- Enter Azure portal and type *Resource groups* in the search bar<br>
[Picture]
- Click the resource group you just created (here is *test-1006*) and click *Delete resource group* on the top. A new panel will show up and type your resource group name under "TYPE THE RESOURCE GROUP NAME"<br>
[Picture]
- Wait for several minutes and go back to resource group to check if all resources are deleted successfully

## Reference Links
[How Can I Connect 2 Azure Virtual Networks?](https://petri.com/can-connect-2-azure-virtual-networks)<br>
[Tutorial: Connect virtual networks with virtual network peering using the Azure portal](https://docs.microsoft.com/en-us/azure/virtual-network/tutorial-connect-virtual-networks-portal)<br>