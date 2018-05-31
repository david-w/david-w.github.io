Getting an older Cisco ASDM client to run on a newer version of Java is a challenge.  Recently, I upgraded an old PIX525 from version 6 to 8 (yikes!).  I use the command line for configuration but I wanted to run ASDM for monitoring purposes.  Even though version 8 is old, it was the most recent I could install on a PIX and that limits the version of ASDM I can run as well. It uses a self signed certificate and the Java applet was complaining about a weak signature.

The error message when I tried to open the jdsm.jnlp file was:
"Unsigned application requesting unrestricted access to system.  The following resource is signed with a weak signature algorithm MD5withRSA and is treated as unsigned"

The quick down and dirty method to get around this temporarily on Mac OS is to edit the java.security file at:
/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security.

Remove MD5 from this line: jdk.jar.disabledAlgorithms=MD2, MD5, RSA keySize < 1024, DSA keySize < 1024

This will re-enable MD5 algorithm signatures being accepted. It will be overwritten on the next java upgrade.

For a persistent solution you can create a file that has the above referenced line only. (without the MD5 entry of course.)

Then just run this from the command-line:
java -Djava.security.properties=/path/to/your/file

This will append the running java.security.properties file with the modified line.

BUT THAT DID NOT WORK FOR ME!   I COULD MODIFY THE java.security.properties file though.
