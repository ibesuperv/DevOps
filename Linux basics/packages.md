# 1. Software Distribution

* **Packages:** Collections of compiled files for software (e.g., Chrome, media players, text editors).
* **Package Managers:** Tools that install and maintain software on your system.
* **Package Types:**

  * **Debian (.deb):** Used in Debian, Ubuntu, Linux Mint.
  * **Red Hat (.rpm):** Used in Red Hat, Fedora, CentOS.
* **Process:**

  1. **Upstream providers** create and compile software.
  2. **Package maintainers** review and distribute the compiled software to users.

---

# 2. Package Repositories

* **Definition:** Central storage locations on the internet for packages.
* **Purpose:** Avoid manually downloading each package; let your system fetch them automatically.
* **Example:**

  * Docker has its own repository: `https://download.docker.com/linux/ubuntu`
  * Contains packages like `docker-ce`, `docker-ce-cli`, `containerd.io`.
* **Distribution Repositories:**

  * Your Linux distro comes with pre-approved repositories.
  * On Debian/Ubuntu, these are listed in `/etc/apt/sources.list`.
  * Your system checks these sources to install or update packages.

---

# 3. `tar and gzip`
You probably already know what a file archive is; you've most likely encountered file types such as .rar and .zip. These are archives of files; they contain many files inside of them, but they come in this very neat single file known as an archive.

## Compressing files with gzip
gzip is a program used to compress files in Linux; they end in a .gz extension. Compress single files.

To compress a file:

```bash
gzip mycoolfile
```
To decompress the file:

```bash
gunzip mycoolfile.gz
```
## Creating archives with tar
gzip can't add multiple files into one archive for us. we have the tar program which does. it will have a `.tar` extension
```bash
$ tar cvf mytarfile.tar mycoolfile1 mycoolfile2
```
- c - create
- v - tell the program to be verbose and let us see what it's doing
   - Without v → tar creates the archive silently. You won’t see any files listed.

   - With v → tar prints each file it’s adding to the archive:
   ```nginx
    file1
    file2
  ```

- f - the filename of the tar file has to come after this option; if you are creating a tar file, you'll have to come up with a name

## Unpacking archives with tar
To extract the contents of a tar file, use
```bash
$ tar xvf mytarfile.tar
```
x - extract
v - verbose output
f - the file you want to extract

## Compressing/uncompressing archives with tar and gzip
Create a compressed tar file:
```bash
$ tar czf myfile.tar.gz #This zipping existing tar.
or
$ tar czf myfile.tar.gz file1 file2
```


Uncompress and unpack:
```bash
tar xzf file.tar
```
---

# 4. Package Dependencies
Packages rarely work alone; they often need other packages or shared libraries to function.
- Linux Context: Dependencies can be:
- Other packages: Software that must be installed first.
- Shared libraries: Pre-written code used by multiple programs.

----
# 5. `rpm` and `dpkg`

Low-level package managers used to install/remove .deb (Debian) or .rpm (Red Hat) files directly
**Limitations:**
- They do not resolve dependencies automatically.
- If a package requires 10 other packages, you must install them manually.
- This limitation is what led to full package management systems like apt or yum/dnf.

**Install a package**
```bash
# Debian
dpkg -i some_package.deb  

# Red Hat
rpm -i some_package.rpm  
```

**Remove a package**
```bash
# Debian
dpkg -r some_package  

# Red Hat
rpm -e some_package  
```
- `r` = remove(Debian)
- `e` = erase (Red Hat)

**List installed packages**
```bash
# Debian
dpkg -l  

# Red Hat
rpm -qa  
```
- `l` = list (Debian)
- `q` = query, `a` = all (Red Hat)


---
# 6. `yum` and `apt`
yum and apt can:
- Fetch packages from online repositories

- Install dependencies automatically

- Update/upgrade multiple packages at once

- Search and show info about packages

**Install a package from a repository**
```bash
Debian: $ apt install package_name
RPM: $ yum install package_name
```

**Remove a package**
```bash
Debian: $ apt remove package_name
RPM: $ yum erase package_name
```

**Updating packages for a repository**
```bash
Debian: apt update; apt upgrade
RPM: yum update
```
**Get information about an installed package**
```bash
Debian: apt show package_name
RPM: yum info package_name
```

---

# 7. Compile Source Code
Sometimes you’ll need to install software that only comes as **source code**.  
This usually involves compiling it yourself.



### Step 1: Install build tools

```bash
sudo apt install build-essential
```
This installs compilers (`gcc`, `g++`, `make`) and other essential tools.
### Step 2: Extract the package
Most source code packages come as .tar.gz files:
```bash
tar -xzvf package.tar.gz
cd package/
```

### Step 3: Read documentation
Inside the package, check:
- `README`
- `INSTALL`
These may contain special instructions.

### Step 4: Configure

Run the configure script (if present):
```bash
./configure
```
- Checks system dependencies.
- If something is missing, fix it before continuing.

### Step 5: Build with make
```bash
make
```
- Looks at the Makefile.
- Compiles the source code into binaries.

### Step 6: Install
```bash
sudo make install
```
- Copies the compiled binaries to system directories.
- Hard to uninstall later (not tracked by package manager).

To uninstall (if supported):
```bash
sudo make uninstall
```
