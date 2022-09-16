---
layout: post
title:  "Files and Directories"
date:   2022-03-03 10:42:00
categories: [Linux]
comments: true
author: vishnuchegondi
tags: [Storage]
---

Files are the most fundamental abstraction of Linux. In Linux <i>everything-is-a-file</i>.

<!--more-->

## Regular Files

The most common files we interact daily to store data are labeled as regualar files in linux. Regular file contains series of bytes of data stored in linear array called byte stream. In linux, no other structure is implemented on the regular files apart from byte stream. 

### Information Node(inode)
 inode stores the metadata related to the particular file(byte stream) such as starting position of the file, size, location and permissions. Whenever we open a file it is identified by <b>unique descriptor(file descriptor)</b> a mapping from inode to file itself and operations starts from initial byte location called <b>file position(fp)</b>. Whenever we read from file fp will be increased from byte-to-byte.

![Inode Tables](assets/img/inode_tables.png)

### HardLinks

 A **link** is a mapping between filename and inode. As of now, we have discussed nothing like linking multiple filenames to same inode. Hardlinks are different filenames mapped to a single inode. These filenames(along with paths) can be in different directories. Deleting one filename in path deletes the link associated with that inode but not the inode and its byte stream data. So, everytime while a hardlink is deleted link count will be decreased in inode when link count is zero inode is deleted but still the byte array exists. This byte array can used to recover the data that is accidently deleted reverting back of the inode states using filesystems.

### SoftLinks or Symbolic Links

Hardlinks can not be created across filesystems as inode numbers are unidentified outside filesystem. On the contradictory to refer file outside of filesystem we use symlinks(symbolic links) which looks like a regular file but it has its own inode which contains path name of the linked-to file.

<b><i>Identicals</i></b>

- In both, symlinks and hardlinks changing one file changes the contents of another file as they are internally refering to same location in storage.

<b><i>Differences</i></b>

- Unlike Hardlink if we delete a main file the symbolic link is invalid as it will be pointing to the mainfile not the inode of the file.
- Unlike Hardlink symlinks can be used across filesystems as symlinks actually points to the file-path which is valid across the Operating system but inode numbers are not valid across filesystems.


### 32 bit file size limitations

Let us consider a 32 bit CPU where register size is 32 bits. Maximum of 2^32 address locations can be represented using this register with one location to one byte.

``` sh
2 power 2 = 4

1 kb = 1024 bytes (2 power 10 bytes)
1 MB =1024 kb (2 power 20 bytes)
1 GB = 1024 MB (2 power 30 bytes)
```
So, with 32 bit CPU we can process maximum file of size 4 GB during a process. 

> FAT32 file system supports maximum of 4 GB file size.


## Directories

Accessing files using inode number is difficult and not a good idea as we should be accessing them across filesystems. So we usually use names along with paths to access the files. To provide the paths we use directories mappings of inodes and the filenames called ***links***.  The physical on-disk form of mapping is actually defined by filesystem and managed by kernel. When a user requests a filename to be opened, the kernel uses the mappings and get the inode number and returns the file descriptor.
Althought directories are like regular files kernel does not allow them to manipulate as regular files. We have different set of system calls to manipulate them. Directory holds different links to interal directory inodes or file inodes.

![directory structure](assets/img/directory_file.png)

#### Reaching nested directories

In Linux, there will be only one directory on the disk which is root directory(/). Directories are like regularfiles and also will be associated inodes to it. Consequently links in directory point to another inodes of directories forming a tree of directories structure. This way allows users to use path names example - /home/user1/test.txt

<b><i>Absolute Path:</i></b> where the path starts nfrom root directory and points to nested directories, Absolute paths always start with rootpath(/). example - /home/user1/absolute_path.txt

<b><i>Relative Path:</i></b> Where the path search starts from current directory. example -  assuming curent directory is /home/user1 relative path can be absolute_path.txt

#### Mounting

Adding another file system as a directory to the root directory is called mounting. There can be multiple mounts but while booting we will have a default filesystem mounted to root path(/).