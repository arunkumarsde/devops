### User Permission Commands

In Linux, Everything is a file. Because Linux was built as a multi-user system from day one, it uses a strict permission model to ensure users can't accidentally (or maliciously) modify system files or access each other's data.

```
-rwxr-xr-- 1 user group 4096 Jan 01 12:00 myfile.txt
```

1st char -> '-' -> file_type (- = Regular File, d = Directory, l = Symbolic Link.)
rwx -> User(u) permissions
r-x -> Group(g) permissions
r-- -> Others(o) permissions

## chomod command to change permissions

```
chmod u-x,g=rw,o-r -> add x permission to user, set rw permission to group, remove r permission from others
```

Best for setting absolute permissions quickly. Each permission adds a value to a total score.

- 4 = Read (r)
- 2 = Write (w)
- 1 = Execute (x)
- 0 = No Permission

- 777 -> All permissions to everyone
- 755 -> Owner can read/edit/execute, everyone can read and run
- 644 -> Owener can read/edit, everyone can read
- 600 -> Only owner can read/edit
- 400 -> Only owner can read

## chown command to change owner

```
sudo chown [new_owner]:[new_group] filename
```

_Note: You almost always need sudo to give files away._

## chgrp command to change group

```
chgrp dev-team project.c
```

_Note: No sudo needed if you are a member of dev-team and own the file._

## Special Permissions (SUID, SGID, Sticky Bit)

To be a true expert, you must know the "4th digit" in permissions (e.g., chmod 4755).

| Type   | Bit | Symbol     | Functions                                                                                                                                                                       |
| ------ | --- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SUID   | 4   | s (User)   | Run as Owner. When executed, the program runs with the file owner's permissions, not the user's. (e.g., passwd command needs root rights to update passwords).                  |
| SGID   | 2   | s (Group)  | Inherit Group. Used on directories. New files created inside will inherit the directory's group, not the creator's primary group. Essential for shared team folders.            |
| Sticky | 1   | t (Others) | Restricted Deletion. Used on shared folders (like /tmp). Users can create files, but only the owner can delete their own files. Prevents users from deleting each other's work. |

# User CRUD Command

Managing users is the foundation of Linux system security. It is not just about typing commands; it is about managing the entries in the system's "database" files: /etc/passwd and /etc/shadow.

### Where Users Live

Before running commands, understand where the data goes. Linux stores user data in plain text files.

- /etc/passwd: Stores public user info (Name, ID, Home Directory, Shell).
- /etc/shadow: Stores encrypted passwords and expiration data.
- /etc/group: Stores group memberships.

**Anatomy of a User Entry (/etc/passwd):**

```
john:x:1001:1001:John Doe:/home/john:/bin/bash
```

- john: Username
- x : Password placeholder (stored in shadow)
- 1001 : User ID (UID)
- 1001 : Group ID (GID)
- /home/john : Home Directory
- /bin/bash : Default Shell

1. Creating Users (adduser or useradd):

```
sudo adduser new_username

or

sudo useradd new_username

or

sudo su
addiser new_username
```

2. Reading User Info

```
finger username

or

id username
```

3. Updating User info

```
sudo usermod -d /new/home/directory username
```

4. Deleting User

```
sudo userdel username

or

sudo deluser username

sudo userdel -r username -> Removes home directory as well
```

5. Adding users to group

```
sudo usermod -aG groupname username

or

sudo gpasswd -a username groupname
```

6. Change userpassword

```
sudo passwd username
```
