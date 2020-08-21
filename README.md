# vufusearchive
vufuse module for libarchive: it supports tar, zip, iso (including compressed files)

Based on [archivemount](https://github.com/cybernoid/archivemount).

NB: this module has not been extensively tested yet. Use it at your own risks.

**archivemount uses global vars, it is not reentrant. mount only one
archive at a time**

## Download, Compile and install:

This project uses a submodule (archivemount) use the following `git clone` command:
```
git clone --recurse-submodules  https://github.com/virtualsquare/vufusearchive.git
```

It uses `cmake`, so the following sequence of commands can be used to compile and install
`vufusearchive`:
```
mkdir build
cd build
cmake ..
make
sudo make install
```

An uninstall procedure is also available:
```
sudo make uninstall
```

## Example:

In a VUOS session, add the `vufuse module`
```
vu_insmod vufuse
```

and then mount your archive. in the following example the file `/tmp/myfile.tgz`
on the mountpoint `/tmp/mnt`.
```
vumount -t vufusearchive /tmp/myfile.tgz /tmp/mnt
```

Use `vuumount /tmp/mnt` to unmount the archive.
