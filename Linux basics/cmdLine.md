

---

# 1. The Shell

The **shell** is a program that takes commands from the keyboard and sends them to the operating system.
Common programs like **Terminal** or **Console** launch a shell.


## Shell Prompt Format

```
username@hostname:current_directory
```

Example:

```
varun@saṅgaṇaka:/home/varun $
```

* `$` → normal user
* `#` → root user



## Command: `echo`

The `echo` command prints text to the screen.

```bash
echo Hello World
```

---

# 2. `pwd` (Print Working Directory)

The Linux filesystem is organized in a **hierarchical directory tree**.
The top directory is the root directory `/`, which contains folders and files.

### Example structure:

```
/
|-- bin
|   |-- file1
|   |-- file2
|-- etc
|   |-- file3
|   `-- directory1
|       |-- file4
|       `-- file5
|-- home
|-- var
```

A **path** shows the location of a file or directory.
Example:

```
/home/varun/Movies
```


## Command: `pwd`

The `pwd` command prints the current directory path.

```bash
varun@saṅgaṇaka:/home/varun $ pwd
/home/varun
```

---


# 3. `cd` (Change Directory)

The `cd` command is used to change your current working directory in the filesystem.


## 📂 Paths in Linux

There are two types of paths you can use with `cd`:

### 1. Absolute Path
- Starts from the **root directory** `/`.
- Always specifies the **full path**.
- Example:
  ```bash
  varun@saṅgaṇaka:/home/varun $ cd /home/varun/Desktop
  ```
  
### 2. Relative Path

- Starts from your current working directory.

- Shorter and easier when already inside a related directory.

- Example:  
    If you are in `/home/varun/Documents` and want to go to `/home/varun/Documents/taxes`, you can simply run:
  ```bash
    varun@saṅgaṇaka:/home/varun/Documents $ cd taxes
  ```
  Other Important Shortcut

| Shortcut | Meaning                         | Example                                         |
| -------- | ------------------------------- | ----------------------------------------------- |
| `.`      | Current directory               | `varun@saṅgaṇaka:/home/varun $ cd .`            |
| `..`     | Parent directory (one level up) | `varun@saṅgaṇaka:/home/varun/Documents $ cd ..` |
| `~`      | Home directory (`/home/varun`)  | `varun@saṅgaṇaka:/home/varun/Documents $ cd ~`  |
| `-`      | Previous directory              | `varun@saṅgaṇaka:/home/varun/Desktop $ cd -`    |



