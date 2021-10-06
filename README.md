# Azure-VM-to-VM-communication
Demonstrate provisioning Linux and Windows two Azure VMs in two different VNets and connect two VNets with virtual network peering. Lastly, Remote connect to two VMs in terminal and ping each other. <br>

In this tutorial, you learn how to:
- [x] Deploy a virtual machine (VM) into each virtual network
- [x] Connect two virtual networks with a virtual network peering
- [x] Remote connect to VMs
- [x] Communication between VMs 

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
<img width="719" alt="CreateLinuxVMBasics" src="https://user-images.githubusercontent.com/29058993/136152311-663d7c42-1b1c-43bb-beae-605fdcdf6cd2.png">

### b. Disks
- leave all settings default<br>
<img width="782" alt="CreateLinuxVMDisks" src="https://user-images.githubusercontent.com/29058993/136152328-56465343-2c74-49ad-b1ec-963c5ef35370.png">

### c. Networking
- Select *Advanced* in "NIC network security group" and click *Create new* button in "Configure network security group"<br>
<img width="666" alt="CreateLinuxVMNetwork-1" src="https://user-images.githubusercontent.com/29058993/136152351-fb40729a-6775-41d3-81f8-eb581f1239ea.png">

- Click *Add an inbound rule* in "Inbound rules" and a new panel will show up. Select *ICMP* in "Protocol" and edit "Name" to *Port_ICMP*<br>
<img width="1649" alt="CreateLinuxVMNetwork-2" src="https://user-images.githubusercontent.com/29058993/136152368-8db80626-f594-41ce-92fb-8dde0569eeee.png">

### d. Management
- leave all settings default<br>
<img width="571" alt="CreateLinuxVMManagement" src="https://user-images.githubusercontent.com/29058993/136152434-5e7184f3-c225-4f3e-9251-764586307c18.png">

### e. Advanced
- leave all settings default<br>
<img width="591" alt="CreateLinuxVMAdvanced" src="https://user-images.githubusercontent.com/29058993/136152535-eb3e1523-5b99-4635-8889-b912b49ce400.png">

### f. Tag
- leave all settings default<br>
<img width="729" alt="CreateLinuxVMTag" src="https://user-images.githubusercontent.com/29058993/136152667-148e4b74-6220-4aca-a79b-e9e8c398ffa0.png">

### g. Review
- Confirm that all settings are correct and click *Create*<br>
<img width="740" alt="CreateLinuxVMReview" src="https://user-images.githubusercontent.com/29058993/136155506-02c67cfb-27db-4a02-8054-01298184a2dd.png">


## Create Windows VM in Vnet2
### a. Basics
- Click Create button and a new panel will show. Choose the same "Resource Group" you just created. Enter "Virtual machine name", "Region", "Image". Type in "Username" and "Password" which later will be used in remote connect<br>
<img width="847" alt="CreateWindowsVMBasics" src="https://user-images.githubusercontent.com/29058993/136155537-171f0731-3da2-480c-a517-38dcab2170f5.png">


(The repeated steps are omitted. Only the different ones are shown below)<br>

### c. Networking
- By default, VMs in the same security group use the same Vnet. Hence, you need to create a new one manually. Click *Create new* in "Virtual network", type "Name" and click *OK*<br>
<img width="1656" alt="CreateWindowsVMNetwork" src="https://user-images.githubusercontent.com/29058993/136152720-a4950adc-1a97-4b7d-8ba5-44ad810ca7b5.png">


## Connect two virtual networks with a virtual network peering
- Enter Azure portal and type the name of Vnet1 (here here is *test-1006-vnet*) in the search bar<br>
<img width="866" alt="AzurePortalSearchVnet1" src="https://user-images.githubusercontent.com/29058993/136152748-c8585c8e-18c3-4b44-b98f-94929b6eb712.png">

- Click *Peerings* on the sidebar. Then click *Add* on the top left corner<br>
<img width="1453" alt="AddPeering-1" src="https://user-images.githubusercontent.com/29058993/136152770-3bb3c2b2-f191-4fa4-b2c6-bdf988af0a7d.png">

- Since peering link is reciprocal, two links will be created. Type in "Peering link name" in "This virtual network" section and "Peering link name" in Remote virtual network section. Then, select Vnet2 (here is *test-1006-vnet2*) in "Virtual network"<br>
<img width="599" alt="AddPeering-2" src="https://user-images.githubusercontent.com/29058993/136152799-5c69d4b2-29af-4d0a-a8ee-fdbd14528d78.png">


