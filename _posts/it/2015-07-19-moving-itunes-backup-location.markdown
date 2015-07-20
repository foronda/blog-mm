---
layout: post
title: Moving iTunes Backup Location
modified:
categories: it
excerpt: Move iTunes backup location to free up SSD OS drive
tags: [it, itunes, pc]
image:
  feature:
date: 2015-07-19T10:25:45-10:00
---

###Problem: 
Operating system (C:) drive running out of space due to iTunes backup.This is very useful if your OS runs on SSD (which is common today) to save valuable space.

###Solution: 
Change the backup location of iTunes using Windows mklink junction command.

###Procedure:
1. Rename the backup folder under %AppData%\Apple Computer\MobileSync\Backup to Backup-old
2. Create the new folder location i.e (D: iTunes Backup)
3. Run the junction command.

{% highlight css %}
    mklink /j current target
{% endhighlight %}
{% highlight css %}
    mklink /j "%appdata%\Apple Computer\MobileSync\Backup" "D:\iTunes Backup"
{% endhighlight %}

----------

###References:

1. http://www.howtogeek.com/164275/how-to-change-the-backup-location-of-itunes-or-any-windows-app/
2. http://social.technet.microsoft.com/Forums/en-US/8700d04f-45b6-45db-bab5-48bd86a69e18/mklink-error-the-syntax-of-the-command-is-incorrect?forum=w8itprogeneral