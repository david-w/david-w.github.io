---
title: "Windows Update Trouble Using WSUS Server"
layout: "single"
classes: wide
---

I am not a Windows expert.  So personally, I would not follow my instructions at all.  I make no claim that this is the correct way to solve this problem.  In fact, it's probably not.  This is simply a record of my steps to hopefully solve the problem of two Windows workstations not receiving updates.

At work we use a Windows Server Update Services (WSUS) server to control how our endpoints receive Windows updates.  An Active Directory group policy specifies the settings for updates to run unattended overnight on Tier 2 workstations once they have been approved and tested on Tier 1.  It's a pretty standard setup.

I have two workstations that have not received updates.  They currently run Windows 10 Enterprise 2016 LTSB.  
NOTE: These were configured for operating manufacturing equipment and came pre-configured running a 32-bit OS on a 64-bit processor.  I think the software that runs the equipment are older 32-bit applications.  

SYMPTOMS
* When checking for Windows updates, the following error was showing: (0x80070057)
* Workstations did not show up as connected on the WSUS server console.
* Event Viewer shows the following relevant messages:
    - wuaserv syatem error "87" has occurred.
    - DCOM got error 87 attempting to start the service wuauserv with arguments "Unavailable" in order to run the server:
    - Event ID 7000: Service Start Failure.
    - Event 7000 Service Control Manager error.

First I completed all the regular steps recommended by Microsoft to reset the Windows update components.
Open a command prompt as an administrator and enter the following commands:
1. Stop these services:
````sh
net stop bits
net stop wuauserv
net stop cryptsvc
````

2. Delete the qmgr*.dat files:
        Del "%ALLUSERSPROFILE%\Application Data\Microsoft\Network\Downloader\qmgr*.dat"

3. Rename the SoftwareDistribution and catroot2 folders:
````sh
Ren %systemroot%\SoftwareDistribution SoftwareDistribution.bak
Ren %systemroot%\System32\catroot2 catroot2.bak
````
4.



Also ran the following commands per Microsoft docs:
````sh
sfc/ scannow
Dism /Online /Cleanup-Image /CheckHealth
Dism /Online /Cleanup-Image /ScanHealth
Dism /Online /Cleanup-Image /RestoreHealth
````


I was able to get past that (or at least change the error) by modifying the wuauserv service with Regedit.
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\wuauserv
I compared the registry values of the service with a workstation that was receiving updates, and noticed that the ImagePath was missing.
I added the following to ImagePath: %systemroot%\System32\svchost.exe -k netsvcs -p

After that, the workstation gave a different error




* Read Windows Update Logs using PowerShell:
Open an elevated PowerShell
````sh
Get-WindowsUpdateLog
````
