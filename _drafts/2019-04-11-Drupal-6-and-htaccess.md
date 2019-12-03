---
title: "Drupal v6 and htaccess files"
layout: "single"  
---


When I first started using Apache HTTP server on Linux, every reference to maximizing Apache's performance said I should not use .htaccess files if possible. In fact, taken right from  Apache Cookbook 2nd Edition - O'Reilly, ".htaccess files cause a substantial reduction in Apache's performance." Even if using .htaccess files would cause minimal performance issues, I'd have to think twice about using them.

When I started using Drupal, I was surprised that there wasn't more discussion about how to avoid using .htaccess altogether. I did find a couple of threads on http://www.drupal.org , but not as much as I would have imagined.



Here is how I handle avoiding .htaccess files in Drupal installations.



( For clarification, I usually configure Apache to use Virtual Hosts, even if I am only using it for one site. These notes reflect a Virtual Host environment using Apache2 on Linux)



     Note:  The following notes describe incorporating the core Drupal .htaccess file directly into the Apache config.  I am not a security expert, however, I believe this is a safe method to control directories. To use this method, obviously you need to be able to modify Apache config files directly.  If you are using a hosted server, and don't have access to Apache then you're TSOL. (if you are on a hosted server, and using .htaccess is necessary, this site has info for common configurations htaccess-guide.com) It's also worth noting that with each Drupal core update, the .htaccess file included with the newer version must be checked against the currently running configuration. Luckily, there is an id at the bottom of the Drupal .htaccess file for easy reference.  If the version id code does not change, there is no need to replace the running config.


     I incorporate the Drupal .htaccess file into my virtual host configuration file like this:

```sh
<VirtualHost *:80>

        ServerAdmin webmaster@localhost



        DocumentRoot /var/www/

        <Directory />

                Options FollowSymLinks

                AllowOverride None

        </Directory>

        <Directory /var/www/>

##########################################################################

### This is the contents of the .htaccess file that comes with Drupal

### The version no. is at the end. When upgrading Drupal CHECK THIS TO THE .htaccess file with the distro!

#

# Apache/PHP/Drupal settings                                   <<<<---INSERT ENTIRE DRUPAL .htaccess FILE HERE



 Blah Blah Blah  Blah.



# $Id: .htaccess,v 1.90.2.4 2009/12/07 12:00:40 goba Exp $     <<<<---SEE IF THIS VERSION NUMBER HAS CHANGED

 # End of Apache/PHP/Drupal settings.                                                                   

##########################################################################

                Order allow,deny

                allow from all

        </Directory>



        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/

        <Directory "/usr/lib/cgi-bin">

                AllowOverride None

                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch

                Order allow,deny

                Allow from all

        </Directory>



        ErrorLog /var/log/apache2/error.log



        # Possible values include: debug, info, notice, warn, error, crit,

        # alert, emerg.

        LogLevel warn



        CustomLog /var/log/apache2/access.log combined



    Alias /doc/ "/usr/share/doc/"

    <Directory "/usr/share/doc/">

        Options Indexes MultiViews FollowSymLinks

        AllowOverride None

        Order deny,allow

        Deny from all

        Allow from 127.0.0.0/255.0.0.0 ::1/128

    </Directory>



</VirtualHost>
```


First, I use diff to check the changes between the old and the new .htaccess files. ( I could use sdiff, and combine the files, but this way I can edit fast and easy using Nano)
Then I use Nano to make the modification to the config file:  


`$ sudo nano /etc/apache2/sites-available/default`
