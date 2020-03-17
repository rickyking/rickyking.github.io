---
layout: "post"
title: "Data Science WSL Proxy Setup Behind Enterprise Environment"
date: "2020-03-11 09:49"
updated: "2020-03-11 09:49"
tags:
  - data science
  - wsl
  - ubuntu
categories:
  - Notes
---

WSL is a life-saver for enterprise developper that needs a `bash-like` environment.

Setting up `cntlm` will help us to make the WSL communicate with outside world. The steps are simple and straight forward.

## Step 1: install cntlm and configure the proxy

To install cntlm in WSL ubuntu do the following: `sudo apt install cntlm`

However, if you are in the enterprise network, you will not be able to do so. So go to [https://packages.ubuntu.com/bionic/net/cntlm]() download the relevant package.

To configure the cntlm for ubuntu, we need to set up conf in `/etc/cntlm.conf`: `domain`, `username`, `password` and `proxy:port`

An example of the `cntlm.conf` content:

```
Username        john.smith
Domain          corp

Auth            NTLMv2
PassNTLMv2      8B767A8C8A34AD69ED6DDD5BF42C0CB8

Proxy           10.11.1.23:8080 # company proxy 
NoProxy         localhost, 127.0.0.*, 10.*, 192.168.*
Listen          3128 # proxy for cntlm
```
One trick to obtain the correct `Auth` and `PassXXXX` setting is to run: `sudo cntlm -M http://google.com` and type the password to get the right setup at the end.

Now we are ready to do restart `sudo service restart cntlm`

<!--more-->

## Step 2: setup the proxy in system

**Linux system**

For the linux system to recognize the proxy, we need to set it up:

Put the following inside of `~/.bashrc`:

```
export http_proxy=http://127.0.0.1:3128
export https_proxy=http://127.0.0.1:3128
```

**apt-get**

put the following into `/etc/apt/apt.conf.d/proxy.conf`

```
Acquire {
  HTTP::proxy "http://127.0.0.1:3128";
  HTTPS::proxy "http://127.0.0.1:3128";
}
```

**R**

c.f. : [https://support.rstudio.com/hc/en-us/articles/200488488-Configuring-R-to-Use-an-HTTP-or-HTTPS-Proxy]()

To set the required environment variables you should add them to the R environment file. On RStudio Server this file is found at `R_HOME/etc/Renviron.site`. On RStudio Desktop this file is found in the user home directory at`~/.Renviron`. 

Put the following content:

```
http_proxy=http://127.0.0.1:3128
https_proxy=http://127.0.0.1:3128
```

One more trick to debug the connection try: `options(internet.info = 0)`

**pip/conda**

No further setup required.

## Step 3: SSL certificate

We are not done yet. We need to get the SSL certificate to make all the stuff works.

To get the certificate, we can obtain the certificate as follows on Windows in Chrome. Assuming your proxy performing an MITM, go to a website which is using the proxy certificate. Click the lock icon next to the URL in chorme in the address bar. Click certificate. Go to Certification Path tab. Select the root certificate in the tree. Then click View Certificate.

In the new dialog box, go to Details tab and click Copy to File. Then export the certificate as a base 64 encoded file. Place this file in a sensible location.

For conda/pip set:

```
pip config set global.cert path/to/ca-bundle.crt
pip config list
conda config --set ssl_verify path/to/ca-bundle.crt
conda config --show ssl_verify

# Bonus while we are here...
git config --global http.sslVerify true
git config --global http.sslCAInfo path/to/ca-bundle.crt
```

## Unset the proxy

While we are out of proxy, we can do:

```
unset http_proxy
unset https_proxy
conda config --set ssl_verify false
```

## Reference

- [https://vijiboy.wordpress.com/2018/03/14/configure-cntlm-to-generate-hash-for-your-password-h-and-verify-m/]()
- [https://medium.com/@shuaib2m/setting-up-wsl-behind-an-ntlm-proxy-987ecf6e3788]()
- [https://support.rstudio.com/hc/en-us/articles/200488488-Configuring-R-to-Use-an-HTTP-or-HTTPS-Proxy]()
- [https://superuser.com/questions/437330/how-do-you-add-a-certificate-authority-ca-to-ubuntu]()
- [https://stackoverflow.com/a/33699578]()
