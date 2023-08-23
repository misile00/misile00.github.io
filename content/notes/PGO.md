---
title: "What is PGO?"
tags:
- optimization
weight: -5
date created: 2023-01-25 10:09
---

## Overview

Profile-Guided Optimization (PGO) is a technique used to optimize the performance of a computer program by using data collected during its execution to guide the optimization process. The idea is to use information about which parts of the program are executed more frequently to make more informed decisions about where to focus optimization efforts. This can lead to significant performance improvements, especially in large, complex programs.

The process of PGO typically involves three main steps:

*  Collect profile data: The program is executed with a special instrumentation that collects information about which parts of the code are executed most frequently. This is often done by inserting extra instructions into the program that record the address of each executed instruction, known as "instrumentation".

*  Optimize the program: The collected profile data is used to guide the optimization process. For example, the compiler may use the data to determine which parts of the code should be inlined or which loops should be unrolled.

*  Profile-guided code generation: The optimized program is then compiled using the profile data as input, this leads to a better optimized binary.

The process may be repeated multiple times to further improve performance.

**It is also important to note that PGO is not always beneficial and sometimes it can even decrease the performance of the program, it depends on the workload and how representative the training set is.**


### Common optimizations performed by PGO

**Inlining** - For example, if a function A frequently calls function B, and function B is relatively small, then profile-guided optimizations inline function B in function A.

**Virtual Call Speculation** - If a virtual call, or other call through a function pointer, frequently targets a certain function, a profile-guided optimization can insert a conditionally executed direct call to the frequently targeted function, and the direct call can be inlined.

**Size/Speed Optimization** - Functions where the program spends the most execution time can be optimized for speed.

**Function Layout** - Based on the call graph and profiled caller/callee behavior, functions that tend to be along the same execution path are placed in the same section.

**Conditional Branch Optimization** - Conditional branch optimization uses profile data to determine which code path is executed more frequently for a particular conditional branch. The compiler will then rearrange the code to make the more frequently executed code path faster and make the less frequently executed code path slower. This can help to improve the performance of the program by reducing the number of branches that need to be executed and the number of mispredictions that occur. 
 
For example, if a certain "if-else" statement is executed more frequently with the "if" part, the compiler will move the "if" part of the statement closer to the branch instruction and move the "else" part farther away. This will make the "if" part faster to execute and make the "else" part slower to execute.

**Dead code elimination** - Removes code that is not executed, reducing the size of the binary and improving performance.

**Loop unrolling** - Duplicates the body of a loop multiple times to reduce the number of iterations.

For example, there is a simple loop:
  
```c
for (int i = 0; i < 10; i++) {
    x += y;
}
```

This loop will execute 10 times, once for each iteration of the loop. With loop unrolling, the loop body is duplicated a certain number of times, and the loop variable is incremented by that number of times per iteration. For example, if we unroll the loop by a factor of 2:

```c
for (int i = 0; i < 10; i += 2) {
    x += y;
    x += y;
}
```

This will still execute the loop 10 times, but it will now execute the loop body twice per iteration, reducing the number of iterations required to execute the loop by a factor of 2.

Reducing the number of iterations can improve performance by reducing the overhead of the loop control instructions.
  
By repeating the loop body multiple times, it increases the chances of the code being executed to be present in the cache, which can also help improve performance by reducing cache misses.

**Register Allocation** - Although not important for reduced instruction set computing (RISC) architectures than their complex instruction set computing (CISC) counterparts, optimal use of available regsiters makes a lot of difference in application performance. With a sample heuristics available, the compiler can easily find the hot and very frequently used variables. This prevents unnesseary cache (perhaps memory) access, speeding up the application.

**There are more and different ways to use Profile-Guided Optimization depending on the platform and the development tools you are using.**


## How to use PGO?

Using PGO has different ways according to the development environment and tools used. I will give an example here via GCC.

First, compile the program with the profile data generation:

```bash
gcc -fprofile-generate=/tmp/pgo main.c -o main
```

Second, run the program (one or more times) to collect data:

```bash
./main
```

Optimize the program with the collected profile data:

```bash
gcc -fprofile-use=/tmp/pgo -fprofile-correction main.c -o main
```

I also used the `-fprofile-correction` option to prevent the code from corruption. 

Description of `-fprofile-correction` option at `gcc(1)` man page:

> Profiles collected using an instrumented binary for multi-threaded programs may be inconsistent due to missed counter updates. When this option is specified, GCC uses heuristics to correct or smooth out such inconsistencies. By default, GCC emits an error message when an inconsistent profile is detected.


Run the program and test:

```bash
./main
```

You can repeat these steps to increase performance further.


## Benchmarks

There are some benchmarks. Taken from Phoronix.

![[notes/images/pgo/yquake-pgo.png]]

![[notes/images/pgo/sci-mark-pgo.png]]

![[notes/images/pgo/botan-pgo.png]]

[Here](https://www.phoronix.com/news/GCC-12-PGO-TR-3990X-AMD) you can find more benchmark results.


## Conclusion

PGO is a very nice optimization option. It contains many nice techniques and may improve performance. It can be used with other optimization techniques such as LTO and you can improve performance much more.

However, PGO requires a compilation of a program multiple times due to its structure. You should consider whether the time and energy you spend on this will be profitable for you and decide whether or not to use PGO.


## See Also

* https://www.phoronix.com/news/GCC-12-PGO-TR-3990X-AMD
* https://learn.microsoft.com/en-us/cpp/build/profile-guided-optimizations?view=msvc-170
* https://en.wikipedia.org/wiki/Profile-guided_optimization
* https://developer.ibm.com/articles/gcc-profile-guided-optimization-to-accelerate-aix-applications/
* https://www.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/optimization-and-programming/profile-guided-optimization-pgo.html
* https://man7.org/linux/man-pages/man1/gcc.1.html