#### Notes

This repository contains a patched version of `PyInstaller Extractor 1.9`.

It contains a hack to make reversing of the main script(s) (`entry points`)
easier.

We would like to land this patch upstream (https://sourceforge.net/projects/pyinstallerextractor/).


#### Demo

Build a sample binary,

```
$ docker run -v "$(pwd):/src/" cdrx/pyinstaller-windows
```

Extract with the original program,

```
$ python3 pyinstxtractor-original.py dist/windows/hello/hello.exe
[*] Processing dist/windows/hello/hello.exe
[*] Pyinstaller version: 2.1+
[*] Python version: 36
[*] Length of package: 1166226 bytes
[*] Found 7 files in CArchive
[*] Beginning extraction...please standby
[+] Possible entry point: pyiboot01_bootstrap
[+] Possible entry point: hello
[*] Found 135 files in PYZ archive
[*] Successfully extracted pyinstaller archive: dist/windows/hello/hello.exe

You can now use a python decompiler on the pyc files within the extracted directory
```

Check the extraction of the `entry point`,

```
$ file hello.exe_extracted/hello
hello.exe_extracted/hello: data  # no profit
```

Now, use the patched program,

```
$ python3 pyinstxtractor-patched.py dist/windows/hello/hello.exe
[*] Processing dist/windows/hello/hello.exe
[*] Pyinstaller version: 2.1+
[*] Python version: 36
[*] Length of package: 1166226 bytes
[*] Found 7 files in CArchive
[*] Beginning extraction...please standby
[+] Possible entry point: pyiboot01_bootstrap
[+] Possible entry point: hello
[*] Successfully extracted pyinstaller archive: dist/windows/hello/hello.exe
```

Check the extraction of the `entry point`,

```
$ file hello.exe_extracted/hello
hello.exe_extracted/hello: python 3.6 byte-compiled  # works

$ cp hello.exe_extracted/hello hello.pyc

$ uncompyle6 hello.pyc
...
print('Hello!')
# okay decompiling hello.pyc
```


#### Ideas

* Add support for PyInstaller generated ELF files.

  See `pyi_arch_find_cookie` function at [this URL](https://github.com/pyinstaller/pyinstaller/blob/develop/bootloader/src/pyi_archive.c#L249).

* Add support for stripping away `digital signature` blobs attached to PE binaries.
