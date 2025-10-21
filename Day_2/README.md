# Day 2 : Launch a Virtual Machine (VM) Instance

## Task 1. Create a new instance from the Cloud console

To understand **how cloud virtual machines work** by creating, configuring, and connecting to a *cloud-based server instance* using a free-tier account.
This task introduces interns to **Infrastructure-as-a-Service (IaaS)** — the foundation of all cloud operations.


In this section, you create new predefined machine types with Compute Engine from the Cloud console.
1. In the Cloud console, on the Navigation menu (☰), click Compute Engine > VM Instances.

This may take a minute to initialize for the first time.

2. To create a new instance, click Create Instance.

3. In the Machine configuration:

Enter the values for the following fields:
 
| Field	 | Value | Additional Information |
|---|---|---|
| Name | gcelab | 	Name for the VM instance |
| Region | REGION  | For more information about regions, see the Compute Engine guide, Regions and Zones. |
| Zone | ZONE | Note: Remember the zone that you selected to use later. For more information about zones, see the Compute Engine guide, Regions and Zones. |
| Machine Type | e2-medium | This is an e2-medium, 2-CPU, 4GB RAM instance. Several machine types are available, ranging from micro instance types to 32-core/208GB RAM instance types. For more information, see the Compute Engine guide, About machine families. |

4. Click OS and storage.

    Click Change to begin configuring your boot disk and select the values for:

    * Operating system: Debian
    * Version: Debian GNU/Linux 12 (bookworm)
    * Boot disk type: Balanced persistent disk
    * Size: 10 GB

Several images are available, including Debian, Ubuntu, CoreOS, and premium images such as Red Hat Enterprise Linux and Windows Server. For more information, see the Operating System documentation.

5. Click Networking.

    * Firewall: Allow HTTP traffic

Select this option in order to access a web server that you install later.

        Note: This automatically creates a firewall rule to allow HTTP traffic on port 80.

6. Once all sections are configured, scroll down and click Create to launch your new virtual machine instance.

It should take about a minute for the VM, gcelab, to be created. After gcelab is created, the VM Instances page lists it in the VM instances list.

7. To use SSH to connect to the VM, click SSH to the right of the instance name, gcelab.

This launches an SSH client directly from your browser.

        Note: Learn more about how to use SSH to connect to an instance from the Compute Engine guide, Connect to Linux VMs using Google tools .
   
## Reference :
Google Cloud Skill Boost :

[Create a Virtual Machine](https://www.skills.google/focuses/3563?parent=catalog)

