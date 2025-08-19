# 1. File Permissions

Every file/directory in Linux has **permissions** that define who can read, write, or execute it.  

---

## 🔹 Example
```bash
$ ls -l Desktop/
drwxr-xr-x 2 varun Avya 4096 Dec  1 11:45 .
```
**Breakdown:**
- `d` → File type (here: directory).
  - `-`= regular file
  - `d` = directory
  - `l` = symlink
- `rwx` → User (owner) permissions → read, write, execute
- `r-x` → Group permissions → read, execute
- `r-x` → Other (everyone else) permissions → read, execute

So here:
- User (varun) → rwx
- Group (Avya) → r-x
- Others → r-x

**Permission Symbols**
- `r` → read
- `w` → write
- `x` → execute
- `-`→ no permission

---
# 2. Modifying Permissions
Changing permissions can easily be done with the chmod command.

First, pick which permission set you want to change: user, group, or other. You can add or remove permissions with a `+` or `-`. Let's look at some examples.
## Adding permission bit on a file
```bash
chmod u+x myfile
```
The above command reads like this: change permission on myfile by adding the executable permission bit to the user set.

## Removing permission bit on a file
```bash
chmod u-x myfile
```

## Adding multiple permission bits on a file
```bash
chmod ug+w
```

## Numerical (Octal) Mode
There is another way to change permissions using numerical format. This method allows you to change permissions all at once
Example
```bash
chmod 755 myfile
```
Breakdown:
- `7` → rwx (user)
- `5` → r-x (group)
- `5` → r-x (others)

So user can read, write, execute; group + others can read, execute.

---
# 3. Ownership Permissions
In addition to modifying permissions on files, you can also modify the group and user ownership of the file.

### Modify user ownership
```bash
sudo chown Avya myFile
```
This command will set the owner of `myFile` to `Avya`.
### Modify group ownership
```bash
sudo chgrp whales myfile
```
This command will set the group of `myFile` to `whales`.

### Modify both user and group ownership at the same time
If you add a colon and group name after the user, you can set both the user and group at the same time.
```bash
sudo chown Avya:whales myfile
```

---

# 4. Umask
Nice one, Varun — let’s capture **umask** into quick notes the same way we did for `chmod`.

---

# 🛡️ Umask Notes

### 1. What is **umask**?

* Sets the **default permissions** for newly created files/directories.
* Works in **reverse** → it **removes** permissions from the base default.
* New files: **666 = rw-rw-rw-** (no execute by default)
* New directories: **777 = rwxrwxrwx**


### 2. How it Works

`umask` subtracts permissions:

```bash
umask 021
```

* User: full (default stays same, nothing removed).
* Group: remove **w** (write).
* Others: remove **x** (execute).

So new files → `rw-r--rw-` style (depends on base).

**Defaults**

* **022** → User full; Group & Others: no write.
* **002** → User full; Group: read + write; Others: read only.


When you run the umask command, it will apply that default set of permissions to any new file you create. However, if you want it to persist, you'll have to modify your startup file (`.profile`).

---
# 5. Setuid
There are many cases in which normal users need elevated access to do stuff. The system administrator can't always be there to enter a root password every time a user needs access to a protected file, so there are special file permission bits to allow this behavior. The Set User ID (SUID) allows a user to run a program as the owner of the program file rather than as themselves.
Let's look at another permission set, this time of the command we ran:
- Changing password → needs to modify /etc/shadow.
- `/etc/shadow` is owned by root, normal users can’t write to it.
- But the `passwd` program has SUID set:
```bash
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 47032 Dec 1 11:45 /usr/bin/passwd
```
- The `s` in `-rws` means SUID enabled for user.
- When user runs `passwd`, it runs with root’s permissions → allows editing `/etc/shadow`.
### Modifying SUID
*Symbolic way*:
```bash
sudo chmod u+s myfile
```
*Numerical way*:
```bash
sudo chmod 4755 myfile
```
**As you can see, the SUID is denoted by a 4 and prepended to the permission set**. You may see the SUID denoted as a capital S. This means that it still does the same thing, but it does not have execute permissions.


---
# 6. Setgid
Similar to the set user ID permission bit, there is a set group ID (SGID) permission bit. This bit allows a program to run as if it were a member of that group.

Let's look at one example:

```bash
$ ls -l /usr/bin/wall
-rwxr-sr-x 1 root tty 19024 Dec 14 11:45 /usr/bin/wall
```

### Modifying SGID
```bash
sudo chmod g+s myfile
sudo chmod 2555 myfile
```
The numerical representation for SGID is 2.

---

Got it Varun 👍 Let’s make this **super clean notes style** so you can drop it into GitHub and revise later.

---

# 7. Process Permissions

### Key Idea

Every process in Linux has **3 types of User IDs (UIDs)** that determine what it can do.

### 1. **Real UID (RUID)**

* The ID of the user who launched the process.
* Used for **accountability/tracking** (who started it).


### 2. **Effective UID (EUID)**

* The ID the process uses to **decide permissions** (what files it can access).
* Normally = the user’s UID.
* If a file has **SUID bit set** → process runs with the file owner’s UID (often root).

### 3. **Saved UID (SUID)**

* Stores the **previous EUID**, so the process can switch back and forth.
* Useful for programs that need temporary privilege escalation (e.g., `passwd`).



Perfect question, Varun — examples make this stick.
Let’s role-play a scenario step by step:

---

### Example: Running `passwd`

👤 **You (Varun)** → UID = 500
👤 **Sally** → UID = 600
👤 **root** → UID = 0


##### Step 1: You run `passwd`

* Normally, processes run with **your UID**.
* So: **RUID = 500**, **EUID = 500**.


##### Step 2: But `passwd` has **SUID bit set** (owned by root)

* That means it runs with **root’s UID** as the **EUID**.
* Now:

  * **RUID = 500** (still you, for tracking)
  * **EUID = 0** (root’s power)
  * **SUID = 0** (saved so it can drop/reuse root privileges)


##### Step 3: What happens inside?

* Since **EUID = 0**, `passwd` can open `/etc/shadow` (only root can).
* It uses **RUID = 500** to check *which account to modify*.
* So you can only change **your own password**, not Sally’s.


##### Step 4: Safety

* Program can temporarily switch back from **EUID=0** to **RUID=500** when root access isn’t needed (thanks to Saved UID).
* This avoids leaving root power on all the time.

---
# 8. The Sticky Bit
This permission bit "sticks a file/directory," meaning that only the owner or the root user can delete or modify the file. This is very useful for shared directories. Take a look at the example below:
```bash
$ ls -ld /tmp
drwxrwxrwx+t 6 root root 4096 Dec 15 11:45 /tmp
```
You'll see a special permission bit at the end here `t`. This means everyone can add files, write files, and modify files in the `/tmp` directory, but only root can delete the `/tmp` directory.
#### Modify sticky bit
```bash
sudo chmod +t mydir
sudo chmod 1755 mydir
```
The numerical representation for the sticky bit is 1.
