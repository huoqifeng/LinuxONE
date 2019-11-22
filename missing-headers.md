# How to solve header file missing when build a s390x binary

Actually this comes from a very good articale: https://portal2portal.blogspot.com/2019/06/its-been-while-c-and-case-of-missing.html
But this site is not accessible always in CN, you know...

So, I met same problem and solved it when build hyperkube,  so I'll write it down here for refereence:



 ## It's been a while - C++ and the case of the missing headers

Whilst trying to compile some code on my Linux box which, of course, is an IBM mainframe running Ubuntu, I was seeing this: 

```
/usr/include/features.h:424:12: fatal error: sys/cdefs.h: No such file or directory
 #  include
            ^~~~~~~~~~~~~
compilation terminated.
```

Checked that libc installed: -

```
sudo apt-get install libc6-dev
sudo apt-get install libc6
```

I did check the missing file: -
```
sudo find / -name cdefs.h
```
which returned: -
```
/usr/include/sys/cdefs.h
```
```
ls -al /usr/include/sys/cdefs.h

lrwxrwxrwx 1 root root 30 Apr 16  2018 /usr/include/sys/cdefs.h -> ../s390x-linux-gnu/sys/cdefs.h
```

which gave me a clue ...

After some digging around, I found this: -

How do I find the package that provides a file?

on AskUbuntu, which referenced the apt-file command: -

```
sudo apt-get install apt-file
sudo apt-file update
```

Having installed it, I ran it: -

```
apt-file find cdefs.h|grep s390
```

which showed: -

```
libc6-dev: /usr/include/s390x-linux-gnu/sys/cdefs.h
libc6-dev-s390: /usr/include/sys/cdefs.h
libc6-dev-s390x-cross: /usr/s390x-linux-gnu/include/sys/cdefs.h
```

Taking a leap o' faith, I installed the s390 element of libc6-dev: -

```
sudo apt-get install libc6-dev-s390
```

but to no avail.

I then did the same for the s390x-linux-gnu element: -

```
sudo apt-get install libc6-dev-s390x-cross
```

which did the job.


Thanks "Dave Hay" who provided the great reference!