## Remote connect to Linux VM and ping to aother VM
1. Enter Azure portal and type *Resource groups* in the search bar<br>
<img width="815" alt="AzurePortalSearchResourceGroups" src="https://user-images.githubusercontent.com/29058993/136152816-0399e9fd-11aa-49c4-9274-c2d785f3e150.png">

2. Click the resource group you just created (here is *test-1006*) and click the Windows VM (here is *test-1001-windows*)<br>
<img width="1662" alt="SelectWindowsVMinResourceGroup" src="https://user-images.githubusercontent.com/29058993/136152857-b2606ca6-7494-4dee-a052-e5fc8d3c1902.png">

3. Copy *Private IP address* under "Networking" property (here is *10.2.0.4*). Leter you will ping to this private IP on Linux VM<br>
<img width="1294" alt="GetWindowsVMPrivateIP" src="https://user-images.githubusercontent.com/29058993/136152882-62e1fe28-f5a9-4564-9212-31e9db52ed07.png">

4. Go back to resource group and click Linux VM (here is *test-1001-linux*)<br>

5. Click *Connect* on the top left corner and click *SSH*<br>
<img width="1280" alt="Connect2LinuxVMonMacOS-0" src="https://user-images.githubusercontent.com/29058993/136152938-994eedd3-5d18-4761-baa3-208aaf7ee922.png">


### a. Env1: MacOS
- Since no credentials is created in provision, just copy the command in "4. Run the example command below to connect to your VM." without the ` -i <private key path>` <br>
<img width="972" alt="Screen Shot 2021-10-06 at 2 42 40 PM" src="https://user-images.githubusercontent.com/29058993/136153155-cad30958-464e-4413-ad89-279383fa0dbf.png">

- Open Terminal on Mac, you can directly connect with the command below in Terminal without private key<br>
`ssh <your-linux-vm-name>@<your-linux-vm-public-ip>`<br>
<img width="550" alt="Connect2LinuxVMonMacOS-1" src="https://user-images.githubusercontent.com/29058993/136155282-8286ecf5-7110-40ea-859d-8c98a8d9e18d.png">

- Type command and you will see the messages has been trasmitted and received<br>
`ping <windows-vm-private-ip>`<br>
<img width="395" alt="Connect2LinuxVMonMacOS-2" src="https://user-images.githubusercontent.com/29058993/136153297-c84a864a-53c0-485b-9d63-f8222f5abbd0.png">


