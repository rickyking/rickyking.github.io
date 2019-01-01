---
title: Install and Configure R on Mac OS X
tags:
  - Mac OS X
  - R
categories:
  - R
date: 2015-11-05 13:07:00
---
Have been changed to work on Mac OS X for a while with R, here is some documentation for configure R in Mac.

## Install R with homebrew

Install the [missing package management for OS X](http://brew.sh/)
```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Then retrieve the [caskroom](http://caskroom.io/) for R binary installation:

```bash
brew install caskroom/cask/brew-cask
```

Then install R:

```bash
brew install Caskroom/cask/r
```

## R configuration

Use a browser for R help document viewing:

1. create a .rprofile in the home location
2. add `options(help_type = "html")` in the .rprofile

Internalisation of R, run the code in terminal:

```bash
defaults write org.R-project.R force.LANG en_US.UTF-8
```
