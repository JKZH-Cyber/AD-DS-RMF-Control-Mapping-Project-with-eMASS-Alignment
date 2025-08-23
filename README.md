# JKZH AD DS RMF/eMASS Mapping Project  

## Applied Skills and Knowledge  

Through this project, I demonstrated hands-on expertise in building and hardening an Active Directory Domain Controller in Microsoft Azure while aligning its configuration with the NIST Risk Management Framework (RMF). I deployed a Windows Server 2025 Datacenter VM, promoted it to a Domain Controller, and implemented a Baseline Security Group Policy Object (GPO) mapped to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  

While eMASS itself was not used in this lab, the project simulates how system owners prepare technical evidence for upload into eMASS. Each GPO hardening step was intentionally mapped to RMF controls, demonstrating compliance traceability in the same manner required for DoD systems.  

I enforced account lockout thresholds, password complexity, Kerberos policies, audit logging, Windows Defender Antivirus, firewall profiles, and removable media restrictions. Each configuration was validated against RMF security controls to ensure the domain environment met access control, auditing, and system integrity requirements.  

This project reflects my ability to stand up AD DS infrastructure in the cloud while also thinking like a compliance assessor by mapping technical implementations to federal security controls.  

---

## Objective  

The goal of this project was to deploy a domain controller in Azure, configure Active Directory services, and apply security baseline GPOs that demonstrate compliance with RMF and eMASS control mappings. By doing so, I simulated how enterprises enforce security policies, reduce attack surfaces, and document compliance within federal environments.  

---

## Skills Demonstrated  

- **Active Directory Deployment:** Promoted a Windows Server to a Domain Controller with DNS integration.  
- **Group Policy Enforcement:** Designed and implemented baseline GPOs for authentication, account lockout, and system auditing.  
- **RMF/eMASS Mapping:** Mapped security configurations to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  
- **Windows Security Hardening:** Applied password policies, Kerberos ticket restrictions, and logon banners.  
- **Endpoint Protection:** Configured Microsoft Defender Antivirus and Windows Defender Firewall.  
- **Audit & Log Management:** Ensured security logs were retained, auditable, and protected.  
- **Media Control:** Restricted removable storage devices to mitigate data exfiltration risks.  

---

## Tools Used  

- **Microsoft Azure:** Hosted the virtualized lab environment.  
- **Windows Server 2025 Datacenter:** Domain Controller with AD DS and DNS roles.  
- **Active Directory Users and Computers (ADUC):** Managed users, groups, and OUs.  
- **Group Policy Management Console (GPMC):** Created and linked security baselines.  
- **Windows Defender Antivirus:** Provided malware protection.  
- **Event Viewer:** Verified audit policies and log collection.  

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

1. Create resource group, VNet, subnet, and NSG in East US.  
2. Provision Windows Server 2025 VM `jkzh-dc01`, attach to `jkzh-emass-vnet` on `default` subnet.  
3. Apply NSG rule to allow RDP from my public IP, deny other inbound traffic by default.  
4. Promote `jkzh-dc01` to a Domain Controller with DNS for `jkzh.lab`.  
5. Create a Baseline Security GPO and enforce security controls.  
6. Map each configuration to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  

---

## 🚀 Step 1: Sign into Azure Portal  

1. Navigated to [https://portal.azure.com](https://portal.azure.com)  
2. Logged in with my Azure account credentials.  
3. Verified subscription access before proceeding.  
See below: Azure welcome page
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

## 📂 Technical Evidence Produced  

This project generated the same types of technical evidence that system owners would upload into eMASS as part of RMF compliance documentation:  

- **Azure Infrastructure Screenshots** - Resource Group, VNet, NSG, and VM build details.  
- **Domain Controller Configuration** - AD DS role installation, DC promotion, and ADUC domain verification.  
- **GPO Enforcement Evidence** - Screenshots of account lockout, password complexity, Kerberos, audit policies, event log settings, Defender AV, firewall profiles, and removable media restrictions.  
- **Compliance Mapping** - Each configuration mapped to NIST SP 800-53 RMF control families (commonly tracked in eMASS).  

While this lab was not connected to eMASS, the collected screenshots and configuration outputs represent the type of artifacts uploaded into eMASS as compliance evidence for DoD systems.  

---

## ✅ Outcome  

By completing this project, I demonstrated both technical expertise in building and securing AD DS in Azure and compliance-focused thinking by mapping system configurations to NIST RMF control families. This dual approach mirrors the expectations of federal compliance environments, where system hardening and control documentation must work hand-in-hand.  

---

## Disclaimer  

This lab was built strictly for educational purposes and cybersecurity skill development. The environment was deployed in a controlled Azure setting with no sensitive or production data. All resources were decommissioned before making this repository public.  

---

## Thank You  

Thank you for taking the time to review my RMF/eMASS Mapping project. This project reflects my ability to design, document, and secure enterprise environments while aligning with federal compliance frameworks. I am excited to continue building projects like this to strengthen and prove my skills as I advance in my cybersecurity career.  
