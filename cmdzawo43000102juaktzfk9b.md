---
title: "Going Splunking - Round Two: Splunk VS Caldera"
seoTitle: "Splunk vs Caldera: Red Team vs Blue Team"
seoDescription: "Explore the setup and use of Splunk and Caldera in a home lab environment for adversarial engagements and threat detection training"
datePublished: Wed Aug 06 2025 01:40:17 GMT+0000 (Coordinated Universal Time)
cuid: cmdzawo43000102juaktzfk9b
slug: going-splunking-round-two-splunk-vs-caldera
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1751478189968/30266830-3781-46ce-8500-75b30b95654d.jpeg
tags: splunk, cybersecurity-1, lab, walkthrough, red-team, blueteam, caldera, purpleteam

---

Now that I have both Splunk and Caldera set up and running on my home lab, its time to get adversarial! Im going to be running an autonomous red-team engagement from Caldera and seeing what I can find in Splunk. Training myself how to detect threats, as noted by the Caldera documentation:

> This is the original Caldera use-case. You can use the framework to build a specific threat (adversary) profile and launch it in a network to see where you may be susceptible. This is good for testing defenses and training blue teams on how to detect threats.

Ill be following along the documentation [here](https://caldera.readthedocs.io/en/latest/Getting-started.html#autonomous-red-team-engagements) for the red team side of things.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753647617141/587adecd-9c92-4627-adf0-56603c57eddf.jpeg align="center")

# The Red Team Attacks

* The first step is to deploy an agent. Ill be using the Sandcat agent.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753647977943/4fd61d70-71fe-42f4-b75c-5f8fe4560118.png align="center")
    
* This agent will be deployed on a Windows virtual machine. Caldera provides the PowerShell script to run:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753648266789/ec0e477b-46b2-4454-9795-4f5058bac9a2.png align="center")
    
* Next Ill be running the provided script in Windows PowerShell running as administrator:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753648631879/07527f35-2d64-49c8-92d8-856e842f59fb.png align="center")
    
    This triggered Microsoft Defender so I had to change some settings:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753649283314/67de32e8-5065-4d39-904d-aa007dfb97b9.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753649369943/ed7fc955-69e6-43c2-91ca-0efbcfddbbd9.png align="center")
    
* After a few attempts, I was now able to get the agent running:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753649490016/75c7d694-c4c7-4475-8d1b-71c077f49a95.png align="center")
    
    and could also confirm from Caldera:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753649558371/15b79910-92c0-423a-97c2-eb3a189c17a2.png align="center")
    
* Now its time to choose the adversary profile, Im going with the Discovery adversary. We can see a list of its abilities from the Adversaries page in Caldera:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753649925821/5ac247cd-fa4e-4274-942a-089e493fa16a.png align="center")
    
    The Discovery adversary maps to the [Discovery](https://attack.mitre.org/tactics/TA0007/) tactic in the MITRE ATT&CK matrix.
    
    Next we will run an operation. Navigating to the Operations page in Caldera and hitting the “New Operation” button presents us with some fields we need to add info to:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753650497981/ff75eb19-3f29-46a1-bfef-293698128116.png align="center")
    
    I named the operation and selected the Discovery adversary, also setting the group to red:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753650724163/f36b7dfb-e897-4b85-8ef5-60188f9a8b08.png align="center")
    
    Once I hit the start button, I could see the operation was created:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753650890490/fa1ee6f6-da11-42d1-926d-a573a9446ace.png align="center")
    
    scroll down, and I could see that the abilities of the adversary were currently in progress:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753651035600/ed655809-13c7-4254-86a1-3962b4efc83e.png align="center")
    
    Once all the abilities ran, the adversarial operation was complete. Now time to get the blue team perspective.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753652241332/b72918eb-d11c-45a6-b5a9-33d6002cf734.jpeg align="center")

# The Blue Team Defends

* Starting from what is already known, 7 adversarial abilities successfully ran, so there should be at least 7 logs in splunk to find. Setting the time range for today I began my search:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754415239335/21f5b4a3-f867-4d59-b1a8-410ff71591c9.png align="center")
    
    ```bash
    index="win_10_logs"  User="WINDOZE10\\Screw Muggz" IntegrityLevel=High
    ```
    
    Using the index set for the logs from my Windows 10 VM, the user name of the user account and IntegrityLevel set to high, this whittled the logs down to a manageable 16 events to start off with.
    
    The 7 adversarial abilities that successfully ran are shown below along with one that failed:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754415728766/c6b527a4-1d45-4345-9a73-1fd0fb35e8d7.png align="center")
    

## Log Analysis

This sections covers how I found the logs in Splunk to the corresponding adversarial abilities ran in Caldera in the order shown in the above screenshot.

