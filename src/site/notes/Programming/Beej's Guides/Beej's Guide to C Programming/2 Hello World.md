---
{"dg-publish":true,"permalink":"/programming/beej-s-guides/beej-s-guide-to-c-programming/2-hello-world/"}
---


# 2.1 What to Expect From C

- Now it's considered a low level language.
- Good learning tool.
- Still useful for embedded systems and operating systems.
- Pointers. You know, they're confusing for people until they get them.

# 2.2 Hello World!

```c
/* Hello world program */

#include <stdio.h>

int main(void)
{
    printf("Hello, World!\n");  // Actually do the work here
}
```

Commenting is between `/* and */`  Anything after `//`.


There are two stages to compilation: the preprocessor and the compiler. Anything that starts with the "octothorpe", (`#`) is a *preprocessor directive*. The preprocessor gets everything ready, and the ouptut of that is run to the compiler which produces an executable.

`<stdio.h>` is a header file, as shown by the `.h`  It's actually the "Standard I/O" header file that gives access to I/O functionality. The `printf` function comes from this.

The `main()` function is a special one in that it gets called when the program get executed.

This is ready to compile:
`gcc -o hello hello.c`

# 2.3 Compilation Details

Python compiles, it just does it behind the scenes. It composes *bytecode* that the Python virtual machine can execute. *Machine code* is generated when C programs are compiled. The C compiler is the program that does the compilation.

# 2.4 Building with gcc

`gcc -o hell hello.c`

`-o` means output to this file, and the name `hello.c` tells what file we want to compile. You can compile multiple files together.

```
gcc -o awesomegame ui.c characters.c npc.c items.c
```

# 2.5 Building with `clang`

On Macs, the stock compiler isn’t `gcc`—it’s `clang`. But a wrapper is also installed so you can run `gcc` and have it still work.

You can also install the `gcc` compiler proper through [Homebrew](https://formulae.brew.sh/formula/gcc)[32](https://beej.us/guide/bgc/html/split/footnotes.html#fn32) or some other means.


# 2.6 Building from IDEs

If you’re using an _Integrated Development Environment_ (IDE), you probably don’t have to build from the command line.

With VS Code, you can hit `F5` to run via the debugger. (You’ll have to install the C/C++ Extension.)





