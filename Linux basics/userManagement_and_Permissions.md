# 1. Users and Groups


* **Users** → Every human or service (like a daemon) on the system is a user.
* **UID (User ID)** → The system doesn’t actually care about “Jane” or “Bob,” it cares about numbers like 1001 or 1002. The username is just a label for humans.
* **Groups** → Collections of users. They make permission management easier. Instead of giving each user access individually, you give access to the group.
* **root** → The boss of the system. Unlimited powers, but also dangerous (because one bad command can wreck the system).

Example

* `/etc/shadow` contains **hashed passwords** for all users. Super sensitive.
* That’s why only `root` and the `shadow` group can read it.
* When you ran `cat /etc/shadow` as a normal user, you got **permission denied**.
* Using `sudo` basically says: *“Run this as root instead of me.”* That’s why it worked the second time.

---
# 2. root
1. `su`(substitute user):
- Opens a new shell as nother user
- If no username is given, it defaults to roots.
- Requires the root password.
-Downsides: everything you type in that shell runs as root (more risk), and no record is kept of which user ran which command.

2. `sudo`:
- Lets you run just one command as root.

- Requires your password (if you’re allowed in /etc/sudoers).
- Keeps a log of what you did (/var/log/auth.log).
- Safer and more controlled.

---
# 3. /etc/passwd
The system uses a user ID (UID) to identify a user. To find out what users are mapped to what ID, look at the /etc/passwd file.

```bash
cat /etc/passwd
```
This file shows you a list of users and detailed information about them. For example, the first line in this file most likely looks like this:
```bash
root:x:0:0:root:/root:/bin/bash
```
**Fields**
```bash
username:password_placeholder:UID:GID:GECOS:home_directory:shell
```
1. username → login name (root, varun, etc.)

2. password_placeholder

- `x` → password is stored in /etc/shadow

- `*` → no login access

- blank → no password set

3. UID → user ID (unique number)

- `0` → root

- `1–999` → system/service accounts

- `1000+` → normal human users

4. GID → group ID (points to /etc/group)

5. GECOS → comment field (full name, phone, etc.)

6. home_directory → user’s home folder

7. shell → login shell (e.g., /bin/bash, /bin/sh, /usr/sbin/nologin)

---
# 4. `/etc/shadow`
The `/etc/shadow` file stores **user authentication information**.  
It requires **superuser (root) read permissions**.

```bash
$ sudo cat /etc/shadow
```
Example
```bash
root:MyEPTEa$6Nonsense:15000:0:99999:7:::
```
**fields**
Each entry is separated by : (colon):
1. Username – login name of the account
2. Encrypted password – hashed password of the user
3. Last password change – number of days since Jan 1, 1970
   - 0 → user must change password on next login
4. Minimum password age – days before a user can change password again
5. Maximum password age – maximum days before password must be changed
6. Password warning period – days before password expiration that user is warned
7. Password inactivity period – days after password expiration before login is disabled
8. Account expiration date – day when account becomes disabled
9. Reserved field – for future use

- `/etc/shadow` is more secure than `/etc/passwd` because it's not world-readable.
- Modern Linux systems often use PAM (Pluggable Authentication Modules) for authentication, not just `/etc/shadow`.

---
# 5. `/etc/group`

The `/etc/group` file is used for **user group management**.  
It defines groups and which users belong to them.
Very similar to the /etc/passwd file,.
```bash
$ cat /etc/group
```
Example
```bash
root:*:0:varun
```
**Fields**
Each entry is separated by : (colon):
1. Group name – name of the group
2. Group password – usually * by default
   - Group passwords are rarely used; elevated privileges are handled via sudo.

3. Group ID (GID) – unique numeric identifier for the group

4. List of users – comma-separated list of users in the group

---
# 6. User Management Tools
Most enterprise environments use management systems to manage users, accounts, and passwords. However, on a single machine computer, there are useful commands to run to manage users.

**Adding Users**
```bash
sudo useradd bob
```
- `seradd` → low-level, minimal setup
- `adduser` → higher-level, friendlier (creates home dir, sets defaults, etc.)
This creates:
An entry in `/etc/passwd`
Default group assignments
- An entry in `/etc/shadow`


** Removing Users**
```bash
$ sudo userdel bob
```
- Removes user account info
- Tries to undo changes made by useradd
- May not remove the home directory unless you specify `-r`

**Changing Password**
```bash
$ passwd varun
```
- Changes the password for yourself or another user
- Requires root privileges to change someone else’s password

---
