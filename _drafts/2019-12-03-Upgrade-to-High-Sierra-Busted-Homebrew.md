Tried to update my github-pages gem for jekyll. got this error-

xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance

I don't have xcode installed, only CommandLineTools for Homebrew.  I guess I could install xcode but I don't want to.  I don't develop apps and I would rather not have all the extra BS.

rvm is still pointing to the correct Ruby installation even after the upgrade, but bundler is failing and so is Homebrew (brew doctor)

??Which broke ruby (I think), CommandLineTools, Bundler, and much more

So I tried to install CommandLineTools again like this -` xcode-select --install `
Then I updated homebrew.
I was then able to use bundler - `bundle install`


https://medium.com/@andv/fixing-tool-xcodebuild-requires-xcode-without-installing-xcode-84edcdc17833
https://shawnliu.me/post/homebrew-without-xcode/
