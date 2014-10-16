<!-- begin metadata
title: Unmount Busy Devices
date: 2013-07-26 17:37
end metadata -->

Trying to unmount a removable disk and getting 'device is busy'?
That happens because the device is actually busy! some process is using it.

Solution: Lazily unmount it with `-l` flag in `umount`. Lazy unmount removes
the filesystem from the fs hierarchy and when the device becomes free, it will
clean up all the references.

```
# umount -l /dev/sdb1
```

To see which processes are using the device,

```
# fuser -m /dev/sdb1
/dev/sdb1:  3717c 4214e
```

3717 and 4214 are the pid of the process keeping the device busy. What are the processes doing you get to know from the letter following the pid.
manpage for fuser lists:

```
     c      current directory.
     e      executable being run.
     f      open file.  f is omitted in default display mode.
     F      open file for writing.  F is omitted in default display mode.
     r      root directory.
     m      mmap'ed file or shared library.
```

That gives you an idea. Do,

```ps -eo pid,command | egrep "3717|4214"```

to get the processes, kill them if you like with. `fuser` can also send signals to these processes. 

```fuser -km /dev/sdb1``` 

kills the processes using the device. 
