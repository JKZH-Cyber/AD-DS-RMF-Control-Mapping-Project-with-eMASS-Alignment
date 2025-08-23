# JKZH AD DS RMF/eMASS Mapping Project  

## Applied Skills and Knowledge  

Through this project, I demonstrated hands-on expertise in building and hardening an Active Directory Domain Controller in Microsoft Azure while aligning its configuration with the NIST Risk Management Framework (RMF). I deployed a Windows Server 2025 Datacenter VM, promoted it to a Domain Controller, and implemented a Baseline Security Group Policy Object (GPO) mapped to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  

While eMASS itself was not used in this lab, the project simulates how system owners prepare technical evidence for upload into eMASS. Each GPO hardening step and account management configuration was intentionally mapped to RMF controls, demonstrating compliance traceability in the same manner required for DoD systems.  

I enforced account lockout thresholds, password complexity, Kerberos policies, audit logging, Windows Defender Antivirus, firewall profiles, and removable media restrictions. I also created organizational units, test user accounts, and security groups to enforce proper role separation between privileged and standard accounts. All configurations were validated against RMF security controls to ensure the domain environment met access control, auditing, and system integrity requirements.  

This project reflects my ability to stand up AD DS infrastructure in the cloud while also thinking like a compliance assessor by mapping technical implementations to federal security controls.  

---

## Objective  

The goal of this project was to deploy a domain controller in Azure, configure Active Directory services, and apply security baseline GPOs that demonstrate compliance with RMF and eMASS control mappings. By doing so, I simulated how enterprises enforce security policies, enforce least privilege through group management, and document compliance within federal environments.  

---

## Skills Demonstrated  

- **Active Directory Deployment:** Promoted a Windows Server to a Domain Controller with DNS integration.  
- **Organizational Units & Accounts:** Created OUs for users, computers, and servers, and provisioned test accounts for standard and privileged access.  
- **Group Management & Role Separation:** Configured `JKZH_SecurityAdmins` and `JKZH_DomainUsers` groups, assigning users to enforce least privilege. Linked SecurityAdmins to Domain Admins and DomainUsers to Domain Users.  
- **Group Policy Enforcement:** Designed and implemented baseline GPOs for authentication, account lockout, Kerberos, audit policies, firewall, and endpoint protection.  
- **RMF/eMASS Mapping:** Mapped all security configurations to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  
- **Windows Security Hardening:** Applied password policies, Kerberos ticket restrictions, logon banners, and removable media restrictions.  
- **Endpoint Protection:** Configured Microsoft Defender Antivirus and Windows Defender Firewall.  
- **Audit & Log Management:** Verified policy application with `gpupdate` and `gpresult`. Captured and filtered Security event logs (Event ID 4728) to confirm group membership changes were logged.  

---

## Tools Used  

- **Microsoft Azure:** Hosted the virtualized lab environment.  
- **Windows Server 2025 Datacenter:** Domain Controller with AD DS and DNS roles.  
- **Active Directory Users and Computers (ADUC):** Managed users, groups, and OUs.  
- **Group Policy Management Console (GPMC):** Created and linked security baselines.  
- **Windows Defender Antivirus:** Provided malware protection.  
- **Event Viewer:** Verified audit policies and log collection.  
- **[NIST SP 800-53 Rev. 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf):** Security and Privacy Controls for Information Systems and Organizations 

---

## Overview/Visual Diagram:
<img width="2021" height="1011" alt="AD DS RMF-eMASS Mapping Project drawio" src="https://github.com/user-attachments/assets/b7b5939b-9759-4e68-902b-79435c651563" />

---

## Azure Deployment Build Details (decommissioned)  

| Item | Value |
| --- | --- |
| Subscription | Azure subscription 1 |
| Region | East US |
| **Resource Group** | `jkzh-emass` |
| **Virtual Network** | `jkzh-emass-vnet` |
| Address Space | `10.0.0.0/16` |
| Subnet | `default` (`10.0.0.0/24`) |
| **Network Security Group** | `jkzh-emass-nsg` |
| **VM Name** | `jkzh-dc01` |
| Image | Windows Server 2025 Datacenter, Azure Edition |
| Size | Standard D2ds v6 (2 vCPU, 8 GiB RAM) |
| Disk Controller | NVMe |
| Availability Zone | 1 |
| Public IP (at build) | `172.210.8.4` |
| Private IP | `10.0.0.4` |
| VNet/Subnet binding | `jkzh-emass-vnet` / `default` |

---

## 🎯 Steps Summarized  

