I use RVM to manage Ruby on my Mac laptop.  RVM is very cool and has a lot of great features, most of which I don't use.  macOS comes with Ruby installed, but Apple doesn't want you messing with it and it's difficult to manage. Sometimes other software requires a different version of Ruby than my "system Ruby".  I use RVM for forcing macOS to use the version of Ruby that I need.  It's very good at that, and simple to use.

I won't go over installation here. The [RVM](https://rvm.io/rvm/install) site has detailed installation instructions.

RVM Commands - Not a comprehensive list. Just some commands I use frequently.

`rvm list known`  -  Shows all versions of Ruby available for installation.

`rvm install 2.2.7` - Installs Ruby version 2.2.7

Then switching between Rubies is easy.

`rvm use 2.2.7`

`ruby -v` - Check which version of Ruby is loaded.

`which ruby` - Shows the path to the Ruby that is running.


`rvm get stable` - Upgrades rvm to the latest stable version available.

`rvm list`
