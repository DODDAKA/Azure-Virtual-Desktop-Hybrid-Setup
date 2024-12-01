# Azure-Virtual-Desktop-Hybrid-Setup
Azure-Virtual-Desktop setup for Hybrid users.
[Final_Azure Virtual Desktop POC.docx](https://github.com/user-attachments/files/17967886/Final_Azure.Virtual.Desktop.POC.docx)


                                                            Azure Virtual Desktop POC

![image](https://github.com/user-attachments/assets/6c5c952e-b05d-4e13-a61e-c1fa63dc49ff)

![image](https://github.com/user-attachments/assets/d808e6db-8c5d-4199-a52c-e1e580453d01)


#########**Create VNETS and SNETS for AVD environment**###############<br>

Here we are creating virtual network and subnets for AVD setup.<br>
fb-avd-poc-vnet (VNET) - 10.2.0.0/24<br>
snet-fb-poc-infra (SNET) - 10.2.0.0/26<br>
snet-fb-poc-prod (SNET) – 10.2.0.128/25<br>

![image](https://github.com/user-attachments/assets/6f40f71e-65c5-4d5f-b17d-2df5cda5eb00)
 
![image](https://github.com/user-attachments/assets/e493ba6d-0c71-4bb4-a30e-45d131a95aea)


#######**Create Azure VM and install AD DS role. And promote as Domain controller.** #######<br>
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
1.Create a storage account with basic configuration.<br>
Here we are choosing LRS.<br>
![image](https://github.com/user-attachments/assets/648f3ca2-f33e-474f-9e23-8fabc4146b8b)

2. And we want to make communication between storage account and other resources in azure cloud privately. So, we are disabling all the public access.<br>
 ![image](https://github.com/user-attachments/assets/00010244-365d-473a-a081-e9c9194f760d)

3. Now create private endpoint for storage account. In storage account, under networking settings we can see the create private endpoint option.<br>
 ![image](https://github.com/user-attachments/assets/1f6b9f38-6b78-4c97-b7d7-f281e0ae990c)


4. Create private endpoint with below basic details.<br>
![image](https://github.com/user-attachments/assets/fde2391c-fb70-482c-ba0c-238ddd4c5c23)

 
5. Choose sub-resource type as file. Because we are using file share inside storage account for our AVD setup.<br>
![image](https://github.com/user-attachments/assets/72d81b6e-f9e9-407d-b8ab-28db8adc4af5)
 


6. Choose VNET,SNET to deploy the private endpoint NIC in that range.<br>
 ![image](https://github.com/user-attachments/assets/32a786d9-7a3b-4bf3-9da9-223a7fa00eab)


7. Under DNS settings, choose option to create private DNS zone. So that endpoint integrate with DNS zone and will create a record for endpoint in that private DNS zone.<br>
 ![image](https://github.com/user-attachments/assets/126b17f3-a48c-48b5-b657-57ca5ddf05ff)




7. After creation of private endpoint. You see the deployment as below.<br>
![image](https://github.com/user-attachments/assets/3cd26f71-221a-4448-bf25-34d5ac4007e4)

 
8. Under DNS configuration, we can see the DNS record of private endpoint.<br>

![image](https://github.com/user-attachments/assets/dbdacf34-a44e-44b2-b89e-59af02670d92)


 

9. Now create file share with preferred size. Here we are creating share of size 1 TB.<br>


 ![image](https://github.com/user-attachments/assets/da4bcb8b-2786-4d8f-a805-6d93b04d7e7c)







#######**Integrate storage account to the Domain Controller using PowerShell**############<br>
Here we are domain joining storage account as computer object. We are creating separate OU named as StorageAccounts.<br>

 ![image](https://github.com/user-attachments/assets/ed5540c1-5b5b-4caf-9499-411552b5b841)


Found below url related how to enable AD DS authentication for Azure file share.<br>
Enable AD DS authentication for Azure file shares | Microsoft Learn<br>
1. First download AzFilesHybrid module. You can found download option in above doc link.<br>

 ![image](https://github.com/user-attachments/assets/ca1c560c-c445-4532-9bb0-478cbfa3be81)
![image](https://github.com/user-attachments/assets/0330fcb3-7fb5-43ea-94c0-d235fa97251c)
![image](https://github.com/user-attachments/assets/323c73c7-9574-4043-9a7f-94dc94b19fe5)



 
 

2. Now run the below commands one by one.<br>

Open the PowerShell as admin and run below commands. Replace few details with your actual resources. Prepare below commands and go to step 3.<br>

cd C:\Users\naveend\Downloads\AzFilesHybrid<br>
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser<br>
.\CopyToPSPath.ps1<br>
Import-Module -Name AzFilesHybrid<br>
Close the Powershell and run above command again.<br>
Connect-AzAccount<br>

$SubscriptionId = " 7f31db35-153e-4566-a603-7ea0fff45fe5"<br>
$ResourceGroupName = " fb-avd-poc-rg"<br>
$StorageAccountName = " steastus2primaryavdpoc"<br>
$DomainAccountType = "ComputerAccount" # Default is set as ComputerAccount<br>
$OuDistinguishedName = "OU= StorageAccounts,DC= fbpoc,DC=online"<br>

Select-AzSubscription -SubscriptionId $SubscriptionId <br>


Join-AzStorageAccount `<br>
        -ResourceGroupName $ResourceGroupName `<br>
        -StorageAccountName $StorageAccountName `<br>
        -DomainAccountType $DomainAccountType `<br>
        -OrganizationalUnitDistinguishedName $OuDistinguishedName<br>
3. Now run above commands one by one in PowerShell.<br>
First go to below path in PowerShell.<br>
 
![image](https://github.com/user-attachments/assets/d981981c-8a4f-4210-bb1a-5ff50ce94a09)

![image](https://github.com/user-attachments/assets/2d748cfe-e9d1-4f39-a966-06ba0344c9e0)










 


4. Run Import-Module -Name AzFilesHybrid<br>
This will install all the modules related to Azure files Hybrid.<br>
 ![image](https://github.com/user-attachments/assets/54a407fc-1c29-4f82-911c-0892d97d6c0e)

5. You will see above error while importing AzFilesHybrid module. Then close that PowerShell and re-open the new PowerShell and run the command again.<br>
 ![image](https://github.com/user-attachments/assets/963ffd07-b4e2-44a0-a1fe-3e9366c9d65d)
![image](https://github.com/user-attachments/assets/f879b860-be76-42f9-88d5-1d8315431915)
![image](https://github.com/user-attachments/assets/7e1356ce-c2d7-4c21-bf1c-e6f548488f4f)


 



 


6.Run this command to connect to your subscription and tenant:  Connect-AzAccount <br>
 ![image](https://github.com/user-attachments/assets/c09aad8b-9fa3-4f03-8657-a4669ab4e9bf)



8. Run the details as below. And replace details with your actual details.<br>
$SubscriptionId = " 7f31db35-153e-4566-a603-7ea0fff45fe5"<br>
$ResourceGroupName = " fb-avd-poc-rg"<br>
$StorageAccountName = " steastus2primaryavdpoc"<br>
$DomainAccountType = "ComputerAccount" # Default is set as ComputerAccount<br>
$OuDistinguishedName = "OU= StorageAccounts,DC= fbpoc,DC=online"<br>
 ![image](https://github.com/user-attachments/assets/12d514ed-e708-4ff0-8c6e-672fe7fc89c5)


9. Then run this command: Select-AzSubscription -SubscriptionId $SubscriptionId <br>
![image](https://github.com/user-attachments/assets/0906259b-9f04-4942-b126-9c873cea5b04)

 


10. Then run this below command to join Azure storage Account.<br>
Join-AzStorageAccount `<br>
        -ResourceGroupName $ResourceGroupName `<br>
        -StorageAccountName $StorageAccountName `<br>
        -DomainAccountType $DomainAccountType `<br>
        -OrganizationalUnitDistinguishedName $OuDistinguishedName<br>
![image](https://github.com/user-attachments/assets/240a3504-78b4-407d-b144-9d53deebfce5)

 
11. After running all the above commands, you will storage as below in OU.<br>
 ![image](https://github.com/user-attachments/assets/7296e808-e8e8-40a0-ba1e-1f01c401ec48)






#########**Assign permission for users on File share and mount file share on Domain Controller**############<br>
1.Now assign below permissions on file share level to the user groups.<br>
SMB share contributor – Assign this to normal users for contributor access on file share.<br>
SMB Elevated contributor – Assign this to admin users for elevated access on file share.<br>
 
![image](https://github.com/user-attachments/assets/fd81b997-697a-46c3-be5d-52c15d2e5558)













2. Before mounting file share on domain controller. First, check the storage account resolution because we have created storage account with private endpoint. When you try to resolve storage account, we should see the private IP address of private endpoint NIC.<br>
Here we can see it is resolving properly. Otherwise, we have to go through troubleshooting to make sure all traffic is going through the private endpoint only.<br>
![image](https://github.com/user-attachments/assets/bab28226-1da8-4559-a094-4a00750a16bc)

 

3. Now go to file share on portal and click on connect option. Once you click on connect, you will see two options, connect with AD DS authentication and storage account key. Choose your drive letter and click on show script. It will generate script and run that script on PowerShell as normal user.<br>
Here we are using storage account key for mounting.<br>
 ![image](https://github.com/user-attachments/assets/38fd5ad9-a01f-4c43-82fb-477222e9594e)

4. Once it is mounted, we will see as below.<br>
 
![image](https://github.com/user-attachments/assets/09299475-c4e5-425f-9c9c-bc0a6ad6e729)

5. we can see the status of mounted share on file explorer as well.<br>
![image](https://github.com/user-attachments/assets/cad2b592-2e44-49ba-b61c-bfa43e07abfc)
 


6. Create new folder called UserProfies and assign NTFS permissions.<br>
 ![image](https://github.com/user-attachments/assets/87eb62a1-a1dc-4bfd-b94d-c0a0601a35a6)

7. Go to properties of that folder->security tab-> Then disable all the inheritance.<br>
 
![image](https://github.com/user-attachments/assets/667183f1-47cb-410e-891d-0d1d0183b234)



8. Except below ones remove all the other entries.<br>
 
![image](https://github.com/user-attachments/assets/31bdfe23-67ac-4438-b140-1b7bc11f3f10)

9. Now give the permissions as below.<br>
https://learn.microsoft.com/en-us/fslogix/how-to-configure-storage-permissions<br>
 ![image](https://github.com/user-attachments/assets/a7ecd1d2-59ed-456f-bade-5f138b1633eb)

![image](https://github.com/user-attachments/assets/469cacd5-6d75-4549-8f84-b96e63e44102)







 


10. Once you given the NTFS permissions as above. Then save the permissions.<br>

######**Configure Group policies on Domain joined session hosts:** ###########<br>
We are creating group policies for FSlogix and windows components.<br>
After creating policy we will apply this to All domain joined session hosts which we will create in AVD setup.<br>
Go to server Manager->Tools->Group policy Management.<br>
 ![image](https://github.com/user-attachments/assets/154d1275-ce40-4999-89c4-2f4717d85440)


1. Then right click on group policy object and create new GPO.<br>
 
![image](https://github.com/user-attachments/assets/57e7a36f-9ffe-466d-b99e-db3ea8d9599b)


2. You will see as below.<br>
 ![image](https://github.com/user-attachments/assets/caf9efc3-64ee-43e9-952a-b42d7cb3b54c)


3. Then attach this GPO to Computes (Session hosts) OU.<br>
Here we are creating fbavdpoc-comp-group OU and we will assign GPO on this OU.<br>

![image](https://github.com/user-attachments/assets/6d53a51f-f552-406e-84e3-5f746ea1bad0)









 





4. Below is the process of linking existing GPO under Group policy Management.<br>
 
![image](https://github.com/user-attachments/assets/eccb578b-f1b4-4f9c-b802-9b4db825b9cd)


Configuring FSLogix settings from Group policy.<br>

First Install FsLogix application from below link. <br>

Install FSLogix Applications - FSLogix | Microsoft Learn<br>


1.Once you unzip FSLogix application, we will see adml and admx files.<br>
![image](https://github.com/user-attachments/assets/7ee0b1bd-e058-428a-be30-2f5ccb15dfd4)
 
2. Now place these files in below path location. Create new folder name PolicyDefinition under below file explorer location.<br>
 ![image](https://github.com/user-attachments/assets/699d1084-4931-47da-8e36-12c5237c96ef)

![image](https://github.com/user-attachments/assets/69b39b21-9e46-4b57-bfbd-905dacdef2b2)

 
3. Place fslogix.admx file in PolicyDefinitions folder and create folder en-us inside Policy Definitions.<br>
 ![image](https://github.com/user-attachments/assets/73fb3e6e-f97f-4a3d-9e33-fe7b97ba2c23)

4. Then place fslogix.adml file inside en-us folder.<br>
 ![image](https://github.com/user-attachments/assets/626af5ee-b6fc-463b-b44d-4d944668ed3c)


5. once you place those adml and admx files in mentioned locations. We will see the polices related to fslogix as below. And we can configure our basic fslogix policies based on our requirement.<br>
 
![image](https://github.com/user-attachments/assets/64077ae3-3788-4bf7-8aad-349ac5232d06)

Enabled :This FSLogix policy will enable the profile container setting.<br>
 
![image](https://github.com/user-attachments/assets/4468d38a-6166-4df0-82f3-f6634dd932e1)


VHD Locations: Here we will give the Azure file share path location. So that all the user profiles will store in that location.<br>
 ![image](https://github.com/user-attachments/assets/cf76f640-158a-48f7-865b-2403c969008d)

Delete Local Profile when VHD should Apply: When you are login into session host for first time, it will create local profile. So that in order to delete that local profile, when have to enable this option.<br>
 ![image](https://github.com/user-attachments/assets/3d9de16a-dae5-4173-a9f7-aa5915588d7e)


Then choose other policies like Volume Type(VHD or VHDX).<br>
Flip Flop profile Directory Name: It will flip the user profile directory name.<br>
 
![image](https://github.com/user-attachments/assets/0a6ee677-382a-4e2c-a1eb-dfb24c7c26c6)








Download administrative templates for windows. We can configure group policies for windows components as well. For this we have to install administrative templates as below.<br>

Create and manage Central Store - Windows Client | Microsoft Learn<br>
First download respective windows server administrative template. And start installing.<br>
While installing choose the path where you would like to install. Here we are choosing same path where we have installed fsLogix related policies.<br>

 ![image](https://github.com/user-attachments/assets/2fa58fb1-d2e5-4b42-bf34-89601b17b745)
![image](https://github.com/user-attachments/assets/f2fdddbb-c995-46b1-bb70-613415a8d867)

![image](https://github.com/user-attachments/assets/ced6cbed-95d0-4190-8772-f7778c26b15a)


 

 

Once you install administrative template in right location. You will see the other folders along with FSLogix folder in group policy management.<br>

gpupdate /force: run this command in order to update group policy and will apply to all the session hosts.<br>
 
![image](https://github.com/user-attachments/assets/d837813a-3bba-461f-b52a-9afbb50e7f11)

















###########**Creating Host pool and Session hosts:**##############<br>
Go to Azure portal and search for AVD/Azure virtual Desktop.<br>

Then create host pool, while creating host pool we have to give some basic details related to hp, load balancing algorithm, Max sessions limit and etc.<br>
 

![image](https://github.com/user-attachments/assets/afe6bad9-9a1d-433a-8cf6-e3839008f1d7)

 
![image](https://github.com/user-attachments/assets/f2cbf12c-9d08-4465-af86-4fa3b7948834)













2. While creating session hosts for host pool. We have to give image and other details based on our requirements.<br>
Here we are choosing Image: Windows 10 multi session+ M365 apps.( this image have fslogix software by default)<br>
Organizational Unit path: This one has to give in below screenshot reference format. Here we have to choose the OU where we have applied group policies(fbavdpoc-comp-group).<br>
Make sure your AVD setup and storage account all are should be in the same location.<br>

 ![image](https://github.com/user-attachments/assets/d24dd5ea-3927-4fe5-816b-a3bd1e44db13)









3. Once you complete the above steps. Then we will see the Host Pool as below with available session hosts.<br>
 
![image](https://github.com/user-attachments/assets/af95efc2-c889-428b-a420-c970667914b3)
![image](https://github.com/user-attachments/assets/48baf609-1099-46c2-baf7-189efa34682c)

 

#############**Assign users to the Desktop group**#######:<br>
Now assign users to this created host pool. So that they can login into session hosts.<br>
For this, first we have to go to Application Group-> You will see Default group with name Default Desktop. Then click on that group.<br>
 
![image](https://github.com/user-attachments/assets/4d56b797-b5a6-4c95-91cd-e4d7462062d6)
















2. Now assign user groups to this application group by going to Assignments section.<br>
 

![image](https://github.com/user-attachments/assets/0151bee4-4340-4ad7-8fd1-929d61057344)











3. Give Virtual machine Administrator Login role on resource group level:<br>
Go to Resource Group->Access control (IAM) then add below role to user groups.<br>
Virtual machine Administrator Login: This role will allow users to login into session hots.<br>
![image](https://github.com/user-attachments/assets/8ff8b230-c278-4d9f-a61d-d546fb053296)

 











4. Now use Remote Desktop client to found user’s workspace and their feed.<br>
Download Remote Desktop client from internet and click on subscribe option in that application, then login with their user’s credentials. So that, it will fetch all user’s feed under their workspace.<br>

Here we are login with dnkavduser1.<br>
![image](https://github.com/user-attachments/assets/f3a48e9f-485f-40d5-88ab-4c523a27ba74)

 

5. Once the workspace is loaded. You will see the Desktop icon. Now double click on it and login with user credentials.<br>










6. Once you login into session host, you can cross verify few details like session host name, user name etc.<br>
 
![image](https://github.com/user-attachments/assets/90eb3d32-da55-442f-88c3-d04b3acc963b)











7. Then go to File share and see whether user profile is creating or not.<br>
Here we can see that after user login, profile is creating successfully.<br>
 ![image](https://github.com/user-attachments/assets/3922d491-16f5-4b1b-917f-a0eb8c2faac5)