1. Signed into the Azure Portal and verified subscription access.  
2. Created a Resource Group, Virtual Network (VNet), subnet, and Network Security Group (NSG) in East US.  
3. Provisioned a Windows Server 2025 VM `jkzh-dc01`, attached it to the VNet and NSG, and configured NSG inbound rules to allow RDP from my public IP while denying all other inbound traffic.  
4. RDP’d into `jkzh-dc01`, installed updates, and promoted the server to a Domain Controller for the `jkzh.lab` domain with DNS integration.  
5. Created Organizational Units (OUs) for users, computers, and servers, and provisioned two test accounts: John Doe (standard) and Security Analyst (privileged).  
6. Created security groups `JKZH_SecurityAdmins` and `JKZH_DomainUsers`. Added Security Analyst to SecurityAdmins and John Doe to DomainUsers. Linked SecurityAdmins to Domain Admins and DomainUsers to Domain Users.  
7. Created and linked a Baseline Security GPO to the `jkzh.lab` domain, enforcing account lockout, password complexity, Kerberos, audit logging, Defender AV, firewall, and removable media restrictions.  
8. Verified GPO application with `gpupdate /force` and `gpresult /r`.  
9. Audited Security event logs (Event ID 4728) in Event Viewer to confirm group membership changes were logged successfully.  
10. Mapped all configurations to NIST SP 800-53 RMF control families (commonly tracked in eMASS) to demonstrate compliance traceability.  
11. Decommissioned the `jkzh-emass` resource group in Azure, removing all lab resources to close out the environment securely.  
 
---

## 🚀 Step 1: Sign into Azure Portal  

