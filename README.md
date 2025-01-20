<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Group Policy Objects within an Active Directory Domain in Azure</h1>
<p>

- This tutorial outlines the creation and deployment of group policy objects (GPOs) within an Active Directory domain consisting of Windows virtual machines hosted in Azure.
  - Specifically, creating (and deploying) a GPO to authorize Remote Desktop Access to created Non-Admin Users.
  - Creating (and deploying) a GPO to trigger account lockouts after a certain number of incorrect password attempts 

- NOTE: To successfully perform this lab, a properly installed and configured Active Directory Domain is required, including a functioning domain controller and at least one 
  client connected to this domain controller.
  - For a comprehensive step-by-step tutorial on how to achieve this please take a look at my previous lab linked below:
      - [Active Directory Installation](https://github.com/CyberSecuriTim/ad-configuration)

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
- Bonus Step: Using Active Directory to Unlock a Locked Account (and Reset the Password).

<h2>Deployment and Configuration Steps</h2>

<h2> STEP 0: Create multiple Non-Admin Users and Add These Users to the Active Directory Domain using Powershell ISE (Integrated Scripting Environment).</h2>

<p> 

  - Turn on your Domain Controller VM, if it was turned off at the end of the previous lab and establish a remote desktop connection to it using its public IP address.
     - Log in as one of the accounts from the previous lab that has Domain Admin privileges.
   
  ![image](https://github.com/user-attachments/assets/1971c08f-e7c2-4e48-bc0a-cdb75052df00)


  - Run "Windows Powershell ISE" (Integrated Scripting Environment) as an administrator

  ![image](https://github.com/user-attachments/assets/a6b44e9a-8e61-4641-9628-42d124c8a5ad)

   - Create a new file then copy and paste the contents of this [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into the newly 
     created Powershell ISE file . 

![image](https://github.com/user-attachments/assets/6951b099-ad34-42fd-ab59-0d4d86faa6d2) 

![image](https://github.com/user-attachments/assets/83526b16-d498-43f5-bb8c-3caed80e4202)


  - The password that will automatically be provisioned (by default) to all the users created by the script is "Password1"
  - The nummber of accounts that will be created by the script (by default) is 10,000
    - Both of these variables can be changed at your discretion.
   
![image](https://github.com/user-attachments/assets/f1962ed4-320f-4079-9d8e-fff8a8bff2bf)

    
- Run the script (press the green arrow) and observe the non-admin accounts being created.

![image](https://github.com/user-attachments/assets/6559aa0b-6302-4296-a989-cc9a67196d85)

 - Open "Active Directory Users and Computers" and select the "_EMPLOYEES" organizational unit (which was created in the previous lab)
   - Observe that all the non-admin users were created and placed into this OU by the script.
</p>

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h2> STEP 1.0: Create and Deploy a Group Policy Object to Enable Remote Desktop Access for Non-admin users on all domain clients  </h2>

<p> 
<h4>(Still Within the Domain Controller VM)</h4>

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<h3> STEP 1.1: Create a Security Group for Non-Admin Remote Desktop Users</h3>
- Open "Active Directory Users and Computers"
  - Right click the name of the domain 
  - Create a new (Global) Security Group within the domain.
  - Name this Security Group "Non-Admin Remote Desktop Users"

![image](https://github.com/user-attachments/assets/804bf433-d642-4f29-88eb-60d01e4fd202)

   - Now we will use this Powershell script to add all the created users in the "_EMPLOYEES" OU to the newly created "Non-Admin Remote Desktop Users" security group. (Thank you 
     Chat GPT! 😊)
![image](https://github.com/user-attachments/assets/6642390c-8d96-411e-8489-c48c42ef451f)

 - Here is the script I ran in powershell (as an administrator) replacing the placeholder values with my actual domain and OU information.

![image](https://github.com/user-attachments/assets/dca0f989-2ac6-4787-8dad-314d2306c7e3)


- Explaining each variable and function in the script:
  - $OU: The distinguished name (DN) of the organizational unit containing the users.
  - $GroupName: The name of the global security group.
  - Get-ADUser: Retrieves all users from the specified OU.
  - Add-ADGroupMember: Adds users to the group.
  - Write-Host: Prints the specified text to the screen. 

 
- Observe the members of the "Non-Admin Remote Desktop Users" security group have now been added from the _EMPLOYEES organization unit.

![image](https://github.com/user-attachments/assets/01769c3d-475b-4d93-9944-dae005ced262)


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> STEP 1.2: Create the Group Policy Object (GPO) </h3>

- Open the "Group Policy Management" console 
   - Right click the Organizational Unit where all the domain client computers reside "_CLIENTS" (this OU was created in the previous lab) and create the group policy 
     object (GPO) and link it here.
   - Name this new GPO "Enable RDP for Non-Admin Users) 

![image](https://github.com/user-attachments/assets/e62fe5fd-b13e-49bb-a8c9-e902c1c398bf) ![image](https://github.com/user-attachments/assets/f309b193-0dab-440a-873b-f8941cf54487)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> STEP 1.3: Edit the GPO </h3>

- Right Click the newly created GPO and click "Edit..."
    - This will automatically open the "Group Policy Management Editor"
    - Navigate to:
      - Computer Configuration > Policies > Administrative Templates > Windows Components >  Remote Desktop Services > Remote Desktop Session Host > Connections
      - Right click the setting "Allow users to connect remotely by using Remote Desktop Services
        - Edit it and set its state to "Enabled"
       
  ![image](https://github.com/user-attachments/assets/4b534b0e-0c55-46b4-a7f7-469bef330245)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> STEP 1.4: Add the Security Group to the Remote Desktop Users and Group </h3>

- Within the Group Policy Management Editor: 
  - Navigate to:
    - Computer Configuration > Policies > Windows Settings > Security Settings > Restricted Groups.
    - Right Click "Restricted Groups" and select "Add Group..."
    - Enter "Remote Desktop Users" and select "OK"
    - Under the "Members of this group" section select "Add..."
      - Add the previously created "Non-Admin Remote Desktop Users" security group
      - Click "OK"

![image](https://github.com/user-attachments/assets/63366d2c-4d1c-4e99-bce8-43b835bad579)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> STEP 1.5: Enable the Remote Desktop Protocol Inbound Firewall Rule on the host based firewalls for all the Domain Clients using this Group Policy Object  </h3>

- Navigate to:
  - Computer Configuration > Windows Settings > Security Settings > Windows Defender Firewall with Advanced Security > Inbound Rules
  - Enable the rule named "Remote Desktop - User Mode (TCP-In)
  - If this rule does not exist, then create a new one: 
    - Right Click "Inbound Rules" and select "New Rule..."
      - Choose "Port"
      - "TCP"
      - Specific Local Port: 3389 
 
![image](https://github.com/user-attachments/assets/365641ab-fe79-4974-a238-2142408d5119)

  - Allow the connection 

  ![image](https://github.com/user-attachments/assets/0ffaef4a-912c-498c-9a61-8f2a6acdf784)

 - Apply this rule for the Domain and Private Profiles.

![image](https://github.com/user-attachments/assets/5caacaad-70af-41e2-acb1-e5908d18fd30)

- Lastly, name this host-based firewall rule (ideally something along the lines of Allow RDP

![image](https://github.com/user-attachments/assets/cbaafb9e-0f13-4f44-a133-c31ad486b660)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


<h3> STEP 1.6: Deploy the GPO </h3>

- Login to the Domain client with an account that has Domain Admin privileges.
   - Run the command prompt or powershell as an administrator and run the following command:
     - "gpupdate /force" to force the GPO to be deployed to this client
     - Alternatively, you could wait for the GPO to be applied automatically.
    

![image](https://github.com/user-attachments/assets/ac8d86e2-70d0-47d0-8bd5-bcdc1a421a6b)



-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


<h3> STEP 1.7: Verify the GPO configurations </h3>

- Login to the Domain Client VM as one of the created Non-admin users.
  - One of my created users was "fapa.canuh" so I will login as this user to test the GPO deploment.
    - Remember the default password assigned to all these created users was "Password1" so use that to login

![image](https://github.com/user-attachments/assets/1d2a48c3-f052-4fc2-930c-43d8eb275739)


- Attempt to connect via Remote Desktop to verify the setup.

![image](https://github.com/user-attachments/assets/9ff726a4-352b-4e7c-bf37-48f486e81d98)



<h3> Welcome fapa.canuh!</h3>

![image](https://github.com/user-attachments/assets/7600ad4b-4b9a-47fa-b163-58ab4219342d)

- You can also run the command "gpresult /r" in the command prompt or powershell with admin privileges to see the applied GPO's on 
  this client 

![image](https://github.com/user-attachments/assets/358cf931-c0e0-4a3c-aa14-32f36d303aad)

</p>

</b> 

----------------------------------------------------------------------------------------------------------------------------------
<h2> STEP 2.0: Configure a Password Lockout Policy by Deploying a Group Policy Object. </h2>

<p> 

<h4> OPTIONAL STEP: Attempt to login to the Domain Client with any of the created users but use the incorrect password. </h4>

 - Perform this step multiple times.

![image](https://github.com/user-attachments/assets/6e6fbaa8-eb6f-4861-833a-14d7acaa7935)

 - Are you getting bored yet?
     - Well you have just essentially performed a brute force attack against this poor user's account (Shame on you!)😠
   
 - Don't worry I forgive you, but in the real world a cybersecurity best practice is to configure an account lockout policy after a certain number of incorrect password attempts in order to 
    prevent malicious threat actors from successfully performing that very same attack against our end users.

 - Now, lets get to stopping these brute force attacks! 😁

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> STEP 2.1: Login to the Domain Controller VM as a Domain Admin account (using the correct credentials this time👀) </h3>

![image](https://github.com/user-attachments/assets/2a7c2a0c-50dc-4c0b-93b4-530eb5008aa6)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<h3> STEP 2.2: Open the "Group Policy Management" console and edit the Default Domain Policy To configure the Account Lockout Policy within this Group Policy Object (GPO). </h3>

 - NOTE: It is more efficient to simply edit the "Default Domain Policy" as opposed to creating a new GPO for multiple reasons: 
   - It is automatically linked to our created domain (account lockout configurations and other authentication based events are domain wide operations)
       - As opposed to authorizing users for Remote Desktop Access this is more of a client level operation and thus linking the previously created GPO to the OU containing all our Domain 
          Clients was a more appropriate step to take.   
   - It takes precedence over any created Group Policy Objects by default...it is the default domain policy after all(very appropriately named😉)
       - Doing so will ensure consistent application across our entire domain and we will not risk it overriding a competing custom GPO that we would have created.
  
- Right click the "Default Domain Policy" and select Edit
  - This will open the "Group Policy Management Editor" 

![image](https://github.com/user-attachments/assets/282fdbab-fb3a-4047-a5bc-e734b0805b4a)

- Within the "Group Policy Management Editor":
  - Navigate to Computer Configuration > Windows Settins > Security Settings > Account Lockout Policy
  - Right Click "Account Lockout Threshold" and select Properties.
  - Set the lockout threshold to a reasonable number
  - Click "Apply"
  
  ![image](https://github.com/user-attachments/assets/1fd7b3b0-53a1-48b9-94ef-9653c9d752a4)

   - Notice that the other Account Lockout Policy Settings will automatically be assigned suggested values.
       - The suggested values are perfectly fine but feel free to modify them at your discretion.

![image](https://github.com/user-attachments/assets/9affaa81-1227-4fb9-9ee3-e35771a23c57)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<h3> STEP 2.3: Deploy the newly edited "Default Domain Policy" to the Domain Client VM. </h3>

- (Turn on the Domain Client VM if it was turned off) Login to the Domain Client VM as a Domain Admin account.
- Run the command prompt or powershell as an administrator and enter the command "gpupdate /force" to adminstratively "force" the newly edited GPO to be applied to the client VM.
- Also run the command "gpresult /r" to display the GPO's that have been applied to this Domain Client

![image](https://github.com/user-attachments/assets/fa8fd839-ede3-49a8-957f-54685b620853)


- Now log out of the Domain Client VM.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<h3> STEP 2.4: Verify the New Account Lockout Policy by Attempting to Login to the Client VM as any of the Created Users but With the Incorrect Password </h3>

 - Notice that once you have surpassed the configured account lockout threshold the account is now locked out.

![image](https://github.com/user-attachments/assets/a811350f-04b5-4843-8529-f979019c9a2d)

 - Well at least we stopped a potential brute force attack...but what if "bid.nek" from HR really did just forget his password and was just trying to guess it to acces his account.
     - To Active Directory and our Domain Controller it all looks the same.
     - If "bid.nek" cannot afford to wait the specified amount of time for the lockout to expire how can he gain access to his account.
       - "Find out next time on GPO Deployment Z Kai"...that was corny wasn't it? 😞
</p>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<h3> BONUS STEP: Unlock the Locked User Account and Reset the Password Using Active Directory </h3>

- Return to the Domain Controller VM and login as a Domain Administrator account.
- Open "Active Directory Users and Computers" and locate the created user account that has been locked out.
    - This account should be within the "_EMPLOYEES" organizational unit (along with the other created Non-Admin Domain Users).
 
- Right click the _EMPLOYEES OU and select "Find" then enter the username of the locked out account.
    - Click "Find Now" 

![image](https://github.com/user-attachments/assets/86189fcc-2b07-4d71-aca0-c7cee063e686)


- Right click the username of the locked out and select "Properties"
  - Select the "Account" Tab and select "Unlock Account"
  - Click "Apply" then "OK"

![image](https://github.com/user-attachments/assets/bba66cf8-6aaf-4bbc-b744-9b5a4c4f2601)


- Now to reset the password, right click the account and select "Reset Password"
  - Assign a new password to this user.
    - I chose "Password2"...very secure, I know 😅

  ![image](https://github.com/user-attachments/assets/1abc2a12-6518-4002-8419-3da2d1afb654)   ![image](https://github.com/user-attachments/assets/618d1a46-b087-4065-b67b-bea4fe7b394d)


<h3> Login to the Domain Client with the newly unlocked account using its new password.</h3>

![image](https://github.com/user-attachments/assets/018df6a8-a123-4bde-8702-df2d9c7613c6)


<h4> WELCOME BACK bid.nek!</h4>

![image](https://github.com/user-attachments/assets/80393e83-0ff4-4c1b-a7b1-4849f8db01a0)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<h2> Congratulations! You have successfully created and deployed GPO's within your Active Directory Domain. You even restored "bid.nek's" account (or whatever random username 
    was created by the Powershell script for your lab)😂. I'm sure whoever it was sends their kindest regards. </h2>
<p>

  - Now you can keep experimenting within your Active Directory Domain at your own discretion using your Domain Client and Domain Controller.
  - In fact, feel free to take a look at the lab I did to build a basic intuition for DNS (Domain Name System) which uses this same AD infrastructure that we have built in these 
     previous two labs.
     - It is linked right [here](https://github.com/CyberSecuriTim/dns-overview).
</p>


<h2> If are completely finished with this Active Directory environment and would like to completely delete your computing resources that are being hosted in Azure, then follow the steps below: </h2>

<h3> DELETION STEPS:</h3>

- Access the home page of the [Azure portal](https://portal.azure.com) and select or search for "Resource Groups"
- Select the Resource Group that contains all your computing resources (VM's, public IP address, Virutal Network Interface Cards etc.)
- Select "Delete resource group"
- Apply force delete
- Enter the name of the resoure group to confirm the deletion.

![image](https://github.com/user-attachments/assets/af2154a8-1f13-425a-a8bd-b203933290d8)


- Repeat the steps above to delete the Network Watcher Resource Group "NetworkWatcherRG"

![image](https://github.com/user-attachments/assets/b8c0137b-0aca-430c-a057-7b7100ec2cd2)
