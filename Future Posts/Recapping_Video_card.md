I was in a jam.

The complete tale is long and boring, but I'll try to give you the super condensed backstory. At work, we need a Mac running a specific older version of OS X around 10.6.  Currently, we have an old MacPro tower that we use and recently it has been showing it's age.  It started out as random lock ups that could be fixed with a reboot.  As the problem progressed, occasionally the screen would start flashing. Rebooting would fix this as well.  I figured it was a driver issue that I needed to straighten out, but at the time there were more pressing problems to deal with. I'd get to it soon.

Starting up in safe mode doesn't load all the kernel extensions, and that worked for a week or two.  Some theorized that there was an OS update that was not compatible with the older video hardware that was in this box (NVIDIA GeForce 7300 GT).  I'm guessing it just temporarily took some load off the card which made it work better for a while.

Then, it stopped working completely. On startup, as soon as the kernel loaded the video, the display would flash like a strobe light.  I pulled the card out and saw this:

PIC OF CARD


So I needed a new video card. However there are only a few video cards that work in this box with the specific OS I need to run, none of which are still manufactured.  I was able to pick up two used cards on the cheap, but it would take a couple of days to get them.

Like I said, I was in a jam.
So I busted out this old thing:

PIC OF SOLDERING GUN

Yes, I know that's not the right tool for the job but I don't normally do electronics work so I don't have a soldering station. I also know it's never a good idea to use old capacitors to replace old failed capacitors. But I had nothing to lose since the card was already broken.
I really like old tools. I have three newer soldering guns that don't work anywhere near as good as this old one. It gets red hot very fast. (and makes the room lights dim!)  Everything I read about re-capping boards said you have to have a hot iron and work quickly. Heat can destroy components.

I was able to dig up an old motherboard that had the right value capacitors that still looked good.  So I de-soldered them.  

PIC OF OLD CAPS AND NEW CAPS

Then took the failed capacitors off the video card.

PIC OF CARD NO CAPS

Cleaned up the holes in the board carefully with a sewing needle.

PIC OF NEEDLE AND LOOP

And finally, soldered the capacitors on the card.  

PIC OF FIXED CARD

Not bad for the first time soldering on a board.  Oh, and now the card works flawlessly.
