---
title: "Recapping a video card"
layout: "single"
classes: wide
---

**I was in a jam.**

At work, we need one Mac running a specific older version of MacOS, around 10.6.  Currently, we have an old MacPro tower that we use and recently it has been showing it's age.  It started out as random lock ups that could be fixed with a reboot.  As the problem progressed, occasionally the screen would start flashing. Rebooting would fix this as well.  I figured it was a driver issue that I needed to straighten out, but at the time there were more pressing problems to deal with. I'd get to it soon.

Starting up in safe mode doesn't load all the kernel extensions, and that worked for a week or two.  Some people have theorized that there was an OS update that was not compatible with the older video hardware that was in this box (NVIDIA GeForce 7300 GT).  I'm guessing it just temporarily took some load off the card which made it work better for a while.

Then, it stopped working completely. On startup, as soon as the kernel loaded the video, the display would flash like a strobe light.  I pulled the card out and saw this:

<figure>
  <img src="/images/mac_video_card_fix/IMG_5447.jpg" alt="pic of bulging capacitors">
  <figcaption>Tired video card</figcaption>
</figure>

<figure>
  <img src="/images/mac_video_card_fix/IMG_5448_arrows.jpg" alt="pic close up capacitors">
  <figcaption>Look at the bulging capacitors!</figcaption>
</figure>

So I needed a new video card. However there are only a few video cards that work in this box with the specific OS I need to run, none of which are still manufactured.  I was able to pick up two used cards on the cheap, but it would take a couple of days to get them.

Like I said, I was in a jam.
So I busted out this old thing:

<figure>
  <img src="/images/mac_video_card_fix/IMG_5455.jpg" alt="pic of soldering iron">
  <figcaption>Back when stuff was built to last</figcaption>
</figure>

That's *not* the right tool for the job but I don't do a lot of electronics work so I don't have a soldering station. I also know it's never a good idea to use old capacitors to replace old failed capacitors. But I had nothing to lose since the card was already broken.  

I really like vintage tools. I have three newer soldering guns that don't work anywhere near as well as this old one. It gets red hot very fast. (and makes the room lights dim.)  Everything I read about re-capping boards said you have to have a hot iron and work quickly. Heat can destroy components.  


I was able to dig up an old motherboard that had the right value capacitors that still looked good.  So I de-soldered them.  

<figure>
  <img src="/images/mac_video_card_fix/IMG_5457.jpg" alt="pic old and new caps">
  <figcaption>Same but different</figcaption>
</figure>

Then took the failed capacitors off the video card.  

<figure>
  <img src="/images/mac_video_card_fix/VXUR4680.jpg" alt="pic of card no caps">
  <figcaption>Out with the old</figcaption>
</figure>

Cleaned up the holes in the board carefully with a sewing needle.  

<figure>
  <img src="/images/mac_video_card_fix/IMG_5456.jpg" alt="pic of needle and loupe">
  <figcaption>Solder by day, sutures by night</figcaption>
</figure>

And finally, soldered the capacitors on the card.  

<figure>
  <img src="/images/mac_video_card_fix/IMG_5462.jpg" alt="pic replaced caps">
  <figcaption>Success</figcaption>
</figure>

Not bad for down and dirty.  Oh, and now the card works flawlessly.
