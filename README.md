# Server Assessment of AWS EC2 Instances  by Azure Migrate

## Description
Customer is looking to Migrate Ubuntu 16.04 EC2 instances hosted in AWS to Azure. Create a POC for this which discuss step by step instructions in migrating a Ubuntu 16.04 hosted in AWS to Azure by Azure Migrate.

## Checking Migration Supportability
In the public MS Docs https://docs.microsoft.com/en-us/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions , there are a list of generic Linux Kernels that are supported for migration to Azure.

So, you can check with your customer **what is the current kernel version that is being used in their EC2 instances**.
 
Run the following commands to get the OS version & Kernel.
 
* Run **lsb_release -a** to get version of Ubuntu
* Run **uname -mrs** to get current kernel

If customer are **not in the supported Kernel**, then below are the option they have.

* Workaround in this case is to install a supported version of the generic kernel (or switch to a supported version of a generic kernel that is available ) and **test the system thoroughly before moving.**
 
* Other options are to setup VMs in Parallel and then do the data migration or RSync to sync file system.

## If **supported version**, then leverage below step by step instructions and learnings.


So as a first step do discovery and assessment of the product.

* Ubuntu 16.04 in AWS 

Png (Ubuntu EC2 instances in AWS) 

* Set up an Azure Migrate project : https://docs.microsoft.com/en-us/azure/migrate/tutorial-assess-physical#set-up-an-azure-migrate-project


* Create Windows server 2016 machine in **AWS** which will be used for Azure Migrate Appliance. 
(It is recommended that the replication appliance is deployed in the same VPC as the source VMs to be migrated. If the replication appliance needs to be in a different VPC, the VPCs need to be connected through VPC peering)
Png (Windows EC2 VM for Replication Appliance) 


* At present, **Key based authentication is not supported in Azure Migrate so we should use password authentication**. For this, change SSH to password authentication for Ubuntu 16.04 hosted in AWS

   * From windows machine: You can use **putty** for connecting to the instance. Steps for connecting https://comtechies.com/how-to-connect-an-amazon-ec2-ubuntu-linux-instances-using-putty.html

   * Open the sshd_config file by command **sudo vi /etc/ssh/sshd_config**


   * Find the Line containing **PasswordAuthentication** parameter and change its value from     **no** to **yes** (PasswordAuthentication yes) 

   * Set up **root** login, find  **PermitRootLogin** parameter and change its value from                                                                                                                    **prohibit-password** to **yes**
   
   * After the changes, save the file and exit
   
   * Change the root password for my EC2 Linux instance by    https://aws.amazon.com/premiumsupport/knowledge-center/set-change-root-linux/


## Some of the learnings from above step: 
* **Key based authentication** will not work so we should use **password** authentication. **Error** while using key based authentication “No suitable authentication method found to complete authentication (publickey)

* Always use **root login** for authentication not **any pseudo user** and then promoting    this user as Admin. Error while using pseudo user. Azure Migrate failed to fetch BIOS GUID

* Set up an Azure Migrate appliance which will collect metadata about system running in AWS. 
https://docs.microsoft.com/en-us/azure/migrate/tutorial-assess-physical#set-up-the-appliance

PNG (Prerequisite Azure Migrate)

PNG (Register with Azure Migrate)

PNG (Physical Server Details)

PNG (Validate)

PNG(Validation Completion)

**After performing all the above steps, you will have server in Discovered Servers**

PNG (Discovered Server in Azure Portal)

PNG (Assessment)

PNG (Assess Servers)

Same steps will be repeated for Migration apart from one extra step. https://docs.microsoft.com/en-us/azure/migrate/tutorial-migrate-physical-virtual-machines



