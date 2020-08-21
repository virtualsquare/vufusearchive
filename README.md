# vufusearchive
vufuse module for libarchive: it supports tar, zip, iso (including compressed files)

Based on [archivemount](https://github.com/cybernoid/archivemount).

NB: this module has not been extensively tested yet. Use it at your own risks.

**archivemount uses global vars, it is not reentrant. mount only one
archive at a time**

## Esample:

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