1. **Identify Active User**
    
    [MITRE ATT&CK Technique: T1033](https://attack.mitre.org/techniques/T1033/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754416464965/45e79a1d-9b0f-4dcd-a6be-c49dd2928aa8.png align="center")
    
    I scrolled through the logs looking at the CommandLine output, and this line indicates a Powershell command asking for the user name:
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C $env:username
    ```
    
    The first adversarial ability has been found.
    
2. **Identify Local Users**
    
    [MITRE ATT&CK Technique: T1087.001](https://attack.mitre.org/techniques/T1087/001/)
    
    This was the log immediately above the last one in Splunk.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754417632331/bb3fdc43-b99a-49e4-ba0a-25ab4e534fed.png align="center")
    
    The command that drew my attention:
    
    ```bash
     CommandLine: powershell.exe -ExecutionPolicy Bypass -C "Get-WmiObject -Class Win32_UserAccount"
    ```
    
    I pasted this line into ChatGpt to ask for an summary and I was given this information:
    
    > That PowerShell one-liner:
    > 
    > * **Runs PowerShell** with execution restrictions bypassed.
    >     
    > * **Queries WMI** for the `Win32_UserAccount` class.
    >     
    > * **Returns** one object per user account (local or domain) with properties like name, domain, SID, and status.
    >     
    > 
    > In short: it lists all user accounts on the machine (and any domain accounts visible via WMI).
    
    So the second adversarial ability has been found.
    
3. **Find User Processes**
    
    [MITRE ATT&CK Technique: T1057](https://attack.mitre.org/techniques/T1057/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754431710704/0fcdd316-f8eb-422f-b97d-fc24af72164c.png align="center")
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "$owners = @{};gwmi win32_process |%% {$owners[$_.handle] = $_.getowner().user};$ps = get-process | select processname,Id,@{l=\"Owner\";e={$owners[$_.id.tostring()]}};foreach($p in $ps) {    if($p.Owner -eq \"Screw` Muggz\") {        $p;    }}"
    ```
    
    The search I ran, and Caldera running the adversary abilities back to back has all the interesting logs stacked on top of each other. I can see that the command in this log was looking for processes. To further understand the Powershell command, I again fed it to ChatGpt to summerize:
    
    > This PowerShell command:
    > 
    > * **Finds all running processes**.
    >     
    > * **Matches each process to its owner** (user account).
    >     
    > * **Filters and displays only the processes** owned by the user `Screw Muggz`.
    >     
    > 
    > Key Purpose:
    > 
    > **Identify all processes currently running under the user account "Screw Muggz".**
    
4. **View Admin Shares**
    
    [MITRE ATT&CK Technique: T1135](https://attack.mitre.org/techniques/T1135/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754433761065/6ffdde19-38dc-4aaf-bfa4-0163c9acdecc.png align="center")
    
    The line of immediate interest:
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "Get-SmbShare | ConvertTo-Json"
    ```
    
    > This PowerShell command:
    > 
    > * **Retrieves all SMB (Windows file) shares** on the local system using `Get-SmbShare`.
    >     
    > * **Converts the results to JSON** format using `ConvertTo-Json`.
    >     
    > 
    > Key Purpose:
    > 
    > **Export a list of all shared folders on the system in JSON format**, useful for logging, auditing, or integration with other tools.
    
5. **Discover Domain Controller**
    
    [MITRE ATT&CK Technique: T1018](https://attack.mitre.org/techniques/T1018/)
    
    This is the log for the adversary ability that failed:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754435125800/61b0d310-db1a-4e43-a906-b98a435dd5fe.png align="center")
    
    The powershell command:
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "nltest /dsgetdc:$env:USERDOMAIN"
    ```
    
    > **Summary of the Command**
    > 
    > This PowerShell command:
    > 
    > * **Runs** `nltest /dsgetdc:<domain>` to find a domain controller for the current user’s domain.
    >     
    > * Uses `$env:USERDOMAIN` to automatically insert the domain name from the environment.
    >     
    > 
    > Key Purpose:
    > 
    > **Identify the domain controller (DC) for the domain the current user is logged into**, useful for troubleshooting or verifying domain connectivity.
    
    I found another log that corresponds to the same adversarial ability:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754435591176/c76b21f4-7c2f-43aa-8454-00a905994ebf.png align="center")
    
    ```bash
    CommandLine: "C:\Windows\system32\nltest.exe" /dsgetdc:WINDOZE10
    ```
    
    > **Summary of the Command**
    > 
    > This command:
    > 
    > * Uses `nltest.exe` to **query for a domain controller (DC)** for the domain named `WINDOZE10`.
    >     
    > 
    > Key Purpose:
    > 
    > **Checks if a domain controller for the domain** `WINDOZE10` is reachable, and retrieves its information if found — useful for Active Directory troubleshooting.
    
6. **Discover Antivirus Programs**
    
    [MITRE ATT&CK Technique: T1518.001](https://attack.mitre.org/techniques/T1518/001/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754437098725/d8096e4c-35be-4ffd-9471-b324f8c3beb2.png align="center")
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "wmic /NAMESPACE:\\root\SecurityCenter2 PATH AntiVirusProduct GET /value"
    ```
    
    > **Summary of the Command**
    > 
    > This PowerShell command:
    > 
    > * Runs a **WMIC query** against the WMI namespace `root\SecurityCenter2`.
    >     
    > * Retrieves **detailed information about installed antivirus products** from the `AntiVirusProduct` class.
    >     
    > * Outputs the results in a **key=value** format.
    >     
    > 
    > Key Purpose:
    > 
    > **Lists antivirus products registered with Windows Security Center**, including their name, status, and file paths — useful for inventory or security checks.
    
    This adversarial ability also had multiple corresponding logs, here is the other:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754438889938/14b52e59-fc22-4401-9134-4a050211aab9.png align="center")
    
    ```bash
    CommandLine: "C:\Windows\System32\Wbem\WMIC.exe" /NAMESPACE:\\root\SecurityCenter2 PATH AntiVirusProduct GET /value
    ```
    
    > **Summary of the Command**
    > 
    > This command:
    > 
    > * Uses **WMIC (Windows Management Instrumentation Command-line)** to query the `AntiVirusProduct` class in the `root\SecurityCenter2` namespace.
    >     
    > * Outputs details in a **key=value** format for all registered antivirus products.
    >     
    > 
    > Key Purpose:
    > 
    > **Displays detailed information about antivirus software installed on the system**, as recognized by Windows Security Center — useful for audits and security validation.
    
