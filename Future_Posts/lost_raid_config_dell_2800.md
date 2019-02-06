## Lost RAID configuration on Dell 2800 with PERC 4e/Di RAID controller

I have been having issues with a Dell 2800 file server.  Yes, it's old.  Until recently however, it's been rock solid. I work for a printing company, and this server holds customer files that we use to print from.  The server runs fine all day but at night occasionally it will show an error and crash.  The error is "PROC_1 Status processor sensor IERR" and "PROC_2 Status processor sensor IERR".  At first I thought it was a processor error, but since I doubt both processors would fail at the same time I did some research on the error.  It appears that it means the processor(s) detected a system failure which is usually memory related.  So I pulled out 2 sticks and the server ran fine for a couple of weeks, until it happened again.

I was switching ram sticks around and ended up getting a beep code error at startup.  I switched them back but still had the code which was no ram installed error. Ugh.  I know there must be a better way to reset the ram configuration but I did it by resetting the NVRAM.  There is a jumper on the motherboard for that.  After resetting the NVRAM and rebooting, the beep code memory error was gone.  Also gone was my RAID configuration!

The server has a PERC 4e/Di RAID controller which wasn't being recognized at startup.  I entered the BIOS configuration and it was configured for SCSI and not RAID.  So I changed it to RAID and saved.  I exited the BIOS and restarted which is when I got this:

## "Mode change from SCSI to Raid on Channel A of the embedded Raid subsystem.  Data loss will occur!  Press (Y) to confirm changes, press any other key to cancel."

The RAID is configured with 8 physical drives and 2 virtual drives which hold live jobs.  Blowing away data was not an option.

Here is what I did to solve the problem:

1. Shutdown

2. Pull all 8 hot swap drives.

3. Boot and enter BIOS config.

4. Change BIOS from SCSI to RAID for channels A and B.

5. Save and exit. Ignore data loss message because no drives are connected.

6. Install all 8 drives and reboot.

7. Check RAID config with Control-M when the RAID controller posts.


All good. The server came up with the RAID config intact.  At this point after completing the steps above, I'm confident that I could have just changed the BIOS config to RAID without disconnecting the drives. 
