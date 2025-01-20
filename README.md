<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Group Policy Objects within an Active Directory Domain in Azure</h1>
- This tutorial outlines the creation and deployment of group policy objects within an Active Directory domain consisting of Windows virtual machines hosted in Azure.<br />

- NOTE: To successfully perform this lab, a properly installed and configured Active Directory Domain is required including a functioning domain controller and at least one 
  client connected to this domain controller.
  - For a comprehensive step-by-step tutorial on how to achieve this please take a look at my previous lab linked [here](https://github.com/CyberSecuriTim/ad-configuration).
 
    
<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Configure Remote Desktop Access for Non-administrative users on the Domain client VM (and all Domain Clients) via a group policy object
- Step 2: Configure a Password Lockout Policy for all domain users via a group policy object.

<h2>Deployment and Configuration Steps</h2>

<h2> STEP 1: Deploy a Group Policy Object to Enable Remote Desktop Access for Non-admin users on all domain clients  </h2>

<p> 
  - 
</p>