7. **Permission Groups Discovery**
    
    [MITRE ATT&CK Technique: T1069.001](https://attack.mitre.org/techniques/T1069/001/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754439841041/d55bf279-bf4d-4a63-a794-2903e5c312b3.png align="center")
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "gpresult /R"
    ```
    
    > **Summary of the Command**
    > 
    > This PowerShell command:
    > 
    > * Runs the `gpresult /R` command to **generate a summary of applied Group Policy settings** for the current user and computer.
    >     
    > 
    > Key Purpose:
    > 
    > **Shows which Group Policy Objects (GPOs) have been applied** to the system and user — useful for troubleshooting policy issues in Active Directory environments.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754442387327/e0ab77e5-3d46-4707-8a03-3ca7ce4c599b.png align="center")
    
    ```bash
    "C:\Windows\system32\gpresult.exe" /R
    ```
    
    > **Summary of the Command**
    > 
    > This command:
    > 
    > * Executes the `gpresult.exe` utility (without any switches), which displays a **brief summary** of the Resultant Set of Policy for the current user and computer.
    >     
    > 
    > Key Purpose:
    > 
    > Quickly view which Group Policy Objects have been applied without generating a detailed report.
    
8. **Identify Firewalls**
    
    [MITRE ATT&CK Technique: T1518.001](https://attack.mitre.org/techniques/T1518/001/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754442874849/38cc2196-2039-4213-9cb7-3edf93d9d504.png align="center")
    
    ```bash
    CommandLine: powershell.exe -ExecutionPolicy Bypass -C "$NameSpace = Get-WmiObject -Namespace \"root\" -Class \"__Namespace\" | Select Name | Out-String -Stream | Select-String \"SecurityCenter\";$SecurityCenter = $NameSpace | Select-Object -First 1;Get-WmiObject -Namespace \"root\$SecurityCenter\" -Class AntiVirusProduct | Select DisplayName, InstanceGuid, PathToSignedProductExe, PathToSignedReportingExe, ProductState, Timestamp | Format-List;"
    ```
    
    > **Summary of the Command**
    > 
    > This PowerShell one-liner:
    > 
    > 1. **Discovers the correct WMI namespace** (either `SecurityCenter` or `SecurityCenter2`).
    >     
    > 2. **Queries the AntiVirusProduct class** within that namespace to retrieve each registered antivirus product’s key properties (name, GUID, executable paths, state, timestamp).
    >     
    > 3. **Formats the output** as a readable list.
    >     
    > 
    > Key Purpose:
    > 
    > **List detailed information about all antivirus products** registered with Windows Security Center in a clean, human-readable format.
    

# Conclusion

I ran through this lab several times prior to documenting it, this is how I was able to refine my search. During documenting the blue team side of things I was able to find more logs than I did on previous tries. I also decided to map the adversarial abilities back to the MITRE ATT&CK matrtix.

### What did I learn?

* Attacks could have multiple logs, so finding one might not be the end of the story.
    
* Commands from the attacks were run in Powershell, as well as on the Command Line using native Windows utilities, this is what caused multiple logs.
    
* Running attacks from Caldera is a fun and interesting exercise to help sharpen and expand my skill set.
    

### Whats Next?

* I plan to continue to run more exercises from Caldera to get better at using Splunk. There is a lot more adversarial abilities to learn from.
    
* I will generate alerts from the commands documented in this article and trigger them by running through this lab again.