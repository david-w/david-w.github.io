---
title: "Windows Update Trouble Using WSUS Server"
layout: "single"
---

I am not a Windows expert.  So personally, I would not follow my instructions at all.  Seriously.

I make no claim that this is the correct way to solve this problem.  In fact, it's probably not.  This is simply a record of my steps to hopefully solve the problem of two Windows workstations not receiving updates.

At work we use a Windows Server Update Services (WSUS) server to control how our endpoints receive Windows updates.  An Active Directory group policy specifies the settings for updates to run unattended overnight on Tier 2 workstations once they have been approved and tested on Tier 1.  It's a pretty standard setup.

I have two workstations that have not received updates.  They currently run Windows 10 Enterprise 2016 LTSB.  
NOTE: These were configured for operating manufacturing equipment and came pre-configured running a 32-bit OS on a 64-bit processor.  I think the software that runs the equipment are older 32-bit applications.  

### SYMPTOMS
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
````console
Del "%ALLUSERSPROFILE%\Application Data\Microsoft\Network\Downloader\qmgr*.dat"
````

3. Rename the SoftwareDistribution and catroot2 folders:
````console
Ren %systemroot%\SoftwareDistribution SoftwareDistribution.bak
Ren %systemroot%\System32\catroot2 catroot2.bak
````

**NOTE:** Step 4 may not be needed and should be skipped the first time attempting to reset Windows Update. Only use this if the first attempt fails.
{: .notice--danger}  

4. Reset the security descriptor for the BITS & Windows Update services:
````plaintext
sc.exe sdset bits D:(A;CI;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)
sc.exe sdset wuauserv D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
````

5. Change to the system32 directory:
````console
cd /d %windir%\system32
````

6. Reregister the BITS and Windows Update files:
NOTE: Not all of the following may be needed.  In my case, some gave errors that they were not installed.
````console
regsvr32.exe atl.dll
regsvr32.exe urlmon.dll
regsvr32.exe mshtml.dll
regsvr32.exe shdocvw.dll
regsvr32.exe browseui.dll
regsvr32.exe jscript.dll
regsvr32.exe vbscript.dll
regsvr32.exe scrrun.dll
regsvr32.exe msxml.dll
regsvr32.exe msxml3.dll
regsvr32.exe msxml6.dll
regsvr32.exe actxprxy.dll
regsvr32.exe softpub.dll
regsvr32.exe wintrust.dll
regsvr32.exe dssenh.dll
regsvr32.exe rsaenh.dll
regsvr32.exe gpkcsp.dll
regsvr32.exe sccbase.dll
regsvr32.exe slbcsp.dll
regsvr32.exe cryptdlg.dll
regsvr32.exe oleaut32.dll
regsvr32.exe ole32.dll
regsvr32.exe shell32.dll
regsvr32.exe initpki.dll
regsvr32.exe wuapi.dll
regsvr32.exe wuaueng.dll
regsvr32.exe wuaueng1.dll
regsvr32.exe wucltui.dll
regsvr32.exe wups.dll
regsvr32.exe wups2.dll
regsvr32.exe wuweb.dll
regsvr32.exe qmgr.dll
regsvr32.exe qmgrprxy.dll
regsvr32.exe wucltux.dll
regsvr32.exe muweb.dll
regsvr32.exe wuwebv.dll
````

7. Reset Winsock:
````console
netsh winsock reset
````

8. Finally, restart the services:
````sh
net start bits
net start wuauserv
net start cryptsvc
````

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

After that, the workstation gave a different error(s)




* Read Windows Update Logs using PowerShell:
Open an elevated PowerShell
````powershell
Get-WindowsUpdateLog
````