1. Navigated to [https://portal.azure.com](https://portal.azure.com)  
2. Logged in with my Azure account credentials.  
3. Verified subscription access before proceeding.  
- See below: Azure welcome page
<img width="1551" height="503" alt="Screenshot 2025-08-22 231338" src="https://github.com/user-attachments/assets/6eaa5f65-7fb4-4012-9628-7e37a9c44065" />


---

## 🚀 Step 2: Azure Build, Networking and VM  

1. **Created Resource Group**  
   - Navigated to **Resource groups** in the Azure Portal and selected **+ Create**.  
   - Named it `jkzh-emass`, Region: East US.  
   - Selected **Review + Create**, then **Create**.  
- See below: Resource Group `jkzh-emass`  
<img width="1678" height="262" alt="Screenshot 2025-08-22 232226" src="https://github.com/user-attachments/assets/08c48357-6bc5-47c0-9060-ec63cf2ac8c7" />

2. **Created Virtual Network**  
   - Searched for **Virtual networks** and selected **+ Create**.  
   - Named it `jkzh-emass-vnet`.  
   - Defined address space: `10.0.0.0/16`.  
   - Added subnet: `default` (`10.0.0.0/24`).  
   - Associated it with Resource Group `jkzh-emass`.  

- See below: VNet `jkzh-emass-vnet`  
<img width="1684" height="728" alt="Screenshot 2025-08-22 232402" src="https://github.com/user-attachments/assets/9be1e909-d6dd-4b1a-b95b-b42b7412205e" />
<img width="1690" height="602" alt="Screenshot 2025-08-22 232507" src="https://github.com/user-attachments/assets/492ef75e-7980-469b-845f-5795c9f21125" />

3. **Created Network Security Group (NSG)**  
   - Searched for **Network security groups** and selected **+ Create**.  
   - Named it `jkzh-emass-nsg`, Resource Group: `jkzh-emass`.  
   - After deployment, opened the NSG and configured inbound rules:  
     - Allowed RDP, port 3389, source **My IP**, priority **100**.  
     - Denied all other inbound traffic by default.  
- See below: NSG `jkzh-emass-nsg`  
<img width="1681" height="606" alt="Screenshot 2025-08-22 233138" src="https://github.com/user-attachments/assets/df51f438-e3e0-4665-8d76-cd482f1e860f" />

4. **Provisioned the VM**  
   - Navigated to **Virtual Machines > + Create > Azure virtual machine**.  
   - Named it `jkzh-dc01`.  
   - Selected image: Windows Server 2025 Datacenter, Azure Edition.  
   - Chose size: Standard D2ds v6 (2 vCPU, 8 GiB RAM).  
   - Placed in availability zone 1.  
   - Attached NIC to `jkzh-emass-vnet` on `default` subnet.  
   - Associated NSG `jkzh-emass-nsg`.  
   - Assigned Public IP (dynamic, `172.210.8.4` at build time) and Private IP (`10.0.0.4`).  
   - Enabled Trusted launch, secure boot, and vTPM.  
   - Selected **Review + Create**, then **Create**.  
- See below: VM list showing `jkzh-dc01`  
<img width="1680" height="485" alt="Screenshot 2025-08-22 235631" src="https://github.com/user-attachments/assets/60e1c1a7-fe55-4cf5-810f-61ec407cdd0c" />

- See below: VM overview for `jkzh-dc01`  
<img width="1667" height="1211" alt="Screenshot 2025-08-22 235752" src="https://github.com/user-attachments/assets/0c51d088-eda8-495f-81b7-25ed75b1c81c" />


---

## 🚀 Step 3: Promote to Domain Controller  

1. RDP’d to `jkzh-dc01` using the public IP.  
   - Adjusted system time/date to match Azure region.  
   - Installed Windows Updates to bring server current.  
- See below: RDP session into `jkzh-dc01`  
<img width="810" height="554" alt="Screenshot 2025-08-23 000830" src="https://github.com/user-attachments/assets/49331cba-e323-4bcc-bab4-be58ee87bc6c" />
<img width="1063" height="819" alt="Screenshot 2025-08-23 001722" src="https://github.com/user-attachments/assets/c481b282-6659-40eb-a0bc-55e7e451b8e0" />

2. Opened **Server Manager > Add Roles and Features**.  
   - Selected **Role-based or feature-based installation**.  
   - Selected the local server.  
   - Added roles: **Active Directory Domain Services (AD DS)** and **DNS Server**.  
- See below: Add Roles and Features Wizard
<img width="1429" height="849" alt="Screenshot 2025-08-23 002656" src="https://github.com/user-attachments/assets/fc7c1431-0acf-415c-9b1a-e4e977d0c9d4" />
<img width="844" height="615" alt="Screenshot 2025-08-23 002733" src="https://github.com/user-attachments/assets/b4abf516-2fa4-4267-9ad0-e114927ced9e" />
<img width="1427" height="843" alt="Screenshot 2025-08-23 002912" src="https://github.com/user-attachments/assets/21f11d91-c1aa-49db-8813-c83437de25cb" />
<img width="1460" height="943" alt="Screenshot 2025-08-23 003154" src="https://github.com/user-attachments/assets/007741e2-cf9b-41a3-ad09-566fb39b0db8" />

3. After installation, clicked the notification flag in Server Manager and selected **Promote this server to a domain controller**.  
- See below: Promote this server to domain controller
<img width="1430" height="843" alt="Screenshot 2025-08-23 003229" src="https://github.com/user-attachments/assets/3a876f14-9863-4200-83b2-3f57e5b4e378" />

4. Configured the promotion wizard:  
   - Deployment configuration: **Added a new forest**, root domain: `jkzh.lab`.  
   - Set forest functional level: **Windows Server 2025**.  
   - Set domain functional level: **Windows Server 2025**.  
   - Checked **DNS Server** and **Global Catalog (GC)**.  
   - Supplied Directory Services Restore Mode (DSRM) password.  
   - Set NetBIOS name: `JKZH`.  
   - Defined paths: `C:\Windows\NTDS` and `C:\Windows\SYSVOL`.  
   - Completed prerequisite check (acknowledged warnings).  
   - Rebooted server automatically after promotion.  
- See below: AD DS promotion wizard screenshots  
<img width="787" height="568" alt="Screenshot 2025-08-23 003308" src="https://github.com/user-attachments/assets/735695c4-dbf3-4acf-9925-1dd489a106dd" />
<img width="780" height="578" alt="Screenshot 2025-08-23 003402" src="https://github.com/user-attachments/assets/f713fbad-43d9-4586-8601-254f2e9683af" />
<img width="784" height="592" alt="Screenshot 2025-08-23 003552" src="https://github.com/user-attachments/assets/c71b4034-0110-4d52-bf70-4eb22dafe3e0" />
<img width="791" height="585" alt="Screenshot 2025-08-23 003605" src="https://github.com/user-attachments/assets/9513e1fd-2ebd-42a5-8d07-4fe629357216" />
<img width="784" height="573" alt="Screenshot 2025-08-23 003614" src="https://github.com/user-attachments/assets/05c14df2-99f5-45aa-835c-323cd2fbec68" />
<img width="793" height="582" alt="Screenshot 2025-08-23 003641" src="https://github.com/user-attachments/assets/749813a6-3bba-4439-88cb-26d8a577d548" />
<img width="778" height="585" alt="Screenshot 2025-08-23 003730" src="https://github.com/user-attachments/assets/5c632cf8-9a59-4862-a1b1-2107995839dc" />
<img width="1436" height="842" alt="Screenshot 2025-08-23 003839" src="https://github.com/user-attachments/assets/0ad8939f-f864-4baf-8e07-732ef3a0b69b" />
<img width="1433" height="919" alt="Screenshot 2025-08-23 004055" src="https://github.com/user-attachments/assets/b5dddf34-9c85-4edb-bf8d-f9547c45aa35" />
<img width="1433" height="848" alt="Screenshot 2025-08-23 004547" src="https://github.com/user-attachments/assets/e40a2dbb-b817-4a43-88d2-dfdfb74ffe5a" />

--- 

## 🚀 Step 4: Active Directory Configuration  

1. Logged in as `jkzh.lab\emassadmin`.  

2. Opened **Active Directory Users and Computers (ADUC)**.  
   - Expanded **Forest: jkzh.lab > Domains > jkzh.lab**.  
- See below: ADUC showing `jkzh.lab` domain  
<img width="1008" height="747" alt="Screenshot 2025-08-23 004804" src="https://github.com/user-attachments/assets/10044601-dfc0-4483-925d-eb392404cb45" />

3. Created Organizational Units (OUs):  
   - Right-clicked the domain root (`jkzh.lab`) > **New > Organizational Unit**.  
   - Created the following OUs:  
     - `JKZH_Users`  
     - `JKZH_Computers`  
     - `JKZH_Servers`  
- See below: ADUC showing OU structure  
<img width="1017" height="754" alt="Screenshot 2025-08-23 004954" src="https://github.com/user-attachments/assets/86352422-dbd9-4860-ae48-057b5ea2603e" />
<img width="1004" height="747" alt="Screenshot 2025-08-23 005032" src="https://github.com/user-attachments/assets/e2a20234-f266-4c75-9081-f76ebe1cf9e6" />


4. Created test user accounts:  
   - Right-clicked `JKZH_Users` > **New > User**.  
   - Created **John Doe** (`jdoe@jkzh.lab`) with a compliant password.  
   - Repeated for **Security Analyst** (`secanalyst@jkzh.lab`).  
- See below: ADUC showing user creation wizard and both users in OU  
<img width="1043" height="734" alt="Screenshot 2025-08-23 005205" src="https://github.com/user-attachments/assets/eacc67a5-bd23-45ec-a4f0-46b1a6272ab8" />
<img width="996" height="731" alt="Screenshot 2025-08-23 005248" src="https://github.com/user-attachments/assets/5a097f85-8da2-4ec2-84c1-950da94c3a80" />
<img width="991" height="583" alt="Screenshot 2025-08-23 005337" src="https://github.com/user-attachments/assets/ffa81530-10ea-4fbe-9f3b-5a71d04b4a18" />
<img width="463" height="396" alt="Screenshot 2025-08-23 005513" src="https://github.com/user-attachments/assets/35bdc5a4-216c-4cea-be1c-46297956b046" />
<img width="1002" height="741" alt="Screenshot 2025-08-23 005601" src="https://github.com/user-attachments/assets/0f6760e8-0c48-448a-bbaa-cf92a9425b08" />

5. Verified that user accounts will be inheriting baseline domain policies that I'll be implementing (password complexity, lockout).  
 
---

## 🚀 Step 5: Create and Link Baseline Security GPO  

1. Opened **Group Policy Management Console (GPMC)** on `jkzh-dc01`.  
   - Expanded **Forest: jkzh.lab > Domains > jkzh.lab**.  

2. Right-clicked the domain root `jkzh.lab` > selected **Create a GPO in this domain, and Link it here...**.  

3. Named the GPO:  
   - `Baseline Security`  

4. Confirmed it appeared under **Linked Group Policy Objects** for `jkzh.lab`.  

5. Right-clicked `Baseline Security` > selected **Edit** and configured the following:  
   - **Account Lockout Policy** - threshold, duration, reset counter.  
   - **Password Policy** - history, length, complexity.  
   - **Kerberos Policy** - ticket lifetime, renewal.  
   - **Local Policies (Security Options)** - logon banner, audit fail shutdown.  
   - **Audit Policy** - account logon, logon events, privilege use, policy change.  
   - **Event Log** - retention method, maximum sizes.  
   - **Windows Defender Antivirus** - real-time protection, script scanning.  
   - **Windows Defender Firewall** - domain, private, public profiles set to on.  
   - **Removable Storage Access** - denied all removable media.  

6. Confirmed `Baseline Security` GPO was linked at domain root.  
- See below: GPMC showing Baseline Security GPO linked to `jkzh.lab` domain  
<img width="983" height="745" alt="Screenshot 2025-08-23 005855" src="https://github.com/user-attachments/assets/c5db9c92-ddd2-42c5-9c76-2487c9337995" />
<img width="1068" height="773" alt="Screenshot 2025-08-23 010042" src="https://github.com/user-attachments/assets/54fb9486-a7fc-4622-bc9b-3c858c4c0f7d" />

6. Verified that the `Baseline Security` GPO and its domain link were **enabled by default**.  

7. Ran `gpupdate /force` on `jkzh-dc01` using PowerShell to ensure the new settings applied immediately.  

8. Verified application of the GPO using:  
   - **gpresult /r** – confirmed `Baseline Security` was applied under Computer Settings.  
- See below: screenshots of gpupdate and gpresult.  
<img width="1145" height="661" alt="Screenshot 2025-08-23 130633" src="https://github.com/user-attachments/assets/5eb80f5d-f8dc-4072-9785-21ad20697c33" />
<img width="1151" height="662" alt="Screenshot 2025-08-23 130653" src="https://github.com/user-attachments/assets/dc0d1aea-5e1d-4b45-acf9-c674f4c74af3" />

---

### 🔑 Account Lockout Policy  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy`  

- Account lockout duration: **15 minutes**  
- Account lockout threshold: **5 invalid logon attempts**  
- Reset account lockout counter after: **15 minutes**  
- Allow Administrator account lockout: **Enabled**  

See below: Account Lockout Policy  
<img width="1074" height="784" alt="Screenshot 2025-08-23 011015" src="https://github.com/user-attachments/assets/8d0598b4-21a2-4c6b-8bd0-deb52ff1498e" />

---

### 🔑 Password Policy  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy`  

- Enforce password history: **2 passwords remembered**  
- Maximum password age: **60 days**  
- Minimum password age: **30 days**  
- Minimum password length: **10 characters**  
- Password must meet complexity requirements: **Enabled**  

See below: Password Policy  
<img width="1089" height="772" alt="Screenshot 2025-08-23 010905" src="https://github.com/user-attachments/assets/7ac6f25a-b653-4113-941e-fe9482b5630a" />

---

### 🔑 Kerberos Policy  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Kerberos Policy`  

- Enforce user logon restrictions: **Enabled**  
- Maximum lifetime for service ticket: **600 minutes**  
- Maximum lifetime for user ticket: **10 hours**  
- Maximum lifetime for user ticket renewal: **7 days**  
- Maximum tolerance for computer clock sync: **5 minutes**  

See below: Kerberos Policy  
<img width="1081" height="776" alt="Screenshot 2025-08-23 011136" src="https://github.com/user-attachments/assets/703ce10c-4275-4640-81f8-7cdd63374873" />

---

### 🔒 Local Policies, Security Options  

- Audit, shut down system immediately if unable to log security audits: **Enabled**  
- Interactive logon, message text for users attempting to log on: **Banner configured**  
- Interactive logon, do not require CTRL+ALT+DEL: **Enabled**  
- Interactive logon, display last username: **Disabled**  

See below: Security Options  
<img width="1084" height="784" alt="Screenshot 2025-08-23 011924" src="https://github.com/user-attachments/assets/e11151a1-10cd-41a0-a52e-4ef3ab8da8e7" />
<img width="1063" height="784" alt="Screenshot 2025-08-23 012001" src="https://github.com/user-attachments/assets/2f4208ae-7ea6-45b2-b83d-0d354bc4e3b7" />

---

### 📜 Audit Policy  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Audit Policy`  

- Audit account logon events: **Success, Failure**  
- Audit account management: **Success, Failure**  
- Audit logon events: **Success, Failure**  
- Audit object access: **Failure**  
- Audit policy change: **Success, Failure**  
- Audit privilege use: **Failure**  
- Audit system events: **Success, Failure**  

See below: Audit Policy  
<img width="1099" height="771" alt="Screenshot 2025-08-23 012526" src="https://github.com/user-attachments/assets/cc94fb72-4f92-490d-8f51-c9aa113e16d3" />

---

### 📜 Event Log Settings  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Event Log`  

- Maximum application log size: **32,768 KB**  
- Maximum security log size: **196,608 KB**  
- Maximum system log size: **32,768 KB**  
- Retain security log: **7 days**  
- Retention method for security log: **By days**  

See below: Event Log Settings  
<img width="1069" height="771" alt="Screenshot 2025-08-23 013254" src="https://github.com/user-attachments/assets/587eb4b3-a1cd-43e3-a783-e7e65d294aae" />

---

### 🛡️ Windows Defender Antivirus  
Path: `Computer Configuration > Policies > Administrative Templates > Windows Components > Microsoft Defender Antivirus > Real-time Protection`  

- Turn on behavior monitoring: **Enabled**  
- Scan all downloaded files and attachments: **Enabled**  
- Monitor file and program activity on your computer: **Enabled**  
- Turn on script scanning: **Enabled**  

See below: Defender AV settings  
<img width="1193" height="786" alt="Screenshot 2025-08-23 013953" src="https://github.com/user-attachments/assets/2579bcff-3de6-4811-add7-226a7a0d13e7" />

---

### 🛡️ Windows Defender Firewall  
Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Windows Defender Firewall with Advanced Security`  

- Domain profile: **On**, inbound Block, outbound Allow  
- Private profile: **On**, inbound Block, outbound Allow  
- Public profile: **On**, inbound Block, outbound Allow  

See below: Firewall profiles  
<img width="1201" height="777" alt="Screenshot 2025-08-23 014401" src="https://github.com/user-attachments/assets/86b898bd-decc-4048-aa30-deab836a5371" />

---

### 💽 Removable Media Control  
Path: `Computer Configuration > Policies > Administrative Templates > System > Removable Storage Access`  

- All removable storage classes, deny all access: **Enabled**  

See below: Removable Media Policy  
<img width="1216" height="781" alt="Screenshot 2025-08-23 015604" src="https://github.com/user-attachments/assets/79453dfa-56bc-498f-94b3-43c59b4a989e" />


---

## 📊 RMF/eMASS Control Coverage  

| Control | Policy Implemented | How Satisfied |
|---------|-------------------|---------------|
| IA-5 | Password Policy | Enforces strong authentication |
| AC-7 | Account Lockout | Prevents brute-force attacks |
| IA-2, SC-12 | Kerberos Policy | Secures authentication tickets |
| AU-2, AU-6 | Audit Policy | Collects logs for accountability |
| AU-11 | Event Log Retention | Ensures logs are retained and protected |
| AC-8 | Logon Banner | Provides system use notification |
| AU-5 | Shutdown on Audit Fail | Prevents silent log failures |
| SI-3 | Defender Antivirus | Malware scanning and protection |
| SC-7 | Firewall | Restricts unauthorized connections |
| MP-7, SC-28 | Removable Media | Denies unauthorized USB access |  

For full details on the security and privacy control catalog, see  
**[NIST SP 800-53 Rev. 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)** - *Security and Privacy Controls for Information Systems and Organizations*.  

---

## 🚀 Step 6: Create Security Groups and Assign Users  

1. Opened **Active Directory Users and Computers (ADUC)**.  
2. Navigated to **jkzh.lab > JKZH_Users**.  
3. Right-clicked on `JKZH_Users` > selected **New > Group**.  
4. Entered group name `JKZH_SecurityAdmins`, selected **Global** as the scope, and **Security** as the type.  
- See below: Group creation dialog for `JKZH_SecurityAdmins`.  
<img width="1050" height="749" alt="Screenshot 2025-08-23 133357" src="https://github.com/user-attachments/assets/fe8d2ab9-6988-48fd-abcf-f092c654ea87" />
<img width="1012" height="742" alt="Screenshot 2025-08-23 133437" src="https://github.com/user-attachments/assets/1a7b3ced-21e6-4350-89a4-b47252d6d689" />

5. Verified that `JKZH_SecurityAdmins` appeared under the `JKZH_Users` OU.   

6. Right-clicked on `JKZH_Users` > selected **New > Group**.  

7. Entered group name `JKZH_DomainUsers`, selected **Global** as the scope, and **Security** as the type.  
- See below: ADUC showing `JKZH_SecurityAdmins` & `JKZH_DomainUsers` groups.
<img width="1021" height="748" alt="Screenshot 2025-08-23 133527" src="https://github.com/user-attachments/assets/966cf2ce-6d0a-4e98-b173-b1561b23dee5" />


8. Right-clicked on **Security Analyst** > selected **Add to a group…** > typed `JKZH_SecurityAdmins` > clicked **Check Names** > pressed **OK**.  
- See below: Security Analyst successfully added to `JKZH_SecurityAdmins`.  
<img width="1025" height="747" alt="Screenshot 2025-08-23 133919" src="https://github.com/user-attachments/assets/b7f97776-d89c-41d0-a18b-c83d814b7100" />
<img width="1027" height="753" alt="Screenshot 2025-08-23 134048" src="https://github.com/user-attachments/assets/53712e7f-afe4-4214-9b83-b52584abeb62" />
<img width="1024" height="754" alt="Screenshot 2025-08-23 134056" src="https://github.com/user-attachments/assets/c5603382-0f26-4cd0-b9f3-d966d6db428a" />

9. Right-clicked on **John Doe** > selected **Add to a group…** > typed `JKZH_DomainUsers` > clicked **Check Names** > pressed **OK**.  
- See below: John Doe successfully added to `JKZH_DomainUsers`.  
<img width="1011" height="739" alt="Screenshot 2025-08-23 134125" src="https://github.com/user-attachments/assets/3e37abc0-9032-448c-86d9-c0b3bf4fc4a2" />
<img width="1003" height="737" alt="Screenshot 2025-08-23 134142" src="https://github.com/user-attachments/assets/cfb2955f-02d1-4512-8406-ad68b62ebe8c" />

10. Opened **Properties** for each group and confirmed members appeared under the **Members** tab.  
- See below: Group membership windows for both `JKZH_SecurityAdmins` and `JKZH_DomainUsers`.  
<img width="1122" height="758" alt="Screenshot 2025-08-23 134242" src="https://github.com/user-attachments/assets/bb02354b-b1d6-4a76-bfc2-230e78da9d6e" />

---

## 🚀 Step 7: Assign Group Roles with Built-in Memberships  

1. In **ADUC**, navigated to **jkzh.lab > JKZH_Users**.  
2. Right-clicked on the `JKZH_SecurityAdmins` group > selected **Properties**.  
3. Opened the **Member Of** tab.  
4. Clicked **Add…** > typed `Domain Admins` > clicked **Check Names** > pressed **OK**.  
5. Verified that `JKZH_SecurityAdmins` appeared as a member of **Domain Admins**.  
- See below: JKZH_SecurityAdmins properties showing Domain Admins membership.  
<img width="1110" height="792" alt="Screenshot 2025-08-23 134537" src="https://github.com/user-attachments/assets/7310dd9b-2f2b-42bf-bc4e-c3b92af20486" />
<img width="1047" height="743" alt="Screenshot 2025-08-23 134602" src="https://github.com/user-attachments/assets/b3afdc36-f17b-4825-b4e6-eaaa77328d5f" />
<img width="997" height="744" alt="Screenshot 2025-08-23 134611" src="https://github.com/user-attachments/assets/e19b924f-1b56-457f-9c57-a097f2f9bec3" />

6. Repeated the process for the `JKZH_DomainUsers` group.  
   - Right-clicked on `JKZH_DomainUsers` > selected **Properties** > opened the **Member Of** tab.  
   - Clicked **Add…** > typed `Domain Users` > clicked **Check Names** > pressed **OK**.  
- See below: JKZH_DomainUsers properties showing Domain Users membership.  
<img width="1002" height="736" alt="Screenshot 2025-08-23 135121" src="https://github.com/user-attachments/assets/e2ef6cfe-076d-45e9-8d2b-30d6f5770d55" />


7. Confirmed role separation:  
   - **Security Analyst** (in `JKZH_SecurityAdmins`) inherited **Domain Admin rights**.  
   - **John Doe** (in `JKZH_DomainUsers`) retained **Domain User privileges only**.  

---

## 🚀 Step 8: Audit and Verify Group Membership Changes  

1. Open **Event Viewer** on `jkzh-dc01`.  
   - Navigated to **Start > Windows Administrative Tools > Event Viewer**.  
- See below: Event Viewer Overview  
<img width="1434" height="843" alt="Screenshot 2025-08-23 140254" src="https://github.com/user-attachments/assets/16739561-04c4-4f64-aac6-e65d5028b752" />

2. Expanded **Windows Logs > Security** to review domain security logs.  
- See below: Security log overview  
<img width="1435" height="846" alt="Screenshot 2025-08-23 140309" src="https://github.com/user-attachments/assets/d30b58a0-3252-4bd2-a50f-99ddedf2981f" />

3. Right-clicked on **Security** and selected **Filter Current Log...**.  
   - Entered Event ID `4728` to filter for events related to group membership changes.  
   - Clicked **OK** to apply the filter.  
- See below: Filter applied with Event ID 4728  
<img width="1434" height="847" alt="Screenshot 2025-08-23 140502" src="https://github.com/user-attachments/assets/e8798614-6735-4feb-a31b-54093ea9594f" />

4. Verified logs showing that users were successfully added to groups:  
   - **John Doe** added to `JKZH_DomainUsers`  
   - **Security Analyst** added to `JKZH_SecurityAdmins`  
   - Both events logged with **Audit Success** (Event ID 4728).  
- See below: A couple event log entries confirming group membership changes  
<img width="1434" height="850" alt="Screenshot 2025-08-23 140622" src="https://github.com/user-attachments/assets/aa062638-3675-46f1-8126-09aa28608a17" />
<img width="1434" height="849" alt="Screenshot 2025-08-23 140636" src="https://github.com/user-attachments/assets/fe8449dd-6211-4a88-b310-2388e8b506d3" />

---

## 📊 Additional RMF/eMASS Control Coverage Related to Security Events and Auditing

| Control    | Policy Implemented     | How Satisfied                                                                                      |
| ---------- | ---------------------- | -------------------------------------------------------------------------------------------------- |
| AC-2       | Account Management     | Created and managed standard and privileged security groups                                        |
| AC-3       | Access Enforcement     | Restricted admin rights to `JKZH_SecurityAdmins`, while `JKZH_DomainUsers` remained non-privileged |
| AC-6       | Least Privilege        | Ensured separation between admin and standard accounts                                             |
| AU-2, AU-6 | Auditable Events       | Verified group membership changes were logged (Event ID 4728)                                      |

Again, for full details on the security and privacy control catalog, see  
**[NIST SP 800-53 Rev. 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)** - *Security and Privacy Controls for Information Systems and Organizations*. 

---

## 🚀 Step 9: Decommission Lab Resources  

Once testing and validation were complete, I decommissioned the environment to ensure no costs or exposed resources remained active in Azure.  

1. Logged into the [Azure Portal](https://portal.azure.com).  
2. Navigated to **Resource groups**.  
3. Located the resource group `jkzh-emass`.  
4. Right-clicked `jkzh-emass` > selected **Delete resource group**.  
5. Entered the resource group name to confirm deletion.  
6. Verified all associated resources (VM `jkzh-dc01`, VNet, NSG, Public IP, etc.) were removed.  

This step simulated the **Disposal** phase of the RMF system life cycle, where environments must be securely decommissioned to avoid unnecessary exposure or costs.  

- See below: Resource group `jkzh-emass` successfully deleted  
<img width="559" height="778" alt="Screenshot 2025-08-23 173827" src="https://github.com/user-attachments/assets/e6136527-0568-4a7b-8245-9382530445dd" />
<img width="468" height="196" alt="Screenshot 2025-08-23 173948" src="https://github.com/user-attachments/assets/6e57834b-8923-4285-b0d5-daecd8daa989" />

---

## 📂 Technical Evidence Produced  

This project generated the same types of technical evidence that system owners would upload into eMASS as part of RMF compliance documentation:  

- **Azure Infrastructure Screenshots** - Resource Group, VNet, NSG, and VM build details.  
- **Domain Controller Configuration** - AD DS role installation, DC promotion, and ADUC domain verification.  
- **Organizational Units and Accounts** - Created OUs for users, computers, and servers, with standard (John Doe) and privileged (Security Analyst) accounts.  
- **Group Management Evidence** - Configured `JKZH_SecurityAdmins` and `JKZH_DomainUsers` security groups to enforce role separation. Added Security Analyst to SecurityAdmins and John Doe to DomainUsers. Linked SecurityAdmins to Domain Admins for elevated rights.  
- **GPO Enforcement Evidence** - Screenshots of account lockout, password complexity, Kerberos, audit policies, event log settings, Defender AV, firewall profiles, and removable media restrictions.  
- **Audit Log Verification** - Captured security event logs showing group membership changes (Event ID 4728) for accountability and compliance evidence.  
- **Compliance Mapping** - Each configuration mapped to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  

While this lab was not connected to eMASS, the collected screenshots and configuration outputs represent the type of artifacts uploaded into eMASS as compliance evidence for DoD systems.  

---

## 🧪 Additional Validation Tests  

1. **RDP Access Enforcement**  
   - Attempted RDP login as **John Doe (standard user)**.  
   - Connection was denied with the message: *"The connection was denied because the user account is not authorized for remote login."*  
   - Confirmed role separation and that standard users cannot remotely administer the Domain Controller.  
- See below: Failed RDP attempt as `jdoe`.  
<img width="581" height="414" alt="Screenshot 2025-08-23 143028" src="https://github.com/user-attachments/assets/27dc1eab-2e4e-4126-89af-9c664cc6c4fe" />

2. **Privileged Access Confirmation**  
   - Attempted RDP login as **Security Analyst (privileged user)**.  
   - Successfully connected to `jkzh-dc01`.  
   - Verified elevated privileges by opening PowerShell and running `whoami`, which returned `jkzh\secanalyst`.  
- See below: Successful RDP session as `secanalyst`.  
<img width="906" height="528" alt="Screenshot 2025-08-23 143347" src="https://github.com/user-attachments/assets/590244da-b135-491d-8d7b-b9a1639e2b9c" />
<img width="1454" height="950" alt="Screenshot 2025-08-23 143225" src="https://github.com/user-attachments/assets/db59d4bc-4d55-4bd7-af3c-e8efb18d1414" />
<img width="1453" height="949" alt="Screenshot 2025-08-23 143239" src="https://github.com/user-attachments/assets/ac067e65-9cad-437d-9a18-9d5e0ba715c4" />

3. **Screen Lockout Policy**  
   - Tested inactivity lockout with the `emassadmin` account.  
   - After **5 minutes of inactivity**, the account was locked, requiring reauthentication at the login screen.  
   - This confirmed the **Account Lockout Policy** and inactivity enforcement was working correctly.  
- See below: Lockout enforcement on `emassadmin`.  
<img width="1464" height="952" alt="Screenshot 2025-08-23 140009" src="https://github.com/user-attachments/assets/b1504147-a05b-41ff-92a0-80c4ad2edc3e" />

## 📊 Additional RMF/eMASS Control Mapping Related to Validation Tests

| Control  | Policy Validated                 | How Satisfied                                                                 |
|----------|----------------------------------|-------------------------------------------------------------------------------|
| AC-2     | Account Management               | Verified proper separation between standard and privileged accounts           |
| AC-3     | Access Enforcement               | Denied remote login for `jdoe`, allowed only for privileged Security Analyst  |
| AC-6     | Least Privilege                  | Confirmed only SecurityAdmins inherit Domain Admin rights                     |
| AC-7     | Account Lockout / Inactivity     | Confirmed inactivity lockout triggered after 5 minutes                        |

Again, for full details on the security and privacy control catalog, see  
**[NIST SP 800-53 Rev. 5](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)** - *Security and Privacy Controls for Information Systems and Organizations*. 

---

## ✅ Outcome  

By completing this project, I demonstrated both technical expertise in building and securing AD DS in Azure and compliance-focused thinking by mapping system configurations to NIST RMF control families. I not only deployed a hardened domain controller and enforced baseline GPOs but also created organizational units, accounts, and security groups that reflected proper role separation.  

Audit logs confirmed that group membership changes were captured and traceable, showing accountability and alignment with federal compliance standards. This dual approach mirrors the expectations of federal compliance environments, where system hardening and control documentation must work hand-in-hand.  

---

## ⚠️ Disclaimer  

This lab was built strictly for educational purposes and cybersecurity skill development. The environment was deployed in a controlled Azure setting with no sensitive or production data. All resources were decommissioned before making this repository public.  

---

## 🙏 Thank You  

Thank you for taking the time to review my RMF/eMASS Mapping project. This project reflects my ability to design, document, and secure enterprise environments while aligning with federal compliance frameworks. I am excited to continue building projects like this to strengthen and prove my skills as I advance in my cybersecurity career.  

