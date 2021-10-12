---
title: "24 Helpful UNIX Commands Every Developer Should Know"
layout: post
date: 2017-11-10 14:48
image:
headerImage: false
tag:
- unix
- command line
- terminal
- bash
- aws
- devops
category: blog
author: flo
description: 24 Helpful UNIX Commands Every Developer Should Know.
# jemoji: '<img class="emoji" title=":ramen:" alt=":ramen:" src="https://assets.github.com/images/icons/emoji/unicode/1f35c.png" height="20" width="20" align="absmiddle">'
---
---

Learning the basic Unix commands is as a very useful skill in the daily life of a Developer. Being confident to use the command line as a tool to deploy, maintain and debug applications is a big chance to dive into the mystic area of DevOps.

In UNIX, **everting is a file**. And every file has a manual that describes it. The **man** command allows you to retrieve the information in the manual and display it as text output on your screen. Using the man command on itself, will give you the manual of the man command.

{% highlight unix %}
  ~$ man man
  NAME
         man - format and display the on-line manual pages

  SYNOPSIS
         man  [-acdfFhkKtwW]  [--path] [-m system] [-p string] [-C config_file] [-M pathlist] [-P
         pager] [-B browser] [-H htmlpager] [-S section_list] [section] name ...

  DESCRIPTION
         man formats and displays the on-line manual pages.  If you  specify  section,  man  only
         looks  in  that  section  of  the manual.  name is normally the name of the manual page,
         which is typically the name of a command, function, or file.  However, if name  contains
         a  slash  (/)  then  man  interprets  it as a file specification, so that you can do man
         ./foo.5 or even man /cd/foo/bar.1.gz.

         See below for a description of where man looks for the manual page files.

  OPTIONS
         -C  config_file
                Specify the configuration file to  use;  the  default  is  /private/etc/man.conf.
                (See man.conf(5).)

         -M  path
                Specify  the  list of directories to search for man pages.  Separate the directo-
                ries with colons.  An empty list is the same as not specifying -M  at  all.   See
                SEARCH PATH FOR MANUAL PAGES.

         -P  pager
{% endhighlight %}

The **pwd** or **print working directory** command will show you the full pathname of your current working directory.

{% highlight unix %}
  ~$ pwd
  /home/root
{% endhighlight %}

The **ls** or **list** comes with a dozens of options. The most common ones are:

*   **-l** to show information about the file e.g. file name, size, owner, permission etc
*   **-a** shows hidden files
*   **-lh** shows sizes in human readable format.
*   **-F** will add the ‘/’ Character at the end each directory.
*   **-r** option display files and directories in reverse order.
*   **-ltr** will shows latest modification file or directory date as last.
*   **-lS** displays file size in order, will display big in size first.

{% highlight unix %}
  ~$ ls
  bin  bitbucket  home  logstasher.log  cronjob.sh  lib
{% endhighlight %}

A shorter way for *ls -l* is the **ll** command. Depending on the OS version you use, *ll* is either aliased to *ls -alF* or *ls -l*.

{% highlight unix %}
  ~$ ll
  total 7
  drwxr-xr-x  3 root  root   4096 Nov  6 20:44 ./
  drwxr-xr-x  3 root  root   4096 Sep  5 09:27 ../
  drwxrwxr-x  1 root  root   4096 Aug 17 11:50 .bin/
  ...
{% endhighlight %}

The **cd** or **change directory** command will change your current working directory.

{% highlight unix %}
  ~$ cd bin/
  ~/bin$
{% endhighlight %}

You can move up one directory level by simple using the **cd..** command.

{% highlight unix %}
  ~$ cd bin/scripts
  ~/bin/scripts$ cd ..
  ~/bin$
{% endhighlight %}

The **cd -** command is the command-line equivalent of the back button which takes you to the previous directory you were in.

{% highlight unix %}
  ~/bin$ cd ~/lib/scripts
  ~/lib/scripts$ cd -
  ~/bin$
{% endhighlight %}

The **mv** or **move** command allows you to move files or directories.

{% highlight unix %}
  ~$ ls
  bin  bitbucket  home  logstasher.log  cronjob.sh  lib
  ~$ mv logstasher.log ~/bitbucket
  ~$ ls
  bin  bitbucket  home  cronjob.sh  lib
  ~$ cd bitbucket/
  ~/bitbucket$ ls
  logstasher.log
{% endhighlight %}

The **rm -rf** or **remove recursive force** command, where **-r** recursive deletion of a  directory and **-f** is "--force" which overrides some sanity checks and prompting, allows you to delete a directory with all of its subdirectories and files in it.

{% highlight unix %}
  ~$ rm -rf
{% endhighlight %}

The **df** or **dirsk free** command displays the amount of available disk space being used by file systems. It also reports the device name, total blocks, total disk space, used disk space, and mount points on a file system. Most common option for this command are:

