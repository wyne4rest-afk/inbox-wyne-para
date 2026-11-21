---
title: LFS Progress Log
tags:
  - resource
  - linux
  - lfs
---

Track each session here: date, chapter, what worked, what broke.

## Status

- [ ] Host system requirements checked
- [ ] Target partition prepared
- [ ] Sources downloaded and verified
- [ ] Cross toolchain built
- [ ] Temporary tools built
- [ ] Base system built in chroot
- [ ] System configured
- [ ] Kernel and bootloader installed

## I have read these on 2026-11-21

Source: [Software Building HOWTO](https://tldp.org/HOWTO/Software-Building-HOWTO.html) by Mendel Cooper (v1.91, 27 July 1999). Sections 1 to 3 of 17.

The package names below (`foo-1.0`, `mytool`) are made-up examples, not real software.

### The big picture

Much Linux software is shared as **source code** in one compressed file. You turn it into a working program in three steps:

1. **Unpack** the file (section 2)
2. **Build** it with `make` (section 3)
3. **Install** it so you can run it (section 3)

### 1. Introduction

- **What it says:** many programs come as source code in a compressed archive, and you have to build them before you can run them.
- **Why:** one source package can be built for many kinds of computers, so the author doesn't have to publish a separate version for each.
- **What it means for you:** you are the "de facto system administrator," so building and installing is your job.
- **Good news:** it is easier than it sounds, and the rest of the guide shows how.

### 2. Unpacking the Files

A compressed source file is usually called a **tarball**. Its name ends in `.tar.gz` or `.tgz`.

**Look inside before unpacking**

```
tar tzvf foo-1.0.tar.gz
```

The `t` means "list". Nothing is extracted.

**Unpack it**

```
mkdir ~/build
cp foo-1.0.tar.gz ~/build
cd ~/build
tar xzvf foo-1.0.tar.gz
```

The `x` means "extract". You now have a `foo-1.0/` folder.

**Other file types**

| File ends in | How to unpack it |
|---|---|
| `.tar.gz`, `.tgz` | `tar xzvf file` |
| `.Z` | `uncompress file.tar.Z`, then `tar xvf file.tar` |
| `.bz2` | see the `bzip2` command below the table |
| `.shar` | `unshar file.shar` |
| `.zip`, `.rar`, `.arj` | use a separate tool for that format (made on DOS, Mac or Amiga) |

For a `.bz2` file, pipe `bzip2` into `tar`:

```
bzip2 -dc file.tar.bz2 | tar xvf -
```

**Some things to remember**

- Read the `README` or `INSTALL` file first. Some packages must be unpacked in a particular place, such as `/usr/src` or `/opt`.
- Don't edit the `Imake` file. Edit the config files or Makefiles instead.
- **Patches:** an update or bug fix often comes as a small patch file. Apply it from inside the source folder:

```
cd foo-1.0
patch < ../foo-1.0.patch
```

### 3. Using Make

The **Makefile** is a recipe that tells `make` how to compile the program. `make` can also install it and clean up afterwards.

**Preview first.** `-n` prints the commands without running any of them:

```
make -n
```

**Two ways the Makefile gets made**

1. **Imake (X11 programs):** the package has an `Imakefile`, which is a template. Generate the real Makefile with:

   ```
   xmkmf -a
   ```

2. **`configure` script:** the package has a script that checks your system and writes the Makefile:

   ```
   ./configure
   ```

   Open the generated Makefile and check it. You may need to tweak it by hand.

If neither is mentioned, the `README` or `INSTALL` file will say what to do.

**Build as a normal user, install as root**

```
make            # build, as a normal user
su              # become root
make install    # copy files into system folders
```

**Where it gets installed**

- `/usr/local/bin` is the preferred place for new programs.
- Programs written for commercial UNIX may expect `/opt`. Either create `/opt` and add it to your `PATH`, or make a symlink:

  ```
  ln -s /opt/mytool/bin/mytool /usr/local/bin/mytool
  ```

**Optional extras**

- **Optimization:** add gcc flags such as `-O2` for a faster program. Use with caution.
- **`strip`:** removes debugging information so the file is smaller.

  ```
  strip mytool
  ```

### Whole process in one example

```
cd ~/build
tar xzvf foo-1.0.tar.gz     # 1. unpack
cd foo-1.0
less README                 # 2. read the instructions
./configure                 # 3. make the Makefile
make -n | head              # 4. preview (optional)
make                        # 5. build, as a normal user
su
make install                # 6. install, as root
```

### Quick recap

| Step | Command | Run as |
|---|---|---|
| Look inside | `tar tzvf file.tar.gz` | normal user |
| Unpack | `tar xzvf file.tar.gz` | normal user |
| Apply a patch | `patch < file.patch` | normal user |
| Make the Makefile | `xmkmf -a` or `./configure` | normal user |
| Build | `make` | normal user |
| Install | `make install` | root |

Back to [[3-resources/linux-from-scratch/index|Linux From Scratch]].
