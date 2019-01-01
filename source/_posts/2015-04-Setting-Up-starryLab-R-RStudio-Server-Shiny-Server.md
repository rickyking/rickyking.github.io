---
title: "Setting Up starryLab (R, RStudio-Server, Shiny-Server, ...)"
date: 2015-04-22 22:06:10
tags: [r, starrylab]
categories: [R]
---

{% asset_img starrylab.png %}

Recently I discovered a low-cost VPS service. So I decided to create a small lab (not so powerfull) to demonstrate/use the power of R/Python/spark(not yet) toolbox.

[Runabove](http://runabove.me/FWKR) from [http://www.ovh.com/](http://www.ovh.com/) provides the cheap vps as low as `$0.004/hr`, that is `$2.50/mo`, even cheaper than the cheapest [digital ocean's](https://www.digitalocean.com/) VPS.

I set up an instance `Cloud Sandbox Large` with `ubuntu 14.04` which has:

- 1 core
- 4 GB RAM
- 30 GB SSD
- 1 TB Bandwidth*

This is sufficient to run R/python.

Once the instance is set-up, login via `ssh`. Before the configuration, update & upgrade the system in first place.

```
sudo apt-get update
sudo apt-get upgrade
```

*PS. Server configuration & dns redicrection is not documented here*

<!-- more -->

## oh-my-zsh (optional)

First, installation of a new shell with a super cool **theme** and **functions** like `git` plugin which display the status of current working repository.

Get it from [http://ohmyz.sh/](http://ohmyz.sh/)

First install `zsh`.

```
sudo apt-get install git zsh
```

Install `oh-my-zsh`, via wget: `wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh`.

Use `chsh` to change the `shell` program.

PS. Log-out required.

## r-base & r-cran pre-compiled

Reference: [http://cran.r-project.org/bin/linux/ubuntu/README](http://cran.r-project.org/bin/linux/ubuntu/README)

Installing r-base is easy via `ubuntu`, but to use the latest version, I have to do the followings:

 - Add `deb http://cran.rstudio.com/bin/linux/ubuntu trusty/` to `/etc/apt/sources.list`
 - Add secure-key `sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9`
 - And install `sudo apt-get install r-base r-base-dev`

`R` pacakge compiling in linux is time consuming, by using a pre-built package is a great solution, here is how:

```
sudo add-apt-repository ppa:marutter/rrutter -y
sudo add-apt-repository ppa:marutter/c2d4u -y
sudo apt-get update
```

Now try search `apt-cache search r-cran`.

## docker

[Reference](http://docs.docker.com/installation/ubuntulinux/)

[Docker](https://www.docker.com/) is super hot recently. It provides an instant way of linux emulation.

To install on `ubuntu 14.04` is super simple:

- Get and sh `wget -qO- https://get.docker.com/ | sh`
- Add your user to docker group to avoid `sudo docker` each time: `sudo usermod -aG docker admin`

## rstudio-server

[Reference](http://www.rstudio.com/products/rstudio/download-server/)

Installation:

```
sudo apt-get install -y gdebi-core
sudo apt-get install -y libapparmor1
wget http://download2.rstudio.org/rstudio-server-0.98.1103-amd64.deb
sudo gdebi rstudio-server-0.98.1103-amd64.deb
```

## shiny-server

[Reference](http://www.rstudio.com/products/shiny/download-server/)

`shiny` package is required:

via R:

```
sudo su - \
-c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""
```

via pre-built Package:

```
sudo apt-get install r-cran-shiny
```

Installation:

```
sudo apt-get install gdebi-core
wget http://download3.rstudio.org/ubuntu-12.04/x86_64/shiny-server-1.3.0.403-amd64.deb
sudo gdebi shiny-server-1.3.0.403-amd64.deb
```

## Anaconda python

[Reference](https://store.continuum.io/cshop/anaconda/)

Anaconda is the popular `python` distribution for scientific computing.

Install:

```
wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.2.0-Linux-x86_64.sh
sudo bash Anaconda-2.2.0-Linux-x86_64.sh
```

## Setting up nginx

Next, I set up nginx in order to redirect the access by subdirectory to localhost port.

```
sudo apt-get install nginx
```

Edit `/etc/nginx/sites-available/default`

Add in `server` section (websocket support added for shiny and rstudio server):

```
## setting for rstudio
location /rstudio/ {
  rewrite ^/rstudio/(.*)$ /$1 break;
  proxy_pass http://localhost:8787;
  proxy_redirect http://localhost:8787/ $scheme://$host/rstudio/;
      # WebSocket support
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  proxy_read_timeout 86400;

}
## setting for shiny
location /shiny/ {
     rewrite ^/shiny/(.*)$ /$1 break;
     proxy_pass http://localhost:3838;
     proxy_redirect http://localhost:3838/ $scheme://$host/shiny/;

       # WebSocket support
     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection "upgrade";
     proxy_read_timeout 86400;
}
```

## Add gogs (Go Git Service)

[Gogs](http://gogs.io/) is `Go lang` based `git service`.

For full installation documentation, please refer to [official documentation](http://gogs.io/docs/intro/)

Get the binary:

```
cd /opt/
wget https://github.com/gogits/gogs/releases/download/v0.6.1/linux_amd64.zip
unzip linux_amd64.zip
cd gogs
mkdir custom && cd custom
mkdir conf && cd conf
```

`nano app.ini` to insert the following lines:

```
[repository]
ROOT = /home/****/gogs-repositories
[server]
ROOT_URL = http://lab.jinyi.me/gogs/
```

edit nginx file to add gogs reverse-proxy:

```
    location /gogs/ {
        proxy_pass http://localhost:3000/;
    }
```

Run `gogs` with `screen`:

```
sudo apt-get install screen
screen
cd /opt/gogs/
./gogs web # launch with proper account
```

Browse to [http://lab.jinyi.me/gogs/install] for setup.

## Next

Now the **starryLab** is set-up and can be visitied via [http://lab.jinyi.me](http://lab.jinyi.me).

Next is to use `git` as my personal **git repository** and upload my previous developped **shiny application**.
