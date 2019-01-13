---
layout: default
title: C++ Workshop 1 - Introduction
permalink: cplusplusintro1.html
---

# C++ Workshop 1 - Introduction

History of native programming languages
=======================================

Machine code
-----------

Different types of CPUs have different **architectures**, which consist of:

- A set of named **registers**, which are small caches of memory contained on the CPU; and
- An **instruction set**, a fixed set of commands that the CPU can execute, that work with data stored in the registers

Processors execute **machine code**, a series of instructions from the architecture's instruction set, represented in binary format.

2nd Generation Languages (2GL): Assembly
----------------------------------------

The next programming languages above machine code were **assembly** languages, also known as **2nd Generation Languages (2GL)**. Assembly is essentially a human-readable form of machine code, and has a 1-to-1 mapping to binary machine code.

- A tool called an **assembler** converts assembly code to binary machine code/
- There is an assembly language for each architecture (e.g. x86, x86-64 / amd64, ARM, etc.), and programs written in assembly must be re-written in the assembly language for each processor architecture that the program will run on/

3rd Generation Languages (3GL)
------------------------------

Due to the frustrations of writing the same program multiple times in different languages for different architectures, the next level of programming languages was invented. Referred to as **3rd Generation Languages (3GL)**, these include programming languages such as FORTRAN, COBOL, C/C++, Java, C#, etc.

- Although some 3GLs can be interpreted by a tool known as an *interpreter*, this strategy does not apply to native code.
- A tool known as a **compiler** parses the code from native 3GLs, and generates the assembly for the chosen **target** architecture.
- The generated assembly code is then assembled into machine code. The compiler produces an executable file containing the machine code version of the program, that can be run on any CPU that utilises the target processor architecture.

As an example, here is a very simple C program:

```cpp
int main()
{
	int i = 0;
	return i;
}
```
**Listing 1: a simple C program.**

Here is the assembly code version of the program, produced by the Clang C++ compiler, targeting the **x86-64** architecture:

```asm
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
	.align	4, 0x90
_main:                                  ## @main
	.cfi_startproc
## BB#0:
	pushq	%rbp
Ltmp2:
	.cfi_def_cfa_offset 16
Ltmp3:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Ltmp4:
	.cfi_def_cfa_register %rbp
	movl	$0, -4(%rbp)
	movl	$0, -8(%rbp)
	movl	-8(%rbp), %eax
	popq	%rbp
	ret
	.cfi_endproc


.subsections_via_symbols
```
**Listing 2: x86-64 assembly version of the C program from Listing 1.**

And here is the assembly code version of the program, produced by the Clang C++ compiler, targeting the **armv7** architecture:

```asm
	.section	__TEXT,__text,regular,pure_instructions
	.section	__TEXT,__textcoal_nt,coalesced,pure_instructions
	.section	__TEXT,__const_coal,coalesced
	.section	__TEXT,__picsymbolstub4,symbol_stubs,none,16
	.section	__TEXT,__StaticInit,regular,pure_instructions
	.section	__TEXT,__cstring,cstring_literals
	.syntax unified
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
	.align	2
	.code	16                      @ @main
	.thumb_func	_main
_main:
@ BB#0:
	sub	sp, #8
	movs	r0, #0
	movt	r0, #0
	str	r0, [sp, #4]
	str	r0, [sp]
	ldr	r0, [sp]
	add	sp, #8
	bx	lr


.subsections_via_symbols
```
**Listing 3: armv7 assembly version of the C program from Listing 1.**


Native languages .vs. non-native languages
==========================================

Native languages have a number of advantages:

- Extremely fast, as the machine code is executed directly by the CPU
- Access to all low-level hardware and OS resources, as code executes directly on the real hardware (as opposed to a sandboxed environment)

However, native languages have one very important disadvantage:

- Programs must be compiled for each target processor architecture the program will run on.

To address this limitation, non-native programming languages often utilise an **Application-Layer Virtual Machine**. This is a software layer that sits on top of the underlying Operating System (OS) and hardware, and abstracts access to it.

- A **compiler** still compiles the source code, but instead of generating machine code for a specific processor architecture, the compiler produces what is known as **bytecode**.
- **Bytecode** is essentially machine code, but targeting the instruction set and registers of a **virtual processor architecture**, defined by the application-layer VM.
- The application-layer VM then executes the bytecode, either by interpreting it or by transforming it into machine code for the processor architecture the application-layer VM is currently running on.
- Access to underlying hardware and OS resources is controlled by the application-layer VM. The executing program is effectively **sandboxed**, and is isolated from the underlying system, abstracting away low-level details and preventing serious system crashes or instability.

This provides non-native code with a number of advantages:

- Programs can be compiled once, and then run on any architecture for which the application-layer VM is available
- Programmers don't need to worry about changes in low-level system or hardware details across architectures, as these have been abstracted by the application-layer VM
- Sandboxed programs cannot cause serious system instability or system-wide crashes, as they are executing in a sandboxed environment, not directly on the real hardware

This makes non-native languages useful for a wide variety of situations. However, native languages are still necessary in a number of situations:

- For maximum speed
- When access to low-level OS and hardware resources is required


Setting up a command-line C++ compiler
======================================

- Under Linux, the **GNU C++ Compiler** (**g++**), is often already installed. In cases when it is not, simply install the relevant package (e.g. `build-essential` under Ubuntu.)
- Under Mac OS X, the **Clang C++ Compiler** (**clang++**), is part of the **"Xcode Command-line Developer Tools"** package, which is available for members of the Apple Developer Program to download from the Apple Developer website at <http://developer.apple.com/downloads>.
- Under Windows, the **GNU C++ Compiler** (**g++**) can be obtained by installing the **MinGW** system from <http://www.mingw.org/>.
  - During installation, be sure to check the relevant boxes that mention g++.
  - You will need to add the directory `C:\MinGW\bin` to the system **PATH** environment variable, in order to access g++ from the command prompt.

