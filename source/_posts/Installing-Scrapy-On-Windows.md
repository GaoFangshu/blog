---
title: Installing Scrapy On Windows
date: 2016-10-23 11:00:52
tags: Python
---
Installing Python packages takes me some time for getting used to. Fortunately, a step-by-step [Tutorial](https://www.youtube.com/watch?v=eEK2kmmvIdw) on YouTube helps a lot.


## Check Python version and environment variables
Open command prompt and type in `pyhton`, check the edition (Python 2.7 and 32-bit) of Python. Additionally, be sure that environment variable `PATH` already contain `C:\Python27;C:\python\Scripts`.

<!--more-->

## Download and install dependencies
* [Visual C++](https://www.microsoft.com/en-us/download/details.aspx?id=29).
* [Win32 OpenSSL](http://slproweb.com/products/Win32OpenSSL.html), full version, not light version. Then, add `C:\OpenSSL-Win32\bin` to environment variable `PATH`.
* [Python setuptools](https://pypi.python.org/pypi/setuptools)
* [pywin32](https://sourceforge.net/projects/pywin32/files/)
* [Twisted](http://twistedmatrix.com/trac/wiki/Downloads)
* [zope.interface](https://pypi.python.org/pypi/zope.interface/4.3.2), download egg for win32. Then, type `cd Your:\directory\of\egg` and `easy_install zope.interface-4.3.2-py2.7-win32.egg` (or other similar egg file).
* [lxml](https://pypi.python.org/pypi/lxml/3.6.0)
* [pyOpenSSL](https://pypi.python.org/pypi/pyOpenSSL/0.13), easy_install the egg file.

## Install Scrapy
Type `easy_install Scrapy`, then check the installation by typing `Scrapy version`.

**PS:** Version conflict may happen during the installation. In most cases, trying a newer version can solve this problem.