*   **-T** display file system type.
*   **-t** display certain file system types only.
*   **-x** exclude certain file system types.

{% highlight unix %}
  ~$ df
  Filesystem     1K-blocks     Used Available Use% Mounted on
  udev             4079016        0   4079016   0% /dev
  tmpfs             817396    57960    759436   8% /run
  /dev/xvda1      50758760 14843180  35899196  30% /
  tmpfs            4086976        0   4086976   0% /dev/shm
{% endhighlight %}

The df command also provides an option to display files sizes in human readable formats by using the option **-h**.

{% highlight unix %}
  ~$ df -h
  Filesystem      Size  Used Avail Use% Mounted on
  udev            3.9G     0  3.9G   0% /dev
  tmpfs           799M   57M  742M   8% /run
  /dev/xvda1       49G   15G   35G  30% /
  tmpfs           3.9G     0  3.9G   0% /dev/shm
{% endhighlight %}

To get the total disk usage size of an directory use the option **-** as follows.

{% highlight unix %}
  ~$ du -hs ~/bin/
  485M	/home/root/bin/
{% endhighlight %}

The **tail** command is very useful while debugging. Instead of limiting by number of lines this will limit by the number of bytes passed to the **-c** option.

{% highlight unix %}
  ~$ tail -c 10 logstasher.log
{% endhighlight %}

It also allows you to "follow" or show e.g log file entries in real-time which simplifies the bug hunt. If you want to see and follow incoming requests, just add the **-f** option.

{% highlight unix %}
  ~$ tail -f logstasher.log
  log entry 1
  log entry 2
  log entry 3
{% endhighlight %}

The **mkdir** or **make directory** command allows you to create your folder structure.

{% highlight unix %}
  ~$ ls
  bin  bitbucket  home  cronjob.sh  lib
  ~$ mkdir test
  bin  bitbucket  home  cronjob.sh  lib  test
{% endhighlight %}

The **!!** command allows you to repeat your last command.

{% highlight unix %}
  ~$ $ echo foo bar baz
  foo bar baz
  $ !!
  foo bar baz
{% endhighlight %}

The most useful in the form: to run last command with your admin rights. This can be achieved by the sudo command which stands for "superuser do". It prompts you for your personal password.

{% highlight unix %}
  $ !!
  Permission denied
  $ sudo !!
  [sudo] password for root:
  foo bar baz
{% endhighlight %}

The **!$** command take the last word of the previous line.

{% highlight unix %}
  ~$ mkdir testdir;
  ~$ cd !$..
  ~/testdir$
{% endhighlight %}


## Valuable UNIX Commands

We can use the **mkdir** command also to create nested folders via command line.

{% highlight unix %}
  ~$ mkdir -p parent/{child1,child2,child3}
{% endhighlight %}

The **touch** command allows you to create files. You can also create files into one of the folders we've created one line above.

{% highlight unix %}
  ~$ touch !!:2/{file_1.txt,file_2.txt}
{% endhighlight %}

Another very helpful command is **'^string^string2'** which takes the last command, replaces *string* with *string2* and executes it.

{% highlight unix %}
  ~$ ehco foo bar baz
  No command 'ehco' found, did you mean:
   Command 'echo' from package 'coreutils' (main)
  ehco: command not found
  ~$ ^ehco^echo
  echo foo bar baz
  foo bar baz
{% endhighlight %}

The **awk** or short for **Aho, Weinberger, and Kernighan,** is an interpreted programming language which focuses on processing text.

The most common options are:

*   **-Ffs** Sets the input field separator to the regular expression fs.
*   **-v var=value** Assigns the value to the variable var before executing the awk program.
*   **-f progfile**  Specify progfile which contains the awk program to be executed.
*   **file...** A file to be processed by the specified awk program.

{% highlight unix %}
  ~$ awk [-Ffs] [-v var=value] [-f progfile] [file...]
{% endhighlight %}

The **grep** or **global regular expression print** command processes text line by line (file or standard input) and prints any lines which match a specified pattern. By default, grep prints the matching lines.

{% highlight unix %}
  ~$ history | grep cd
  1  cd bin
  2  cd bin/scripts
  3  cd lib/scripts
  ...
{% endhighlight %}


## Piping Commands

Lets combine some unix commands to get a list of all used commands sorted by the quantity of usage. We can use the command called pipe **l** that allows us to pass the output of one commands to another one.

{% highlight unix %}
  ~$ history | awk '{print $2}' | awk 'BEGIN {FS="|"}{print $1}' | sort | uniq -c | sort -nr | head
  184 ls
  103 cd
   86 sudo
   52 vi
   36 crontab
   33 exit
   24 tail
{% endhighlight %}


## Takeaway

Most of the above UNIX commands come with a variation of options. To master UNIX commands is a very important skill in the daily life of a Developer. o simplify shipping, debugging and maintaining your applications wandering in application infrastructures.
