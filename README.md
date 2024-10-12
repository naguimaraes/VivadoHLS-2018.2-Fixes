# Fixes for known issues in Vivado HLS 2018.2

This repository contains a step-by-step guide to fix some issues that we've found in Vivado HLS 2018.2 while using an Ubuntu 20.04 LTS. Most of them occured during the tutorial provided by Xilinx.

## ERROR: [APCC 202-10] clang compile failed: child process exited abnormally and ERROR: [APCC 202-1] ProcessSources failed / APCC failed

Those error were found during the HLS Lab 1, Step 4: RTL Verification. It is related to a problem in the Clang compiler. The full error message is something like:

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

Change the extension of the C files from `.c` to `.cpp`. This will make the Clang compiler to use the C++ compiler instead of the C compiler.

You will also have to add `#include <cstlib>` in the beginning of the new C++ testbench file for it to use the same functions as the C testbench.



