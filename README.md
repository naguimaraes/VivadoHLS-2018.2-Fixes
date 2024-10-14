# Fixes for known issues in Vivado HLS 2018.2

This repository contains a step-by-step guide to fix some issues that we've found in Vivado HLS 2018.2 while using an Ubuntu 20.04 LTS. Most of them occured during the tutorial provided by Xilinx/AMD.

## Table of fixed errors by their code

- [[APCC 202-10] and [APCC 202-1]](#error-apcc-202-10-clang-compile-failed-child-process-exited-abnormally-and-error-apcc-202-1-processsources-failed--apcc-failed)
- [[IMPL 213-28]](#error-impl-213-28-failed-to-generate-ip)
- [BFD: Please report this bug](#bfd-please-report-this-bug)

## ERROR: [APCC 202-10] clang compile failed: child process exited abnormally and ERROR: [APCC 202-1] ProcessSources failed / APCC failed

Those error were found during the HLS tutorial in Chapter 2, Lab 1, Step 4: `RTL Verification`. It is related to a problem in the Clang compiler. The error message shown in console will be something like this:

```bash
In file included from /usr/include/math.h:43:
/usr/include/x86_64-linux-gnu/bits/floatn.h:75:52: error: unsupported machine mode '__TC__'
typedef _Complex float __cfloat128 __attribute__ ((__mode__ (__TC__)));
                                                   ^
/usr/include/x86_64-linux-gnu/bits/floatn.h:87:9: error: unknown type name '__float128'; did you mean '__cfloat128'?
typedef __float128 _Float128;
        ^
/usr/include/x86_64-linux-gnu/bits/floatn.h:75:24: note: '__cfloat128' declared here
typedef _Complex float __cfloat128 __attribute__ ((__mode__ (__TC__)));
                       ^
2 errors generated.
ERROR: [APCC 202-10] clang compile failed: child process exited abnormally
ERROR: [APCC 202-1] ProcessSources failed
INFO: [APCC 202-3] Tmp directory is /tmp/apcc_db_nathan/605521728415860478249
ERROR: [APCC 202-1] APCC failed.
```

### Solution

- Change the extension of the C files from `.c` to `.cpp`. This will make the Clang compiler to use the C++ compiler instead of the C compiler.

- You will also have to add `#include <cstlib>` in the beginning of the new C++ testbench file for it to use the same functions as the C testbench.

## ERROR: [IMPL 213-28] Failed to generate IP

This error was found during the HLS tutorial in Chapter 2, Lab 1, Step 5: `IP Creation`. It is related to a problem in the way they export the IP: using the current date, after 2022, will cause an overflow that will make the IP generation fail. The error message shown in console will be something like this:

```bash
source run_ippack.tcl -notrace
bad lexical cast: source type value could not be interpreted as target
    while executing
"rdi::set_property core_revision 2410081812 {component component_1}"
    invoked from within
"set_property core_revision $Revision $core"
    (file "run_ippack.tcl" line 795)
INFO: [Common 17-206] Exiting Vivado at Tue Oct  8 18:12:29 2024...
ERROR: [IMPL 213-28] Failed to generate IP.
```

### Solution

- Xilinx/AMD is already aware of this issue and released a patch to fix it, that can be found [here](https://adaptivesupport.amd.com/s/article/76960?language=en_US).

- Just follow the instructions in the README found inside the patch folder. But pay attention to a small datail: it is written in the README `Note: While extracting this patch on Windows, make sure the destination folder does not contain the zip file name (y2k22_patch-1.2). EX: If the installation root is C:\Xilinx make sure after the extraction y2k22_patch folder is under C:\Xilinx, i.e : C:\Xilinx\y2k22_patch` but this is also true for Linux systems. So, take care to extract the patch in a folder that does not contain the zip file name.

## BFD: Please report this bug

This error was found during the HLS tutorial in Chapter 3, Lab 1, Step 3: `Run the C Debugger`. It is related to a problem in the way the debugger is trying to load the shared library symbols. The error message shown in console will be something like this:

```bash
warning: Could not load shared library symbols for linux-vdso.so.1.
Do you need "set solib-search-path" or "set sysroot"?
BFD: BFD (GNU Binutils) 2.24.51.20140729 internal error, aborting at ../../../src/lnx64/bfd/elf64-x86-64.c line 5334 in elf_x86_64_plt_sym_val

BFD: Please report this bug.
```

### Solution

- Go to **Window -> Preferences**. In the opened window, go to **C/C++ -> Debug -> GDB**. In the **GDB debugger** field, add the following line: `/usr/bin/gdb`.
    1. ![Window](https://media.discordapp.net/attachments/1046502604546523136/1295491196239741038/image.png?ex=670ed7b2&is=670d8632&hm=4a591ca9ced2dddce1d1e830b56fc018147c1be26494135e70084321ffaf21a2&=&format=webp&quality=lossless)
    2. ![GDB](https://media.discordapp.net/attachments/1046502604546523136/1295491288728211620/image.png?ex=670ed7c8&is=670d8648&hm=d2d7987c0064685af446684f241873c12d33a5dfc7dac9a0baafe9748d471847&=&format=webp&quality=lossless)

- As a result, the following error may show up:
![EA](https://media.discordapp.net/attachments/1046502604546523136/1295490991557443704/image.png?ex=670ed781&is=670d8601&hm=e75da8fa64314c9512a71e1275e13097e846448348386e55b1131ef6acd43177&=&format=webp&quality=lossless)

- To solve this new error, follow this steps:
    1. Open your .bashrc file with `nano ~/.bashrc`;
    2. Add the following line to the end of the file: `export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6`;
    3. Save the file and run `source ~/.bashrc`.
