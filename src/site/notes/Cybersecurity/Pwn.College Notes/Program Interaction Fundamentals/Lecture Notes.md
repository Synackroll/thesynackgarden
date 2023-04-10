---
{"dg-publish":true,"permalink":"/cybersecurity/pwn-college-notes/program-interaction-fundamentals/lecture-notes/"}
---


[Program Interaction - The Command Line](https://www.youtube.com/watch?v=w7nQFk6bi_k)

This class focuses on Linux and is a quick command line refresher.

Typically a command contains a pogram name and arguments to that program.

Get familiar with programs: Use documentation with "man" for programs. Also, help for shell "builtins."

A process is a running program.

A program is a file on your computer.

[Program Interaction - Binary Files](https://youtu.be/nKqFeYJ483U?list=PL-ymxv0nOtqqQ4NR1JnbWoHNm0Q8EspO1)

ELF - Executable Linkable Format.  Defines a program that can be executed in memory. This is on linux.  On windows `.exe` files.

Can examine a file with `file <filename>`

## ELF Program Headers

ELF contains `program headers` that define segments of an elf file that will be loaded when the program is executed.  You can do `readelf <file>` to examine a file and everything you'd want to know about it.

Most important entry types:
- INTERP: defines the libarary that should be used to loadt he ELF into memory.
- LOAD: defines a part of the file that should be loaded into memory.

## ELF Section Headers

A different view of the ELF with useful information.

Important sections:
- `.text` the executable code of the program.
- `.plt` and `.got` used to resolve and dispatch library calls.
- `.data` used for pre initiliazed global wriatable data (such as global arrays with initial values)
- `.rodata` used for global read-only data (such as string constants)
- `.bss` used for uninitialized global writable data (sucha s global arrays without initial values.)

Section headers are not a necessary part of ELFs

## Symbols

Binaries and libraries that use dynamically loaded libraries rely on symbols (names) to find libraries, resolve function calls into those libraries, etc. [Blog post on symbols](https://www.intezer.com/blog/malware-analysis/executable-linkable-format-101-part-2-symbols)

## Interacting with Your ELF

- gcc - to make your ELF
- readelf - to parse the 
- objdump - to parse the ELF header and disassemble the source code
- nm = to view your ELF's symbols
- patchelf - to change some ELF properties
- objcopy - to swap out ELF sections
- strip - to remove otherwise-helpful information
- kaitai struct - look through ELF interactively

# Linux Process Loading

When we type `cat /flag`:

1. Process is created
2. Cat is loaded
3. Cat is initialized
4. Cat is launched
5. Cat reads its arguments and environment
6. Cat does its thing
7. Cat Terminates

## What is a process?

Every Linux process has:
- state (running, waiting, stopped, zombie)
- priority (and other scheduling informaiont)
- parent, siblings, children
- shared resources (files, pipes, sockets)
- virtual memory space
- Security Context
	- effective uid and gid
	- saved uid and gid
	- capabilities

### Where do processes come from?

Processes come from otherp rocesses.

`fork` and `clone` are *system calls* that create a nearly exact copy of the calling process: a *parent* and *child*.

The child process uses `execve` syscall to *replace* itself with another process.

## Next Loading

### Can we load?

First the kernel checks for executable permissions.

### What to load?

The kernel will then decide what to load. It reads the beginnging of the file and decides:
1. if file starts with #! kerlen extracts the interpreter from the rest of that line and executes the interpreter with the original file as an argument.
2. If the file matches a format in /proc/sys/fs/binfmt_misc, the kernel executes the interpreter specified for that format with the original file as an argument.
3. IF the file is a dynimcally -ELF, the kernel reads the interpeter/loader defined in the ELF, loads the interpreter and the original file and lets the interpreer take control.
4. If the file is a statically-linked ELF, the kernel will load it.
5. Other legacy file formats are checked for.

These can be recursive.

One of the takeaways here is that the extension does not mateter.

### Dynamically linked ELFS: the interpreter

Process loading is done by the ELF interpreter specified in the binary.

```
$ readelf -a /bin/cat | grep interpret
[Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]

```

Can change the interpreter with `patchelf --set-interpreter`

### Dynamically linked ELFs: the loading process

1. Program and interpreter are loaded by the kernel.
2. The interpreter locates the libraries.
	a. LD_PRELOAD environment variable and anything in `/etc/ld.sopreload`
	b. LD_LIBRARY_PATH environment variable (can be set in the shell)
	c. DT_RUNPATH or DT_RPATH specified in the binary file (both can be mod with patchelf)
	d. system-wide configuration (`/etc/ld.so.comf`)
	e. `/lib` and `/usr/lib`
3. The interpreter loads the libraries
	- These libraries can depend on other libraries, causing more to be loaded.
	- Relocations are updated.

[See also.](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-4.html)

`Strace` is a cool program that shows all the system calls.

### Where does it get loaded to?

Each linux process has a *virtual memory space*. It contains:
- the binary
- the libararies
- the "heap" (for dynamically allocated memory)
- the "stack" (for function local vairables)
- any memory specifically mapped by the program
- helper regions
- Kernel code in the "upper half" of memory inaccessible to the process.

Virtual memory is dedicated to the process.
Physical memory is shared among the whole system.
You can see the whole space by looking at /proc/self/maps.
[See also](https://gist.github.com/CMCDragonkai/10ab53654b2aa6ce55c11cfc5b2432a4)

### The Standard C Library

libc.so is linked by almost every process

Provides functionality you take for granted:
- printf()
- scanf()
- socket()
- atoi()
- malloc()
- free()

### Loading statically linked processes

Statically linked binaries: This is where all the libraries are part of the binary.

1. The binary is loaded.

## Next: Initialization

### Cat is initialized.

Every ELF binary can specify *constructors*, which are functions that run before the program is actually launched.

For example, depending on the version, libc can intialize memory regions for dynamic allocations (malloc/free) when the program launches.

You can specify your own:
```

__attribute__((constructor))void haha()
{
	puts("Hello world!")
}
```


# Execution

A normal ELF automatically calls `__libc_start_main()` in libc, which in turn calls the program's main() function.

`objdump -d cat -M intel` to check the assembler.

## Cat Reads its arguments and environment.

`int main(intargc, void **argv, void **envp);`

The processes' input from the outside world consists of:
- the loaded objects (binaries and libraries)
- command-line arguments in argv
- "environment" in envp

Environmental variables may change the execution of the program.

## Using Library Functions

The binary's *import symbols* have to be resolved using the librarie's *export symbols*. 
It used to be symbol uses was resolved on-demand. This caused a ton of security gaps.

In modern times, these resolutions are done on load.

`nm` will allow you to look at symbols in a binary.  Symbols defined in the binary will have an address.

## Interacting with the environment

almost all programs interact with the outside world. This is done primarily thorugh *system calls* (`man syscalls`)/  Each system call is well documented in section 2 of the man pages. (i.e., `man 2 open`)

We can process system calls using `strace`

## System Calls

[Syscall Table](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)

There are over 300 system calls in linux. They are a way to call into the OS. This does not go the other way around.

## Signals

Signals is a signal sent by the OS to the process. You can go to signal handler. Signals pause process execution and invoke the handler.

You can't block a SIGKILL (signal 9) and SIGSTOP (signal 19) -- that is, thaey can't be handled.

These can be seen with `man 7 signal` and `kill -l`

```
   Standard signals
       Linux supports the standard signals listed below.  The second column of
       the table indicates which  standard  (if  any)  specified  the  signal:
       "P1990"  indicates  that  the  signal  is  described  in  the  original
       POSIX.1-1990 standard; "P2001" indicates that the signal was  added  in
       SUSv2 and POSIX.1-2001.

       Signal      Standard   Action   Comment
       ────────────────────────────────────────────────────────────────────────
       SIGABRT      P1990      Core    Abort signal from abort(3)
       SIGALRM      P1990      Term    Timer signal from alarm(2)
       SIGBUS       P2001      Core    Bus error (bad memory access)
       SIGCHLD      P1990      Ign     Child stopped or terminated
       SIGCLD         -        Ign     A synonym for SIGCHLD
       SIGCONT      P1990      Cont    Continue if stopped
       SIGEMT         -        Term    Emulator trap
       SIGFPE       P1990      Core    Floating-point exception
       SIGHUP       P1990      Term    Hangup detected on controlling terminal
                                       or death of controlling process
       SIGILL       P1990      Core    Illegal Instruction
       SIGINFO        -                A synonym for SIGPWR
       SIGINT       P1990      Term    Interrupt from keyboard
       SIGIO          -        Term    I/O now possible (4.2BSD)
       SIGIOT         -        Core    IOT trap. A synonym for SIGABRT
       SIGKILL      P1990      Term    Kill signal
       SIGLOST        -        Term    File lock lost (unused)
       SIGPIPE      P1990      Term    Broken pipe: write to pipe with no
                                       readers; see pipe(7)
       SIGPOLL      P2001      Term    Pollable event (Sys V);
                                       synonym for SIGIO

       SIGPROF      P2001      Term    Profiling timer expired
       SIGPWR         -        Term    Power failure (System V)
       SIGQUIT      P1990      Core    Quit from keyboard
       SIGSEGV      P1990      Core    Invalid memory reference
       SIGSTKFLT      -        Term    Stack fault on coprocessor (unused)
       SIGSTOP      P1990      Stop    Stop process
       SIGTSTP      P1990      Stop    Stop typed at terminal
       SIGSYS       P2001      Core    Bad system call (SVr4);
                                       see also seccomp(2)
       SIGTERM      P1990      Term    Termination signal
       SIGTRAP      P2001      Core    Trace/breakpoint trap
       SIGTTIN      P1990      Stop    Terminal input for background process
       SIGTTOU      P1990      Stop    Terminal output for background process
       SIGUNUSED      -        Core    Synonymous with SIGSYS
       SIGURG       P2001      Ign     Urgent condition on socket (4.2BSD)
       SIGUSR1      P1990      Term    User-defined signal 1
       SIGUSR2      P1990      Term    User-defined signal 2
       SIGVTALRM    P2001      Term    Virtual alarm clock (4.2BSD)
       SIGXCPU      P2001      Core    CPU time limit exceeded (4.2BSD);
                                       see setrlimit(2)
       SIGXFSZ      P2001      Core    File size limit exceeded (4.2BSD);
                                       see setrlimit(2)
       SIGWINCH       -        Ign     Window resize signal (4.3BSD, Sun)

       The signals SIGKILL and SIGSTOP cannot be caught, blocked, or ignored.

```


## Shared Memory

Share memory with another process is a way to interact with the outside world.

## Process termination

Ends in two ways:
1. receiving an unhandled signal
2. Calling the exit() system call: `int exit(int status)`

All processes must be "reaped"
- ater termination they will remain in a zombie state until they are wait()ed on by their parent,
- When this happens their exit code will be returned to the parent and the process will be freed.
- If the parent dies without waiting on them, they are re-parented to PID 1 and will stay there until they're cleaned up.




