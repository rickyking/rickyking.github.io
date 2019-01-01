---
title: SetUp Jupyterhub in CEntOS 7
tags: [centos, jupyterhub, ipython]
categories: [toolbox]
date: 2015-12-16 09:26:49
---

The [Jupyter Notebook](http://jupyter.org) is a web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text, just like RStudio's rmarkdown. The project comes from [IPython](http://ipython.org) which now becomes a kernel for `Jupyter`.

[Jupyterhub](https://github.com/jupyter/jupyterhub) is a multi-user server that manages and proxies multiple instances of the single-user Jupyter notebook server.

The ubuntu-like installation guide can be found [here](https://github.com/jupyter/jupyterhub/blob/master/README.md). And here goes the installation steps for CEntOS 7.

<!--more-->

## Installation

Jupyterhub requires Python >= 3. Use `whereis -b python` to check the version.

If Python3 does not show up, use `yum`:

```bash
sudo yum search python3
sudo yum -y install python34
```

Next, install the necessary component for `Jupyterhub`:

```bash
sudo  yum  -y  install   zlib-devel   bzip2-devel   openssl-devel   ncurses-devel   sqlite-devel readline-devel   tk-devel   gdbm-devel   db4-devel   libpcap-devel   xz-devel  npm
```

Use `npm` to install `http-proxy` in nodeJS:

`sudo npm install -g configurable-http-proxy`

You should have `wget`, if not:

```bash
sudo yum -y install wget
```

Install `pip3` by:

```bash
wget https://bootstrap.pypa.io/get-pip.py
chmod +x ./get-pip.py
python3.4 get-pip.py
```

The dev component for Python (2 & 3):

```bash
sudo yum -y install python-devel  python34-devel
```

Let's go and get `jupyterhub` via `pip3`: `sudo pip3 install jupyterhub ipython[notebook]`

Now we are ready to start `jupyterhub` by simply issuing `sudo jupyterhub`, and go to [http://localhost:8000]() (8000 is the default port)

## Configuration for Kernels

With the default configuration, we can only use `python3` kernel, in order to be able to use `python2`, the following command is necessary:

```bash
python -m IPython kernelspec install-self
# python3.4 -m IPython kernelspec install-self
```
