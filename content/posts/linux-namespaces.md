---
title: "Into a containers 1: Linux namespaces"
date: 2021-09-2T12:00:00+02:00
draft: true
---

# Linux namespaces

## Why this article?

I use docker every day for the last five years. The Docker CLI, Dockerfile, docker-compose are the keywords I am familiar with, but what is inside? 

This article is more for me, to learn new things. During the series of articles, I will answer the following questions:

- What is inside of the docker?
- How processes and containers are living in the operating system?
- How to implement your containerization engine?


## What is linux namespace

> A namespace wraps a global system resource in an abstraction that makes it appear to the processes within the namespace that they have their isolated instance of the global resource.  Changes to the global resource are visible to other processes that are members of the namespace but are invisible to other processes. One use of namespaces is to implement containers.

ref: https://man7.org/linux/man-pages/man7/namespaces.7.html


Linux system has the following kind of namespaces:

- [PID](https://man7.org/linux/man-pages/man7/pid_namespaces.7.html) - isolates process IDs
- [Mount](https://man7.org/linux/man-pages/man7/mount_namespaces.7.html) - isolates mount points
- [Cgroup](https://man7.org/linux/man-pages/man7/cgroup_namespaces.7.html) - isolates resources like cpu, memory 
- [IPC](https://man7.org/linux/man-pages/man7/ipc_namespaces.7.html) - isolates System V IPC and POSIX message queues
- [Network](https://man7.org/linux/man-pages/man7/network_namespaces.7.html) - isolates network devices, stacks, ports, etc.
- [Time](https://man7.org/linux/man-pages/man7/time_namespaces.7.html) - isolates boot and monotonic clocks
- [User](https://man7.org/linux/man-pages/man7/user_namespaces.7.html) - isolates user and group IDs
- [UTS](https://man7.org/linux/man-pages/man7/uts_namespaces.7.html) - isolates hostname and NIS domain name

Namespaces are driven by the files under the `/proc` path. In the `/proc` directory, You can find one folder for all processes running in the operating system. Details about namespaces are in the `ns` directory for all running programs. 

For example, We can find the namespace details for your current shell(`$$`):

```bash
daniel1302@smaug:~$ ls -l /proc/$$/ns
total 0
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 mnt -> 'mnt:[4026531840]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 net -> 'net:[4026531992]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 pid -> 'pid:[4026531836]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 time -> 'time:[4026531834]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 time_for_children -> 'time:[4026531834]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 user -> 'user:[4026531837]'
lrwxrwxrwx 1 daniel1302 users 0 Aug 15 21:44 uts -> 'uts:[4026531838]'
```

You cannot create unlimited namespaces in your system. You can see limits set in the `/proc/sys/user` folder. 

#### Excercise

Write program or script checking if two processes belong to the same namespace.
  