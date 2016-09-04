---
layout: post
title: "ldoce5viewer Installation on a Debian based 64-bit Distro"
tags:
- English
---

Longman Dictionary of Contemporary English 5 (LDOCE5) software won't install on
Ubuntu GNOME 16.04 LTS, but it is not a big deal since I wouldn't use the
official software. I use the great [ldoce5viewer] -a python based application-
instead. It requires *ldoce5.data* directory from the LDOCE5 DVD-ROM.
[ldoce5viewer] extracts the data, creates its index for incremental search out
of *ldoce5.data* folder, and it is ready to go. I find it much more
user-friendly and faster than the official software.

Here is how I built and installed [ldoce5viewer] on my distro.

1. Install prerequisite packages.
 
{% highlight bash %}
sudo apt-get install python-qt4 pyqt4-dev-tools python-lxml \
    python-gst0.10 gstreamer0.10-plugins-good \
    python-qt4-phonon checkinstall
    
# distro python-whoosh won't work, use this instead
sudo apt-get install python-pip
sudo pip install --upgrade pip
sudo pip install whoosh
{% endhighlight %}

2. Git clone [ldoce5viewer], build and install.

{% highlight bash %}
git clone https://github.com/ciscorn/ldoce5viewer.git
cd ldoce5viewer
make build
sudo checkinstall
{% endhighlight %}

3. Copy *ldoce5.data* from LDOCE5 DVD-ROM to `/usr/share/ldoce5viewer` or
anywhere you like and change access permissions to read-only for regular files
and read & execute for directories, otherwise you can't `cd` into the
directories. Adjust directory names for your computer.

{% highlight bash %}
sudo su
mkdir /usr/share/ldoce5viewer
cp -R /media/gokhanettin/LDOCE5/ldoce5.data /usr/share/ldoce5viewer/ && sync
find /usr/share/ldoce5viewer/ldoce5.data -type d -print0 | xargs -0 chmod 555 
find /usr/share/ldoce5viewer/ldoce5.data -type f -print0 | xargs -0 chmod 444
exit
{% endhighlight %}

Fire up [ldoce5viewer] and locate /usr/share/ldoce5viewer/ldoce5.data when it
asks. After a while, it should complete indexing and words should appear as you
type on it.

If you want to remove [ldoce5viewer] and *ldoce5.data* from your computer run
the following commands.

{% highlight bash %}
sudo dpkg -r ldoce5viewer
sudo rm -rf /usr/share/ldoce5viewer/ldoce5.data
{% endhighlight %}

[ldoce5viewer]: https://forward-backward.co.jp/ldoce5viewer/
