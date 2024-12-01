# Azure-Virtual-Desktop-Hybrid-Setup
Azure-Virtual-Desktop setup for Hybrid users.
[Final_Azure Virtual Desktop POC.docx](https://github.com/user-attachments/files/17967886/Final_Azure.Virtual.Desktop.POC.docx)


                                                            Azure Virtual Desktop POC



**Create VNETS and SNETS for AVD environment**<br>

Here we are creating virtual network and subnets for AVD setup.<br>
fb-avd-poc-vnet (VNET) - 10.2.0.0/24<br>
snet-fb-poc-infra (SNET) - 10.2.0.0/26<br>
snet-fb-poc-prod (SNET) – 10.2.0.128/25<br>

![image](https://github.com/user-attachments/assets/6f40f71e-65c5-4d5f-b17d-2df5cda5eb00)
 
![image](https://github.com/user-attachments/assets/e493ba6d-0c71-4bb4-a30e-45d131a95aea)


#########################################################################
**Create Azure VM and install AD DS role. And promote as Domain controller.**  <br>
<br>
1.Go to Azure portal and create a basic VM which represents as Domain controller. Here I am taking windows server 2019 with basic configuration.<br>
![image](https://github.com/user-attachments/assets/56f86b4d-d241-4dda-88ad-d04633ce8bb0)
![image](https://github.com/user-attachments/assets/4ef99ebc-cdc3-451e-b6cf-5a1077d8e990)
<br>

2. Go to virtual network and add custom DNS.<br>
Here we are adding private IP address of Domain controller VM on VNET DNS server settings. So that, all the servers in that VNETS knows about DNS server.<br>
 ![image](https://github.com/user-attachments/assets/ce381d05-a5c1-46ab-82e2-591137c75088)

**Deploy Domain controller then add Domain and users on DC server.**<br>
1. Now login into Domain controller VM which we have created previously and install AD DS role on it.<br>
 ![image](https://github.com/user-attachments/assets/b2a51ca9-29ec-4800-b95c-db6000a45fb0)
2. Then promote this server as domain controller. While promoting server as Domain controller, enter your domain name by selecting option Add a new forest.<br>
 ![image](https://github.com/user-attachments/assets/7dd6327e-1d4d-4518-a548-9e8fc1e9aa53)
3. Once you configure everything, it will ask for restart. Then do it accordingly.<br>
After restart, login into Domain controller. Then in server manager you will see as below.<br>
 ![image](https://github.com/user-attachments/assets/e26d3bd9-4ad2-4bc8-8196-9b7d7ce4a7be)
4. Then add users and groups to Active Directory. Here we are creating few users for testing purpose. And we will sync these users to the azure cloud.<br>
![image](https://github.com/user-attachments/assets/11f59f74-47f8-4074-b580-042af43788b2)
5. Giving elevated permissions for single user and make him as Admin user. So that, he can do all operations like domain joining and etc.<br>
Double click on the user name-> Go to Member of option-> Add below Displayed roles.<br>
![image](https://github.com/user-attachments/assets/5b70d50e-4742-4de6-956c-751c6f134160)

6. Create groups and add users to those respective groups.<br>
fbavdpoc-admin-group : Here we are adding the user who has elevated permission.<br>
fbavdpoc-users-group: Here we are adding all the normal users.<br>

Adding users: Double click on group name and go to Members option. And add respective users.<br>
 ![image](https://github.com/user-attachments/assets/65705097-a526-4dbd-9882-1cabaa41e050)
![image](https://github.com/user-attachments/assets/12349d8f-59ea-4897-95d5-cc34d9aea3b0)


**Install AD Connect and sync users to the cloud**<br>
1.In order to sync users from on premises Active Directory to Azure cloud. We need an cloud user with global administrator permissions on Entra ID level.<br>

Here we are using cloudrockadmin user who has global administrator permission on entra id.<br>
![image](https://github.com/user-attachments/assets/9ada7e3d-5c3e-4108-bee9-188198522a6c)
![image](https://github.com/user-attachments/assets/d2c11e3c-a13f-45db-a28d-2e76c1222624)

2. On Domain controller server, download Azure AD connect tool.<br>
This Azure AD connect tool syncs the users to azure cloud. We can get this software from internet which is available for free.<br>

After installing and running the software. Now, we have to enter Azure could user credentials who has permissions to sync to the cloud. <br>
 ![image](https://github.com/user-attachments/assets/db058896-2a25-48de-99aa-a4f07b0a4ecf)

After clicking next in previous step, now enter the AD DS enterprise administrator credentials as in below format.<br>
![image](https://github.com/user-attachments/assets/2e1b694b-49b3-4536-b3a3-aaa4136b8287)
 ![image](https://github.com/user-attachments/assets/cfc6ce19-429d-4e33-acbd-78fd66056f17)

After complete installation. We will the confirmation as below.<br>
![image](https://github.com/user-attachments/assets/64b2d2a6-a228-4e92-9057-8cd8542297b3)

3. Now go to Azure portal-> Entra ID-> Users.<br>
We can see on premises Active Directory users and groups are synced to cloud successfully.<br>
 ![image](https://github.com/user-attachments/assets/7969a611-f94c-468b-a485-4b0328ab071a)
![image](https://github.com/user-attachments/assets/70542722-c00e-4cf5-b630-fec53c669607)

**Create storage account and File share in Azure portal**<br>
1.Create a storage account with basic configuration.
Here we are choosing LRS.
 
2. And we want to make communication between storage account and other resources in azure cloud privately. So, we are disabling all the public access.
 
3. Now create private endpoint for storage account. In storage account, under networking settings we can see the create private endpoint option.
 

4. Create private endpoint with below basic details.

 
5. Choose sub-resource type as file. Because we are using file share inside storage account for our AVD setup.
 


6. Choose VNET,SNET to deploy the private endpoint NIC in that range.
 

7. Under DNS settings, choose option to create private DNS zone. So that endpoint integrate with DNS zone and will create a record for endpoint in that private DNS zone.
 



7. After creation of private endpoint. You see the deployment as below.
 
8. Under DNS configuration, we can see the DNS record of private endpoint.
 

9. Now create file share with preferred size. Here we are creating share of size 1 TB.

 






Integrate storage account to the Domain Controller using PowerShell
Here we are domain joining storage account as computer object. We are creating separate OU named as StorageAccounts.
 

Found below url related how to enable AD DS authentication for Azure file share.
Enable AD DS authentication for Azure file shares | Microsoft Learn
1. First download AzFilesHybrid module. You can found download option in above doc link.
 


 
 

2. Now run the below commands one by one.

Open the PowerShell as admin and run below commands. Replace few details with your actual resources. Prepare below commands and go to step 3.

cd C:\Users\naveend\Downloads\AzFilesHybrid
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
.\CopyToPSPath.ps1
Import-Module -Name AzFilesHybrid
Close the Powershell and run above command again.
Connect-AzAccount

$SubscriptionId = " 7f31db35-153e-4566-a603-7ea0fff45fe5"
$ResourceGroupName = " fb-avd-poc-rg"
$StorageAccountName = " steastus2primaryavdpoc"
$DomainAccountType = "ComputerAccount" # Default is set as ComputerAccount
$OuDistinguishedName = "OU= StorageAccounts,DC= fbpoc,DC=online"

Select-AzSubscription -SubscriptionId $SubscriptionId 


Join-AzStorageAccount `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType $DomainAccountType `
        -OrganizationalUnitDistinguishedName $OuDistinguishedName
3. Now run above commands one by one in PowerShell.
First go to below path in PowerShell.
 











 


4. Run Import-Module -Name AzFilesHybrid
This will install all the modules related to Azure files Hybrid.
 
5. You will see above error while importing AzFilesHybrid module. Then close that PowerShell and re-open the new PowerShell and run the command again.
 

 



 


6.Run this command to connect to your subscription and tenant:  Connect-AzAccount 
 


8. Run the details as below. And replace details with your actual details.
$SubscriptionId = " 7f31db35-153e-4566-a603-7ea0fff45fe5"
$ResourceGroupName = " fb-avd-poc-rg"
$StorageAccountName = " steastus2primaryavdpoc"
$DomainAccountType = "ComputerAccount" # Default is set as ComputerAccount
$OuDistinguishedName = "OU= StorageAccounts,DC= fbpoc,DC=online"
 

9. Then run this command: Select-AzSubscription -SubscriptionId $SubscriptionId 

 


10. Then run this below command to join Azure storage Account.
Join-AzStorageAccount `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType $DomainAccountType `
        -OrganizationalUnitDistinguishedName $OuDistinguishedName

 
11. After running all the above commands, you will storage as below in OU.
 





Assign permission for users on File share and mount file share on Domain Controller
1.Now assign below permissions on file share level to the user groups.
SMB share contributor – Assign this to normal users for contributor access on file share.
SMB Elevated contributor – Assign this to admin users for elevated access on file share.
 













2. Before mounting file share on domain controller. First, check the storage account resolution because we have created storage account with private endpoint. When you try to resolve storage account, we should see the private IP address of private endpoint NIC.
Here we can see it is resolving properly. Otherwise, we have to go through troubleshooting to make sure all traffic is going through the private endpoint only.

 

3. Now go to file share on portal and click on connect option. Once you click on connect, you will see two options, connect with AD DS authentication and storage account key. Choose your drive letter and click on show script. It will generate script and run that script on PowerShell as normal user.
Here we are using storage account key for mounting.
 
4. Once it is mounted, we will see as below.
 

5. we can see the status of mounted share on file explorer as well.
 


6. Create new folder called UserProfies and assign NTFS permissions.
 
7. Go to properties of that folder->security tab-> Then disable all the inheritance.
 



8. Except below ones remove all the other entries.
 

9. Now give the permissions as below.
https://learn.microsoft.com/en-us/fslogix/how-to-configure-storage-permissions
 







 


10. Once you given the NTFS permissions as above. Then save the permissions.

Configure Group policies on Domain joined session hosts:
We are creating group policies for FSlogix and windows components.
After creating policy we will apply this to All domain joined session hosts which we will create in AVD setup.
Go to server Manager->Tools->Group policy Management.
 

1. Then right click on group policy object and create new GPO.
 


2. You will see as below.
 

3. Then attach this GPO to Computes (Session hosts) OU.
Here we are creating fbavdpoc-comp-group OU and we will assign GPO on this OU.










 





4. Below is the process of linking existing GPO under Group policy Management.
 


Configuring FSLogix settings from Group policy.

First Install FsLogix application from below link. 

Install FSLogix Applications - FSLogix | Microsoft Learn


1.Once you unzip FSLogix application, we will see adml and admx files.
 
2. Now place these files in below path location. Create new folder name PolicyDefinition under below file explorer location.
 

 
3. Place fslogix.admx file in PolicyDefinitions folder and create folder en-us inside Policy Definitions.
 
4. Then place fslogix.adml file inside en-us folder.
 

5. once you place those adml and admx files in mentioned locations. We will see the polices related to fslogix as below. And we can configure our basic fslogix policies based on our requirement.
 

Enabled :This FSLogix policy will enable the profile container setting.
 


VHD Locations: Here we will give the Azure file share path location. So that all the user profiles will store in that location.
 
Delete Local Profile when VHD should Apply: When you are login into session host for first time, it will create local profile. So that in order to delete that local profile, when have to enable this option.
 

Then choose other policies like Volume Type(VHD or VHDX).
Flip Flop profile Directory Name: It will flip the user profile directory name.
 








Download administrative templates for windows. We can configure group policies for windows components as well. For this we have to install administrative templates as below.

Create and manage Central Store - Windows Client | Microsoft Learn
First download respective windows server administrative template. And start installing.
While installing choose the path where you would like to install. Here we are choosing same path where we have installed fsLogix related policies.

 


 

 

Once you install administrative template in right location. You will see the other folders along with FSLogix folder in group policy management.

gpupdate /force: run this command in order to update group policy and will apply to all the session hosts.
 

















Creating Host pool and Session hosts:
Go to Azure portal and search for AVD/Azure virtual Desktop.

Then create host pool, while creating host pool we have to give some basic details related to hp, load balancing algorithm, Max sessions limit and etc.
 


 













2. While creating session hosts for host pool. We have to give image and other details based on our requirements.
Here we are choosing Image: Windows 10 multi session+ M365 apps.( this image have fslogix software by default)
Organizational Unit path: This one has to give in below screenshot reference format. Here we have to choose the OU where we have applied group policies(fbavdpoc-comp-group).
Make sure your AVD setup and storage account all are should be in the same location.

 








3. Once you complete the above steps. Then we will see the Host Pool as below with available session hosts.
 

 

Assign users to the Desktop group:
Now assign users to this created host pool. So that they can login into session hosts.
For this, first we have to go to Application Group-> You will see Default group with name Default Desktop. Then click on that group.
 
















2. Now assign user groups to this application group by going to Assignments section.
 












3. Give Virtual machine Administrator Login role on resource group level:
Go to Resource Group->Access control (IAM) then add below role to user groups.
Virtual machine Administrator Login: This role will allow users to login into session hots.

 











4. Now use Remote Desktop client to found user’s workspace and their feed.
Download Remote Desktop client from internet and click on subscribe option in that application, then login with their user’s credentials. So that, it will fetch all user’s feed under their workspace.

Here we are login with dnkavduser1.

 

5. Once the workspace is loaded. You will see the Desktop icon. Now double click on it and login with user credentials.










6. Once you login into session host, you can cross verify few details like session host name, user name etc.
 











7. Then go to File share and see whether user profile is creating or not.
Here we can see that after user login, profile is creating successfully.
 


