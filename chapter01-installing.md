Ruby on Rails Install How-to
============================

Development System
------------------

This chapter's installation methods described are intended for
development systems. If you are looking for instructions on installing a
web server in the production system, please have a look at ?.

Both types of installation are very different.

### After the Installation?

If you have never worked with Ruby before, you should first read ?. If
you are already a Ruby master and want to move on to Rails now, then I
recommend reading ?.

Ruby on Rails 4.0 on Debian 7.1 (Wheeze)
----------------------------------------

Installation
Ruby on Rails
Debian 7.1
Debian 7.1
installation on Ruby on Rails
Ruby
Installation
Rails
Installation
RVM (Ruby Version Manager)
Ruby Version Manager
RVM
There are two main reasons for installing a Ruby on Rails system with
RVM (Ruby Version Manager):

-   You simply do not have any root rights on the system. In that case,
    you have no other option.

-   You want to run several Rails systems that are separated cleanly,
    and perhaps also separate Ruby versions. This can be easily done
    with RVM on the Linux system. Then you can run Ruby 1.9.3 with Rails
    3.2 and in parallel Ruby 2.0.0 with Rails 4.0 on the same computer.

> **Note**
>
> Detailled information on RVM can be found on the RVM homepage at
> <https://rvm.io/>.

This description assumes that you have a freshly installed Debian
GNU/Linux 7.1 (“Wheeze”). You will find an ISO image for the
installation at <http://www.debian.org>. I recommend the approximately
250 MB net installation CD image. For instructions on how to install
Debian-GNU/Linux, please go to <http://www.debian.org/distrib/netinst>.

### Preparations

If you have root rights on the target system, you can use the following
commands to ensure that all required programs for a successful
installation of RVM are available. If you do not have root rights, you
have to either hope that your admin has already installed everything you
need, or send them a quick e-mail with the corresponding lines.

First, an update of the package lists:

    root@debian:~#
    [...]
    root@debian:~#

> **Note**
>
> Of course, you can optionally install a SSH server on the system, so
> you can work on the system via SSH instead of on the console:
>
>     root@debian:~#

And now the installation of the packages required for the RVM
installation:

    root@debian:~#
    [...]
    root@debian:~#

Now is a good time to log out as root:

    root@debian:~#
    Abgemeldet

### Installing Ruby 2.0.0 and Ruby on Rails 4.0 with RVM

Log in with your normal user account (in our case, it's the user `xyz`).

RVM, Ruby, and Ruby on Rails can be installed in various ways. I
recommend using the following commands and get at least one cup of
tea/coffee/favorite drink:

    deployer@debian:~$
    [...]
    deployer@debian:~$
    [...]
    deployer@debian:~$

RVM, Ruby 2.0.0 and Rails 4.0 are now fully installed.

Ruby on Rails 4 on Windows
--------------------------

installation
of Ruby on Rails
on Windows
Windows
installation of Ruby on Rails
Ruby
installation
Rails
installation
At <http://railsinstaller.org/> there is a simple, all-inclusive Ruby on
Rails installer for Windows for Ruby 2.0.0 and Rails 4.0. Optionally,
you can also install SSH and Git.

Ruby on Rails 4 on Mac OS X 10.7
--------------------------------

Installation
Ruby on Rails
Mac OS X 10.7
Mac OS X 10.7
Installation of Ruby on Rails
Ruby
Installation
Rails
Installation
RVM (Ruby Version Manager)
Ruby Version Manager
RVM
Mac OS 10.7 includes Ruby version 1.8.7 as standard. Not interesting for
our purposes. We want Ruby 2.0.0 and Rails 4.0. To avoid interfering
with the existing Ruby and Rails installation and therefore the packet
management of Mac OS X, we install Ruby 2.0.0 and Rails 4.0 with RVMRuby
Version ManagerRVM (Ruby Version Manager).

With RVM, you can install and run any number of Ruby and Rails versions
as normal user (without root rights and in your home directory). So you
can work in parallel on old projects that may still use Ruby 1.9.3 and
new projects that use Ruby 2.0.0.

> **Note**
>
> Detailled information on RVM can be found at the RVM homepage at
> <https://rvm.io/>.

### Xcode Installation or Upgrade

Xcode
Before you start installing Ruby on Rails, you must install the *latest*
Apple Xcode tools on your system. The easiest way is via the Mac App
Store (search for "xcode") or via the website
<https://developer.apple.com/xcode/>. Please take care to install all
the command line tools.

### Installing Ruby 2.0.0 and Ruby on Rails 4.0 with RVM

RVM can be installed in various ways. I recommend using the following
monster command (please copy it exactly) that installs the latest RVM,
Ruby and Ruby on Rails in your home directory:

    $
    [...]
    $
    [...]
    $
