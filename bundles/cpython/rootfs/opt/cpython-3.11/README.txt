CPython 3.11.2 runtime for CPUSTC LA32R
=======================================

Target:
  LoongArch32, little endian, ILP32 soft-float ABI
  GNU/Linux with the LA32R instruction set

Included:
  CPython 3.11.2
  pip 22.3.1
  setuptools 65.6.3
  bz2, ctypes, curses, decimal, expat, lzma, readline, sqlite3,
  OpenSSL, threading, and zlib support

The runtime is relocatable. Mount this image at any path and run:

  /path/to/mount/bin/python3 --version
  /path/to/mount/bin/pip3 --version
  /path/to/mount/bin/cpython-selftest

Bytecode writes are disabled by default to reduce SD-card writes. Override
PYTHONDONTWRITEBYTECODE=0 when persistent bytecode caches are desired.

This baseline supports pure-Python pip packages. Native extension builds need
a matching target-native compiler and a separate CPython development sysconfig.
