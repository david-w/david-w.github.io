---
title: "macOS Rebuild LaunchServices Database"
layout: "single"
classes: wide
---

This is here because of macOS searchlight not indexing Outlook correctly and mail not being found.
I don't know if this command will help.
I just found a post that said this worked when nothing else did.
Have not tried this.
````
sudo /System/Library/Frameworks/CoreServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -kill -seed -lint -r -f -v -dump -domain local -domain system -domain user -domain network
````