To test that the compiler was installed correctly, you can enter the following command in a terminal / command prompt (use `clang++` instead of `g++` under Mac OS X):

```
g++ -v
```

If the compiler outputs its version information, then everything is working correctly.


A simple C++ program
====================

The program
-----------

Create a file named `test.cpp` with the following contents:

```cpp
#include <iostream>

int main()
{
	std::cout << "Hello World!" << std::endl;
	return 0;
}
```

Now, compile the program using the following command (under Mac OS X, use `clang++` instead of `g++`, under Windows, call the output file `test.exe` instead of just `test`):

```
g++ -o test test.cpp
```

This should compile the program and create an executable called `test` (`test.exe` under Windows.) You can then run the program under Linux or Mac OS X like so:

```
./test
```

or using the following command under Windows:

```
test.exe
```

There are a number of things that need explaining in the C++ source code, which are described in the remaining sections.

The scope resolution operator (`::`)
--------------------------

Programmers familiar with languages such as Java and C# will be used to using the dot operator (`.`) for accessing the methods and properties of objects, static methods and properties of classes, and classes contained in packages (in Java) or namespaces (in C#.) In C++, the dot operator is still used for accessing the methods and properties of objects. However, the [scope resolution operator](http://en.wikipedia.org/wiki/Scope_resolution_operator) (`::`), is used when accessing static methods and properties of classes, and when accessing the names of classes and values inside namespaces (which are the C++ equivalent to packages in Java or namespaces in C#.)

Operator overloading
--------------------

Every operator in the C++ language can be overloaded - that is, a custom implementation can be provided by the programmer, which will be used when the operands of the operator are of the specified type. In the code above, the bitwise left-shift operator (`<<`) has been overloaded by the class [`std::ostream`](http://www.cplusplus.com/reference/ostream/ostream/), which is what [`std::cout`](http://www.cplusplus.com/reference/iostream/cout/) is an instance of. The `std::ostream` class utilises it as an "insertion operator", and is used for writing data to the stream. The overloaded version of the operator returns a reference to the stream object, allowing it to be chained as seen above. The equivalent Java code would be:

```java
System.out.println("Hello World" + "\n");
```

(The constant [`std::endl`](http://www.cplusplus.com/reference/ostream/endl/) represents a newline and also flushes the output buffer. It is equivalent to `'\n' << std::flush`.)

Multi-phase translation
-----------------------

The final part of the example code that needs explaining is the line `#include <iostream>`. To understand this, first an explanation is required of how C and C++ code is compiled.

C/C++ code is compiled in multiple phases. These phases are depicted in Figure 1:

![Diagram depicting multi-phase translation](./images/compilation_process_large.png "Figure #num: multi-phase translation of C/C++ code")

- **Preprocessing**: first, each source file is passed through the preprocessor, which acts upon any preprocessor directives such as `#define` and `#include` and performs the necessary substitutions. The output of the preprocessor is then passed to the compiler.
- **Compilation**: next, the compiler actually compiles the processed source code into machine code, and stores this output in an **object file** (usually with the extension `.o`.) One object file is created for each source file (source files for C/C++ typically having the extensions `.c`, `.cc`, or `.cpp`).
- **Linking**: finally, the linker takes all of the object files and "glues" them together, combining them into the finished executable.

The line `#include <iostream>` is a preprocessor directive that instructs the preprocessor to insert the contents of the header file "`iostream`". **Note that preprocessing is recursive when including files.** For example, if file A includes files B and C, then including file A will insert the contents of files A, B, and C.

The purpose of header files is to contain **forward declarations** of classes and functions that exist in other source files. Since C++ does not have a sophisticated package system such as that found in newer programming languages, it instead relies on effectively copy-pasting the contents of header files into source files. These header files contain forward-declarations, which are the minimum amount of information required about the contents of other source files in order to interoperate with them, without duplicating the contents of the actual source file in all the other source files that use the functions and classes it contains.

An illustrative example is helpful in understanding how header files are used. Consider the following three files:

**`utility.h`**
```cpp
void helpfulFunc();
int anotherHelpfulFunc(int a);
```

**`utility.cpp`**
```cpp
#include "utility.h"

void helpfulFunc()
{
	//Actual implementation of the function
	//...
}

int anotherHelpfulFunc(int a)
{
	//Actual implementation of the function
	//...
	
	return a + 2; //Or something useful
}

```

**`main.cpp`**
```cpp
#include "utility.h"

int main()
{
	helpfulFunc();
	
	int x = 2;
	int y = anotherHelpfulFunc(x);
	
	return 0;
}

```

The code in `main.cpp` utilises the functions contained in `utility.cpp`. Instead of duplicating the actual contents of `utility.cpp`, we place the minimum amount of information required in order to work with its contents, into the header file `utility.h`. We then `#include` the header file in the source files that need to work with our utility functions.

We can compile this example using the following commands (under Mac OS X, use `clang++` instead of `g++`, under Windows, call the output file `example.exe` instead of just `example`):

```
g++ -c -o utility.o utility.cpp
g++ -c -o main.o main.cpp
g++ -o example utility.o main.o
```

We compile each source file individually, producing one object file per source file. We then link the object files together into the final executable.

This compilation scheme becomes very useful when working on larger projects. Because the information about a source file is contained in its header, we can change the details of its implementation and recompile it into its object file, without the need to recompile any of the other source files (assuming the changes we make do not affect the function prototypes or class interfaces, which would necessitate changes to the header file, and the recompilation of any source files that `#include` the header file.)
