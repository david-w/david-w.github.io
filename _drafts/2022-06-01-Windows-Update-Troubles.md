---
title: "Windows Update Trouble Using WSUS Server"
layout: "single"
---



I am not a Windows expert.  So personally, I would not follow my instructions at all.  Seriously.

I make no claim that this is the correct way to solve this problem.  In fact, it's probably not.  This is simply a record of my steps to hopefully solve the problem of two Windows workstations not receiving updates.

At work we use a Windows Server Update Services (WSUS) server to control how our endpoints receive Windows updates.  An Active Directory group policy specifies the settings for updates to run unattended overnight on Tier 2 workstations once they have been approved and tested on Tier 1.  It's a pretty standard setup.

I have two workstations that have not received updates.  They currently run Windows 10 Enterprise 2016 LTSB.  
NOTE: These are used for operating manufacturing equipment and came pre-configured running a 32-bit OS on a 64-bit processor.  I think the software that runs the equipment are older 32-bit applications.  

### SYMPTOMS
* When checking for Windows updates, the following error was showing: (0x80070057)
* Workstations did not show up as connected on the WSUS server console.
* Event Viewer shows the following relevant messages:
  - wuaserv syatem error "87" has occurred.
  - DCOM got error 87 attempting to start the service wuauserv with arguments "Unavailable" in order to run the server:
  - Event ID 7000: Service Start Failure.
  - Event 7000 Service Control Manager error.
* Oddly, the time was wrong (off by 6 mins.) even though it was configured to syncronize with the domain controller via group policy.  
* Even more oddly, the Windows Update Log had lots of entries with unknown (No Format Information found) and a date of 12/31/1600.


First I completed all the regular steps recommended by Microsoft to reset the Windows update components.
Open a command prompt as an administrator and enter the following commands:
**1.** Stop these services:
````sh
net stop bits
net stop wuauserv
net stop cryptsvc
````

**2.** Delete the qmgr*.dat files:
````console
Del "%ALLUSERSPROFILE%\Application Data\Microsoft\Network\Downloader\qmgr*.dat"
````

**3.** Rename the SoftwareDistribution and catroot2 folders:
````console
Ren %systemroot%\SoftwareDistribution SoftwareDistribution.bak
Ren %systemroot%\System32\catroot2 catroot2.bak
````

      **NOTE:** Step 4 may not be needed and should be skipped the first time attempting to reset Windows Update. Only use this if the first attempt fails.
      {: .notice--danger}  

**4.** Reset the security descriptor for the BITS & Windows Update services:
````plaintext
sc.exe sdset bits D:(A;CI;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)
sc.exe sdset wuauserv D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
````

**5.** Change to the system32 directory:
````console
cd /d %windir%\system32
````

**6.** Reregister the BITS and Windows Update files:
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

**7.** Reset Winsock:
````console
netsh winsock reset
````

**8.** Finally, restart the services:
````sh
net start bits
net start wuauserv
net start cryptsvc
````

 I also ran the following commands per Microsoft docs:  
 ````sh
 sfc/ scannow
 Dism /Online /Cleanup-Image /CheckHealth
 Dism /Online /Cleanup-Image /ScanHealth
 Dism /Online /Cleanup-Image /RestoreHealth
 ````

**NOTE: None of the above steps had any effect on the workstations.**
{: .notice--danger}  


I was able to get past the original error (or at least change the error) by modifying the wuauserv service with Regedit.
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\wuauserv
I compared the registry values of the service with a workstation that was receiving updates, and noticed that the ImagePath was missing.
I added the following to ImagePath: %systemroot%\System32\svchost.exe -k netsvcs -p  


After modifying the wuauserv service, at least the workstations showed up in the list of end points on the WSUS server.
{: .notice--success}

After that, the workstation gave a different error(s)  
(0x8024500c) - Agent exit code.   
Related - EP: error: 0x8024500C: -failed to get SLS data.   
Related - Service exit code = 0x240001   
Related - Failed to syncronize, error = 0x8007000E   
Related - Failed to syncronize, error = 0x8024000B   
Related - Service CheckAccessByPolicy rejected call, hr=0x80244011   
Related - PopulateDataStore failed 0x8024000e

After getting the workstation connected to the WSUS server, things were still broken with the following errors showing in the Windows Update Log.  


|   Error          | Reported by       |   Information                                                     |
| ---------------- | ------------------| ----------------------------------------------------------------- |
| (0x8024500c)     | Misc              | EP: error: 0x8024500C : failed to get SLS data                    |
| (0x8024500c)     | ProtocolTalker    | Initialization failed for ProtocolTalker Context 0x8024500C       |
| (0x240001)       | Shared            |       |
| [Jane Bloggs](#) |                       | With hair like that?! Enough said.                                |

* Read Windows Update Logs using PowerShell:
Open an elevated PowerShell
````powershell
Get-WindowsUpdateLog
````
* Viewing Windows Update settings using PowerShell:
Open an elevated PowerShell
````powershell
Get-ItemProperty -Path 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate'
Get-ItemProperty -Path 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate\AU'
$(New-Object -ComObject "Microsoft.Update.ServiceManager").Services | Select-Object Name, IsDefaultAUService
````
* Some good Microsoft links relating to WSUS, Windows Updates, etc.
Windows Update error codes
https://docs.microsoft.com/en-us/windows/deployment/update/windows-update-error-reference
Deploying updates with WSUS and configuring automatic updates using Group Policy
https://docs.microsoft.com/en-us/windows/deployment/update/waas-manage-updates-wsus
Troubleshooting WSUS client issues
https://docs.microsoft.com/en-us/troubleshoot/mem/configmgr/troubleshoot-issues-with-wsus-client-agents
WSUS Maintenance Guide
https://docs.microsoft.com/en-us/troubleshoot/mem/configmgr/wsus-maintenance-guide
Re-register Windows clients in WSUS server
https://docs.microsoft.com/en-us/troubleshoot/windows-server/deployment/re-register-windows-clientserver-in-wsus
Microsoft info on Dual-Scan
https://docs.microsoft.com/en-us/archive/blogs/wsus/demystifying-dual-scan
https://docs.microsoft.com/en-us/archive/blogs/wsus/improving-dual-scan-on-1607

* Non-Microsoft links
Good WSUS setup guide
https://www.ajtek.ca/guides/how-to-setup-manage-and-maintain-wsus-part-1-choosing-your-server-os/
Great guide for fixing clients not reporting to wsus
https://www.ajtek.ca/wsus/client-machines-not-reporting-to-wsus-properly/
Great info on "Dual-Scan", and Windows Update error 0x8024500c
https://blog.tofte-it.dk/wsus-windows-10-clients-error-0x8024500c/
