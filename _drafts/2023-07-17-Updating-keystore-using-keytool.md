---
title: "Updating SSL cert on Windows in java keystore using keytool"
layout: "single"
classes: wide
---


## **Installing a regenerated (updated) SSL Cert using keytool for Windows Server**

This is how I updated an SSL certificate for a Windows Tomcat web server without resubmitting a CSR (certificate signing request).

### **Background**

We purchase five year SSL certificates from ssl.com, but since September 2020 because of Apple's lovely certificate policy change, certificates have a maximum validation period of 397 days.  So that means after 397 days, the server certificate expires but the original certificate order on ssl.com is still valid. A new certificate must be generated and installed on our server.

Since I was not the person who originally submitted the certificate request for this server, and also am not a designated contact, I would not receive the email needed for verification if I submitted a new CSR.

ssl.com has the option to regenerate a certificate using the existing CSR, which they even keep on file.  This makes it easy to reprocess an existing valid certificate request and download a signed server certificate good for another 397 days.

The part that wasn't exactly easy, was that the instructions on the ssl.com site were incorrect for importing the new certificate into the Java keystore which Tomcat uses.   

I struggled with this for quite some time before I figured it out.  I could not find a good reference anywhere.  I found lots of references, but none seemed to be accurate to my particular situation or they had the same commands that I had already used unsuccessfully. I finally pieced together the solution from multiple sources, plus trial and error.

This is what worked for me.
Since I do this only once a year, I'm documenting this as a reference for next time.

Before changing anything, duplicate the existing keystore and stash it, so in case something breaks, you can revert back to the expired certificate.  In my case the server runs a critical application used daily, and having an expired security certificate is much better than a broken web server.

Download the new signed certificate and any required root and intermediate certs needed to complete the chain of trust.  

I needed the following certificates in my keystore:
CERTUM_TRUSTED_NETWORK_CA.crt	Root 1 Certificate
SSL_COM_ROOT_CERTIFICATION_AUTHORITY_RSA.crt	Root 2 Certificate
SSL_COM_RSA_SSL_SUBCA.crt	Intermediate Certificate
my_server_name_here.crt	Signed Server Certificate

** **See note at the end of this post about missing CERTUM Root 1 cert.**

** **Also note:**
On my server, keytool is located at:
**C:\Program Files\Java\jre1.x.x_x\bin\keytool**
For some reason, adding that location to my path wasn't working, so I executed commands using the full path to the keytool application. Since there is a space in the path that needed to be escaped, the actual commands I executed included:
**"C\Program Files\Java\jre1.x.x_x\bin\keytool"**
Later I realized I could have just navigated to where the keytool application is located and executed the commands from there. So for ease of explanation, the commands listed assume that you've either changed directory to the location where the keytool app is, or have added the location to your user path, and **C:\key\mykey.jks** is the path to the keystore and the required root/intermediate certs.


The keystore is password protected, so you'll need the password to modify it.
{: .notice--info}

First, to see the contents of the existing keystore:

    keytool -list -keystore C:\key\mykey.jks

Note the names of the entries.  These are aliases.  When modifying certs, the same alias names must be used.

Next, we need to replace the root and intermediate certs in the correct order starting with root1. In order to do that, we need to delete the entries for the chain of trust, then re-import. (Not deleting first will generate an error.)

So, delete the root1 alias:

    keytool -delete -alias root1 -keystore C:\key\mykey.jks

Then, import the new root1 certificate:

    keytool -import -trustcacerts -file C:\key\CERTUM_TRUSTED_NETWORK_CA.crt -alias root1 -keystore C:\key\mykey.jks

The above command should return **"Certificate was added to keystore."**
{: .notice--info}

Delete the root2 alias:

    keytool -delete -alias root2 -keystore C:\key\mykey.jks

Then import the new root2 certificate:

    keytool -import -trustcacerts -file C:\key\SSL_COM_ROOT_CERTIFICATION_AUTHORITY_RSA.crt -alias root2 -keystore C:\key\mykey.jks
Delete the inter alias:

    keytool -delete -alias inter -keystore C:\key\mykey.jks

Then import the new intermediate certificate:

    keytool -import -trustcacerts -file C:\key\SSL_COM_RSA_SSL_SUBCA.crt -alias inter -keystore C:\key\mykey.jks

**Note:** At this point the ssl.com docs say to use the same commands to import the new signed server certificate. The import worked and I got the same "Certificate was added to keystore" message, but the certificate was broken and Tomcat would not serve any web pages at all.

Here, I reverted back to the original keystore (since I backed it up) and performed the previous steps again.  When it came time to import the new signed certificate, I did **NOT** delete the alias for my signed server cert. I used the following command:

    keytool -import -file C:\key\my_server_name_here.crt -alias my_alias -keystore C:\key\mykey.jks

The above command should return **"Certificate reply was installed in keystore."**  This is important, and differs from the messages received when adding the root and intermediate certs to the keystore.
{: .notice--warning}

After that everything just worked.  
Then, viewing the contents of the keystore using the -list switch, I had 3 entries listed as trustedCertEntry, and one listed as PrivateKeyEntry.

The keytool docs were very helpful:
[https://docs.oracle.com/en/java/javase/18/docs/specs/man/keytool.html](https://docs.oracle.com/en/java/javase/18/docs/specs/man/keytool.html)


### ****Missing CERTUM root certificate.**

When I downloaded my signed certificate from ssl.com, the root1 cert (CERTUM_TRUSTED_NETWORK_CA.crt) was missing. There were supposed to be 4 certificates to complete the chain of trust. There were only 3 available to download for Tomcat (Java). I checked the downloads for other formats as well and none included the root CA cert.

Thinking I was not understanding, I contacted ssl.com support who were as confused as I was.  They gave me the url to a repository that included all the root certs they use, which did include 4 different CERTUM certs, but none were the correct file type (.crt).  

I ended up downloading it directly from CERTUM: [https://www.certum.eu/en/cert_expertise_root_certificates/](https://www.certum.eu/en/cert_expertise_root_certificates/)
They had all their root certificates in many formats and I was able to download the one I needed as a .crt file.
