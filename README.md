*NOTE*: This is a copy of Yoshinori K. Okuji's failmalloc library,
whose original homepage is at http://www.nongnu.org/failmalloc/

It has been patched to compile on modern Ubuntu systems.

# Introduction

Welcome to failmalloc. This software generates a shared library
which can be loaded by `LD_PRELOAD` or linked at compilation time.

The idea behind failmalloc is to demonstrate what really happens
if memory allocation fails. Unfortunately, most programs in this
world are not robust, mostly because programmers are careless,
but sometimes because programmers are too brave. Regardless of
whatever reasons, the most critical problem is in that there is no
good way to see such failures in reality, until a program is
deployed into a heavy production system. Clearly, this is too
late!

Failmalloc addresses this problem. Failmalloc inserts hooks
into your program, which induces always, often or sometimes
failures of memory allocation calls. The functions `malloc`,
`realloc` and `memalign` are hooked, but other functions which
use one of these functions are also affected.

# Supported systems

This library depends on glibc. So it will run with all glibc-based
operating systems, such as GNU/Linux and GNU/Hurd.

# Installation

As usual, `configure && make && make install`.

If you want to test it without installing, use `.libs/libfailmalloc.so`.

# Usage

The easiest way is to use `LD_PRELOAD`. For example:

  `env LD_PRELOAD=libfailmalloc.so ls`

Note that failmalloc always fails by default. So there are
four environment variables to tweak the behavior:

  `FAILMALLOC_PROBABILITY` -- specifies how often it should fail
                            between 0.0 and 1.0.

  `FAILMALLOC_INTERVAL` -- specifies the interval of failures.

  `FAILMALLOC_TIMES` -- specifies how many times failures may happen
                      at most.

  `FAILMALLOC_SPACE` -- specifies the size of free space where memory
                      can be allocated safely in bytes.

For example, this makes malloc/realloc/memalign fail at 80%:

  `env LD_PRELOAD=libfailmalloc.so FAILMALLOC_PROBABILITY=0.8 ls`

This makes them fail every 5 times:

  `env LD_PRELOAD=libfailmalloc.so FAILMALLOC_INTERVAL=5 ls`

If you specify both variables, they fail at 80% every 5 times:

  ```
  env LD_PRELOAD=libfailmalloc.so FAILMALLOC_INTERVAL=5 \
    FAILMALLOC_PROBABILITY=0.8 ls
  ```

This ensures that they can fail only once:

  `env LD_PRELOAD=libfailmalloc.so FAILMALLOC_TIMES=1 ls`

This ensures that memory allocations do not fail up to 1MB being
allocated:

  `env LD_PRELOAD=libfailmalloc.so FAILMALLOC_SPACE=0x100000 ls`

# Contact information

Please look at <http://www.nongnu.org/failmalloc> or send email
to "Yoshinori K. Okuji" <okuji@enbug.org>.
