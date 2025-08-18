

---

# 1. The Shell

The **shell** is a program that takes commands from the keyboard and sends them to the operating system.
Common programs like **Terminal** or **Console** launch a shell.

---

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

---

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

---

## Command: `pwd`

The `pwd` command prints the current directory path.

```bash
varun@saṅgaṇaka:/home/varun $ pwd
/home/varun
```

---

