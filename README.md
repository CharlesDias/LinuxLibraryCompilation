# Linux Compilation

**Index:**
- [1. Compilation process.](#1-compilation-process)
- [2. Types of linking.](#2-types-of-linking)
- [3. Object files.](#3-object-files)

## 1. Compilation process.

The compilation process transforms human-readable source code into machine-executable binary code.

1. Preprocessing:
    - Before actual compilation begins, the source code goes through a preprocessing stage.
    - This stage handles directives of preprocessing, like `#ifdef`, expand macros and add include files in source code.
    - Use the cpp command to generate the file with all macros and code expanded. Example,
    ```console
    $ gcc -E main.c > main.i
    ```

2. Compilation:
    - Expanded source code is compiled into assembly language code specific to the target architecture.
    - This step checks the syntax of the program. If there are any syntax errors, the compiler will stop and report them.
    - The command `gcc -S` generates the `.s` output file that contains architecture-specific assembly code.
    ```console
    $ gcc -S main.i
    ```

3. Assembly:
    - The assembler translates the assembly code into machine code in the form of object files (with a .o or .obj extension).
    - This step generates the actual binary code but hasn't linked it yet.
    ```console
    $ gcc -c main.i
    ```

4. Linking:
    - The linker takes one or more object files and links them together to produce a single executable file or a library.
    - If your program references external libraries or functions across multiple source files, the linker resolves these references and ensures that everything points to the right place.
    ```console
    $ gcc -o main.o
    ```

### Code example

See the `ex01_compilation_process` code to reproduce the compilation process.

## 2. Types of linking

In Linux there are two main types of linking when it comes to libraries: static linking and dynamic linking.

### 2.1 Static Linking

In static linking, the library's code is integrated into the executable itself at compile time. As a result, the executable becomes self-contained and doesn't depend on external library files during runtime. This also means the binary will be larger since it contains all the required library code.

**Pros**
- No external dependencies at runtime. The executable can run on any system of the same architecture without needing the library installed.
- Potentially faster execution since there's no runtime overhead of loading or linking library code.

**Cons**
- Larger executable size.
- If the library gets an update (e.g., a bug fix or a security patch), the executable won't benefit from it unless recompiled with the updated library.
- Uses more disk space if multiple executables statically link the same library.

**Usage**
Use the static version of the library (typically has a .a extension, e.g., libfoo.a).

```bash
gcc program.c -L/path/to/library -lfoo -static -o program
```

### 2.2 Dynamic linking

In dynamic linking, the library's code isn't part of the executable. Instead, the executable contains a reference to the library. The actual linking happens at runtime when both the executable and the library are loaded into memory. This is the most common method of linking on Linux.

**Pros**
- Smaller executable size.
- Multiple executables can share a single instance of the library in memory, saving RAM.
- Updating the library immediately benefits all dynamically linked executables, without recompilation. This is especially important for security patches.

**Cons**
- The executable requires the library to be present on the system at runtime. If the library is missing or an incompatible version is installed, the program won't run.
- Slight overhead during program startup due to the dynamic linking process.

**Usage**
Use the shared version of the library (typically has a .so extension, e.g., libfoo.so).

```bash
gcc program.c -L/path/to/library -lfoo -o program
```

**Note:** For dynamic libraries, there's also the concept of the runtime linker (or dynamic linker), which is responsible for loading and linking shared libraries at runtime.

**Which One to Choose?**

If portability (across machines without needing extra libraries) and possibly faster execution are crucial, consider static linking.

If saving disk space, memory, and benefiting from library updates without recompiling are more important, go with dynamic linking.

In practice, many systems rely heavily on dynamic linking due to the advantages of shared library updates and memory savings. Still, there are cases, especially in embedded systems or isolated environments, where static linking might be preferred.


## 3 Object files

An object file is the output generated when source code files are compiled. It contains machine code, but it's not yet a complete, runnable program or library. These files usually have a .o or .obj extension.

Contents of an Object File:
- Machine Code: This is the binary code the CPU will execute, but it might still have some placeholders for addresses or values that need to be filled in during the linking phase.
- Symbols: Symbols are names that represent various entities, such as functions or variables. Object files contain tables that list these symbols, and these tables help the linker in the final stages of creating an executable or library.
- Relocation Information: This information is vital for the linker. It tells the linker which parts of the machine code need to be adjusted or filled in with correct addresses once the final program layout is known.
- Debug Information: If the program was compiled with debug flags (e.g., -g with GCC), the object file might contain extra information that debuggers can use to map machine code back to the original source code.
- Other Metadata: This includes information about the architecture, sections of the object file, and more.

There are three types of object files:
- Relocatable Object Files: These are the intermediate files that are produced when source code files are compiled but not yet linked. They contain code and data sections that can be combined with other relocatable object files to produce a runnable program or shared object.
- Executable Object Files: These are runnable programs. They result from linking one or more relocatable object files. All addresses are fixed, and all symbols have been resolved.
- Shared Object Files: These are similar to executable files but are intended to be loaded by other programs at runtime. They are used to provide library functions and other capabilities to programs without having to include that code in every single program.