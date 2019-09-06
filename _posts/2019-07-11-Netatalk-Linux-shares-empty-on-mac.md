---
title: "Netatalk-Linux-shares-empty-on-MacOS"
layout: "single"
classes: wide
---
I'm running an older version of netatalk on a Linux server running proftpd.  Netatalk allows AFP (Appleshare) connections to Linux boxes.


Recently a user logged on to a share and reported that all the files on the share were missing.  When I logged on to the server via ssh, the files were present.  Since none of the errors in the log pointed me to the problem, it took a bit of digging. Eventually I was able to trace the problem to a corrupt .AppleDB file.

**PROBLEM:**  
• User logs in to the share as usual.  
• Share is completely empty. No folders or files showing.  
• Attempting to create a file or folder throws a permissions error.  
• Server shows errors in logs for afpd (daemon for afp connections).  

**Example error log entries:**
````sh
afpd[8522]: [cnid_open.c:357]: E:Default: cnid_open: Failed to open did/name database: Cannot allocate memory  
afpd[8522]: [cnid_open.c:552]: E:Default: cnid_open: Failed to setup CNID DB environment  
afpd[8522]: [enumerate.c:83]: E:AFPDaemon: adddir: Incorrect parameters passed to cnid_add
````
**SOLUTION:**  
Somehow the .AppleDB file becomes corrupt.  
SSH in to the server, navigate to the folder that is showing as the empty share when connecting with AFP, and delete the .AppleDB folder.
