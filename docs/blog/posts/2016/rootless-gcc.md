---
date: 2016-06-16
tags:
    - gcc
    - linux
    - python
    - root
    - autoconf error
---

# Installing GCC w/o root

I cannot tell you how many times I have been asked to install a Python application on a shared-host server which did not have GCC pre-installed!  Actually, I can: twice.  I failed the first time, gave up and used a different server, and went on to live a happy life — or so I thought.  But I did not give up the second time.

Basically, I was installing a Python library which required pycrypto.  Everything was installing well with pip up to pycrypto until the `RuntimeError(“autoconf error“)` dampened my eager spirits.  This was the same issue I had run into in the past, but had long since suppressed the memory.  A quick Google search reminded me that the problem could be solved by simply running `sudo apt-get install gcc.` — which cannot be done without root privileges which I could have on the shared-host server I’m working with.

Unlike my first encounter, I decided this time to dig in and find a way.  That is when I found a helpful root-free gcc install tutorial over at <http://luiarthur.github.io/gccinstall>.

A note on my installation experience:

* I first tried GCC 5.4.0, but got:

```sh
cannot stat `libgcc_s.so.1′: No such file or directory
```

* Not knowing how to handle that (Google couldn’t find me a clear answer), I then tried GCC 6.1.0.  It took a while on my server, but it worked.

Edit:

When compiling Python or just installing via ‘pip’, add `env CC=/path/to/new/gcc` before your command:

```sh
~$ env CC=/path/to/new/gcc pip install ...
```

or, when compiling manually (exclude ––prefix if the default path is fine , usually default is something like ‘/usr/bin/blablabla’)

```sh
~$ env CC=/path/to/new/gcc ./configure ––prefix=/path/to/custom/directory

~$ make

~$ make install
```

Note that if you compiled Python from source using your local GCC, you will not need to explicitly declare your GCC with that Python’s installation of pip (since it by default uses the GCC which was used to compile Python).