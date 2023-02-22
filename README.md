**Project Address**

https://github.com/jkriege2/TinyTIFF

**Security Issue Report**

A heap-buffer-overflow issue was discovered in TinyTIFF in tinytiffreader.c file. The flow allows an attacker to cause a denial of service (abort) via a crafted file.

**Summary**

SUMMARY: AddressSanitizer: heap-buffer-overflow (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x496a79) in __asan_memcpy

**Problem Code location**

```
    #0 0x496a79 in __asan_memcpy (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x496a79)
    #1 0x4c9a92 in TinyTIFFReader_getSampleData /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:794:25
    #2 0x4d1183 in main /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:1066:9
    #3 0x7ffff7c4a082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #4 0x41c3bd in _start (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x41c3bd)
```

[Poc file](https://github.com/wavesky327/tinytiff/blob/main/id28)

[asan_tinytiffreader](https://github.com/wavesky327/tinytiff/blob/main/asan_tinytiffreader)


**ASAN Report:**

```bash
ubuntu@ubuntu:~/Desktop/TinyTIFF/src$ ./asan_tinytiffreader out/default/crashes/id\:000028\,sig\:06\,src\:000137\,time\:590193\,execs\:84091\,op\:havoc\,rep\:8 
=================================================================
==4023556==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x629000004200 at pc 0x000000496a7a bp 0x7fffffffddb0 sp 0x7fffffffd578
WRITE of size 4 at 0x629000004200 thread T0
    #0 0x496a79 in __asan_memcpy (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x496a79)
    #1 0x4c9a92 in TinyTIFFReader_getSampleData /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:794:25
    #2 0x4d1183 in main /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:1066:9
    #3 0x7ffff7c4a082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #4 0x41c3bd in _start (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x41c3bd)

0x629000004200 is located 0 bytes to the right of 16384-byte region [0x629000000200,0x629000004200)
allocated by thread T0 here:
    #0 0x497792 in calloc (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x497792)
    #1 0x4d1173 in main /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:1065:34
    #2 0x7ffff7c4a082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16

SUMMARY: AddressSanitizer: heap-buffer-overflow (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x496a79) in __asan_memcpy
Shadow bytes around the buggy address:
  0x0c527fff87f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8800: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8810: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8820: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8830: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c527fff8840:[fa]fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8850: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8860: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8870: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8880: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8890: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==4023556==ABORTING
```

