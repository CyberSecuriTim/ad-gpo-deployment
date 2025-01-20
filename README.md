<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Group Policy Objects within an Active Directory Domain in Azure</h1>
<p>

- This tutorial outlines the creation and deployment of group policy objects (GPOs) within an Active Directory domain consisting of Windows virtual machines hosted in Azure.

- NOTE: To successfully perform this lab, a properly installed and configured Active Directory Domain is required including a functioning domain controller and at least one 
  client connected to this domain controller.
  - For a comprehensive step-by-step tutorial on how to achieve this please take a look at my previous lab linked [here](https://github.com/CyberSecuriTim/ad-configuration).

 </p>
    
<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 0: Create multiple Non-Admin users and add them to the Active Directory Domain
- Step 1: Configure Remote Desktop Access for Non-administrative users on the Domain client VM (and all Domain Clients) via a group policy object
- Step 2: Configure a Password Lockout Policy for all domain users via a group policy object.

<h2>Deployment and Configuration Steps</h2>

<h2> STEP 0: Create multiple Non-Admin Users and Add These Users to the Active Directory Domain.</h2>

<p> 

  - Turn on your Domain Controller VM if it was turned off at the end of the previous lab and establish a remote desktop connection to it using its public IP address.
     - Log in as one of the accounts from the previous lab that has Domain Admin privileges.
   
  ![image](https://github.com/user-attachments/assets/1971c08f-e7c2-4e48-bc0a-cdb75052df00)


  - Run "Windows Powershell ISE" (Integrated Scripting Environment) as an administrator

  ![image](https://github.com/user-attachments/assets/a6b44e9a-8e61-4641-9628-42d124c8a5ad)

   - Create a new file then copy and paste the contents of this [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into the 
     Powershell ISE file . 

![image](https://github.com/user-attachments/assets/6951b099-ad34-42fd-ab59-0d4d86faa6d2) 

![image](https://github.com/user-attachments/assets/83526b16-d498-43f5-bb8c-3caed80e4202)


  - The password that will automaticall be provisioned to all the created users is "Password1"
  - The Number of Accounts that will be created is 10,000
    - Both of these variables can be changed at your discretion.
    

 
</p>

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h2> STEP 1: Deploy a Group Policy Object to Enable Remote Desktop Access for Non-admin users on all domain clients  </h2>

<p> 

 

</p>
