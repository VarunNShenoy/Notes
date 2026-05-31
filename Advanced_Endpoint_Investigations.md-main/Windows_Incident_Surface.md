**Powershell Profile:**

A powershell profile is a script that executes every time powershell is executed. Whe can say that a powershell profile is to powershell, what a startup program is to windows.

Therefore before executing powershell, we should look for traces of compromise in the powershell profile. 

So not avoid getting into confussion when every powershell is executed a profile is created, if we also us powershell for analysis then we might get confused while analyzing. Hence we need to use our own powershell - CMD-DFIR.exe

To collect the env variables of the system - set > env_variables.txt

<img width="942" height="593" alt="image" src="https://github.com/user-attachments/assets/f9374067-a387-49f2-acd7-714703e148eb" />

Fields that might be frequently hyjacked are

1. Comspec - Windows prompt command executable (cmd.exe)
2. Path - Environment path Values
3. PSModulePath: Powershell Directories
4. Public : Public Folder
5. TEMP and TMP: Temp Locations

Although the path of the directories doesn't look abnormal, we should perhaps look inside the PowerShell directories just to make sure that the actual profile files are not modified. There are six different profiles in PS paths, but only four are used by the PowerShell console and applications (Windows PowerShell ISE uses two). Below are the default locations of profile files.

| Profile Scope | Profile File Path |
|---------------|-------------------|
| Current User + Current Host  | $HOME\Documents\WindowsPowerShell\profile.ps1 |
| All Users + Current Host | $PSHOME\Microsoft.PowerShell_profile.ps1 |
| Current User + All Hosts | $HOME\Documents\profile.ps1 |
| All Users + All Hosts | $PSHOME\profile.ps1 |

The above mentioned paths are relative paths, but since we are using our own command shell, we will need to translate them to absoulte paths. We can take help from the environment variables we have previously listed to do that. The USERPROFILE variable shows the absoulte path of the #Home Directory. The PSModulePath varaible shows the absolute path to the $PSHome and other relevant directories. In case there are multiple directories present for the PSModulePath, we can use the location of the PowerShell executable as our PSModulePath. Profile file needs to reside in the same directory as this executable. Therefore, we can use the command below to reveal the absolute path of the $PSHOME.

<img width="953" height="53" alt="image" src="https://github.com/user-attachments/assets/436fb62a-2d94-48d4-8b91-46998bdc7064" />

<img width="882" height="86" alt="image" src="https://github.com/user-attachments/assets/5c80a3a7-7c41-4f95-a930-f63cb0ffe648" />

<img width="885" height="275" alt="image" src="https://github.com/user-attachments/assets/0f5d918f-9b16-49a3-8eed-21a63566cbc2" />

In Current Scenario that is the VM, we see only one user profile that exits :
<img width="951" height="51" alt="image" src="https://github.com/user-attachments/assets/a169c5c5-2bee-4f71-a877-3edc69c1fd81" />

We have identified that a PowerShell profile exists and is valid for all users and hosts. Let's read this file to determine if an event-triggered execution (ATT&CK ID: T1546.013) is present in the system. We can do that by executing the commands in the terminal below.

Best Pratice: Before starting analysis, we need to restore clean powershell Profiles, in the place of the malicious powershell profile in the system. Remember to take backup of the malicious powershell profile so that we can analyze later on.

Command Executed:

ren PS-DFIR-Profile.ps1 (Clean Powershell profile) profile.ps1(Default name presnet in the system)
ren C:\Windows\System32\WindowsPowerShell\v1.0\profile.ps1 profile.bak (Taking backup)
copy profile.ps1 C:\Windows\System32\WindowsPowerShell\v1.0\ (copying the clean to default powershell profile path)

After Analyzing the malicious ps1 we observed 

<img width="940" height="117" alt="image" src="https://github.com/user-attachments/assets/d034e658-f2aa-46e3-89aa-440cc7fe3020" />

Clean powershell profile 

<img width="569" height="119" alt="image" src="https://github.com/user-attachments/assets/c3f35657-447a-4684-b13b-850d370ebd05" />

System Profile:

System Name and Network Information : Get-CimInstance win32_networkadapterconfiguration -Filter IPEnabled=True | ft DNSHostname, IPAddress, MAC Address

OS Version and Installation Details: Get-CimInstance -ClassName Win32_OperatingSystem | ft CSName CSName, Version, BuildNumber, InstallDate, LastBootUpTime, OSArchitecture (ft stands for Format-Table)

Date and Timezone Info : Get-Date; Get-TimeZone

Reviewing Policies: Get-GPResultantSetOfPolicy -ReportType HTML -Path (Join-Path -Path (Get-Location).Path -ChildPath "RSOPReport.html")

Network Ports and Connections:

To get the Activte Ports in the system.

Get-NetTCPConnection | select Local*, Remote*, State, OwningProcess,` @{n="ProcName";e={(Get-Process -Id $_.OwningProcess).ProcessName}},` @{n="ProcPath";e={(Get-Process -Id $_.OwningProcess).Path}} | sort State | ft -Auto | tee tcp-conn.txt

` @{} - This syntax is used inside Select-Object to add a new column that doesn’t exist on the original object.
n="" - This is the column name that will appear in the output
e{} - This is the PowerShell code that runs for each object in the pipeline
$_ -Represents the current object from Get-NetTCPConnection. That object has a property called OwningProcess (the PID)

This is used to extract a pid from owning process and comparing it the value of in Get-Process and print the value of process name and process path in the new columns called ProcName and ProcPath.

Reviewing Policies: Get-GPResultantSetOfPolicy -ReportType HTML -Path (Join-Path -Path (Get-Location).Path -ChildPath "RSOPReport.html")


**Users and Sessions**

Adversaries can create a new accounts or manipulate the existing ones to maintain or elevate their access to victim system. Therefore analyzing the user accounts and the current sessions of these accounts are a crucial part of any investigation.

Command 

Get-LocalUser | tee users.txt
Get-CimInstance -Class Win32_UserAccount -Filter "LocalAccount=True" | Format-Table  Name, PasswordRequired, PasswordExpires, PasswordChangeable | Tee-Object "user-details.txt"

To check the Last Log on details

Get-CimInstance -Class Win32_NetworkLogonProfile | Format-Table Name, LastLogon 

**Session:**

In a live system, you can also look for the current sessions in the system. These sessions identify who's on the system. If the attacker is active and currently connected to the system, you can identify their session. Let's list the active sessions using the Sysinternals  PsLoggedon utility. This shows if the attacker is within the network.


Network Shares: 

Get-CimInstance -Class Win32_Share | tee net-shares.txt

Firewall (Network): Get-NetFirewallProfile | ft Name, Enabled, DefaultInboundAction, DefaultOutboundAction | tee fw-profiles.txt




