### b. Env2: WindowsOS
WindowsOS by default does not have SSH connection port, so you have to either download [OpenSSH](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse) or [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or other clients. Since no credentials is created in provision, you can directly connect without private key.

- [Putty]: Enter "Host Name" and "Port". Enter account and password<br>
<img width="505" alt="Connect2LinuxVMonWindowsOS-1" src="https://user-images.githubusercontent.com/29058993/136153360-e3323905-56f1-4930-b126-5b51b9383531.png">
<img width="743" alt="Connect2LinuxVMonWindowsOS-2" src="https://user-images.githubusercontent.com/29058993/136153390-0016eb53-520b-41b3-9b06-370ab206d7ab.png">
<img width="667" alt="Connect2LinuxVMonWindowsOS-3" src="https://user-images.githubusercontent.com/29058993/136153427-27e544a0-ca52-448e-b501-2b9db7132ff6.png">
<img width="674" alt="Connect2LinuxVMonWindowsOS-4" src="https://user-images.githubusercontent.com/29058993/136153442-ef12c2ab-e3b3-41a8-ba0d-11a8e823efca.png">

- [OpenSSH]: Enter the following command in PowerShell<br>
`ssh -p 22 <your-linux-vm-name>@<your-linux-vm-public-ip>`<br>
[Picture]
- Type command and you will see the messages has been trasmitted and received<br>
`ping <windows-vm-private-ip>`<br>
<img width="723" alt="ping2WindowsVM" src="https://user-images.githubusercontent.com/29058993/136153772-8f00bf8d-3bc7-4739-ae39-abb287dee4fb.png">



## Remote connect to Windows VM and ping to aother VM
1. Repeat steps 1-5 in the previous section, but this time you will get Linux VM's private IP (here is *10.1.0.4*) in step 1-3 and click *RDP* in step 5<br>
<img width="1257" alt="GetLinuxVMPrivateIP" src="https://user-images.githubusercontent.com/29058993/136153891-3da37c98-4a38-42ed-b2a4-5ba762cb4a24.png">
<img width="902" alt="WindowsVMConnect" src="https://user-images.githubusercontent.com/29058993/136153951-c918377c-27fc-4051-8f78-baf9686ca716.png">

### a. Env1: MacOS
- [SSH]: You need to first connect to the VM with other clients and have an SSH Server, such as Open SSH, installed on it. See the [doc](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse) for SSH Server installation on VM.
- [Virtual Desktop Client]:
    - Download [Microsoft Remote Desktop Client](https://apps.apple.com/tw/app/microsoft-remote-desktop/id1295203466?l=en&mt=12)<br>
    <img width="890" alt="MacRemoteDesktop" src="https://user-images.githubusercontent.com/29058993/136154061-374b48eb-1ac9-4588-bf9e-fb9ae472c2d9.png">
    
    - Click the downloaded *.rdp* file and it will open *Microsoft Remote Desktop Client*<br>
    <img width="1066" alt="Connect2WindowsVMonWindowsOS-0" src="https://user-images.githubusercontent.com/29058993/136154026-88b827e4-b217-4370-b503-fcd5fbf9f281.png">
    
    - Type in account and password that you previously set<br>
    <img width="603" alt="Connect2WindowsVMonMacOS-0" src="https://user-images.githubusercontent.com/29058993/136154167-fc4bba75-fc27-4d73-9c70-e8835c76ecf3.png">

    - Connect to the Windows VM<br>
    - Open Powershell and type command and you will see the messages has been trasmitted and received<br>
    `ping <linux-vm-private-ip>`<br>

### b. Env2: WindowsOS
- [Virtual Desktop Client]:
    - Click the downloaded *.rdp* file and it will open *Remote Desktop Connection*<br>
    <img width="1066" alt="Connect2WindowsVMonWindowsOS-0" src="https://user-images.githubusercontent.com/29058993/136154026-88b827e4-b217-4370-b503-fcd5fbf9f281.png">
    <img width="620" alt="Connect2WindowsVMonWindowsOS-1" src="https://user-images.githubusercontent.com/29058993/136154242-ee213439-c3f8-424e-aaa1-b8e28d2abac4.png">

    - Click *more option* and type in the account and password that you previously set<br>
    <img width="525" alt="Connect2WindowsVMonWindowsOS-2" src="https://user-images.githubusercontent.com/29058993/136154282-d7e83b59-c4cc-4fd8-90a8-c108fef80a1b.png">

    - Connect to the Windows VM<br>
    <img width="451" alt="Connect2WindowsVMonWindowsOS-3" src="https://user-images.githubusercontent.com/29058993/136154306-95cef6d6-adfd-4fdf-99e7-8246b6cad74f.png">
    <img width="1436" alt="Connect2WindowsVMonWindowsOS-4" src="https://user-images.githubusercontent.com/29058993/136154325-36e80fc9-d2d0-4ced-9ca9-a03aea35c665.png">

    - Open Powershell and type command and you will see the messages has been trasmitted and received<br>
    `ping <linux-vm-private-ip>`<br>
    <img width="1460" alt="Connect2WindowsVMonWindowsOS-5" src="https://user-images.githubusercontent.com/29058993/136154394-df77f352-5f85-4aef-a455-2b1783f34558.png">


## Clean all resources
- Enter Azure portal and type *Resource groups* in the search bar<br>

- Click the resource group you just created (here is *test-1006*) and click *Delete resource group* on the top. A new panel will show up and type your resource group name under "TYPE THE RESOURCE GROUP NAME"<br>
<img width="1656" alt="DeleteResourceGroup" src="https://user-images.githubusercontent.com/29058993/136154415-171c5833-50bc-4585-927f-1a116cbe71db.png">

- Wait for several minutes and go back to resource group to check if all resources are deleted successfully

## Reference Links
[How Can I Connect 2 Azure Virtual Networks?](https://petri.com/can-connect-2-azure-virtual-networks)<br>
[Tutorial: Connect virtual networks with virtual network peering using the Azure portal](https://docs.microsoft.com/en-us/azure/virtual-network/tutorial-connect-virtual-networks-portal)<br>
