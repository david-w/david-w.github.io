---
title: "Manually Backing Up A Drupal Site"
layout: "single"  
---


I have an old Drupal site running Drupal 6 that I developed to manage inventory for one of our customers where I work.  I know that D6 is old and not supported, but the site works great and there is no reason to update it. It serves it's intended purpose well. These are the commands that I use to back up the site. There are many other ways to do this, but this is the down and dirty command line way I use.  

Once logged in to the server, navigate to the place you want to save the backup files then run the following commands.  

```sh
sudo tar cjf drupalbackup_04.11.2019.tar.bz2  /var/www/  
```  



Since Drupal uses a database, we have to back that up separately.  

```sh
sudo mysqldump -u drupal -p mysql-password > drupaldumpfile_04.11.2019.mysql  
```

That's it.  If you are doing this remotely over ssh, then you can open up a new terminal and use scp to copy the files to a safe spot.  
