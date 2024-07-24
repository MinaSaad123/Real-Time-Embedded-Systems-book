# **Chapter 2 Cross-Platform Development**
## **Table of Contents**

- [**Cross-Platform Development Process**](#cross-platform-development-process)
- [**Hardware Architecture**](#hardware-architecture)
- [**Software Development**](#Software-Development)
    - Software Design 
    - System Programming Language C/C++ 
         -*Declarations and definitions* 
         - *Scope regions* 
         - *Storage duration* 
         - *Linkage* 
- **Storage-class specifiers** 
    - Test Hardware-Independent M  odules 
- [**Build Target Images**](#Build-Target-Images)
    - Cross-Development Toolchain 
         - *Cross compiler/assembler* 
         - *Linker* 
         - *Dynamic linker* 
    - Executable and Linking Format 
         - *Linking view* 
         - *Execution view* 
    - Memory Mapping 
    - Case Study: Building a QNX Image 
- **Transfer Executable File Object to Target** 
- **Integrated Testing on Target** 
- **System Production** 

## **Cross-Platform Development Process**
**We need to distinguish two terms:**

**`Host platform:`** refers to the computing environment (i.e., the processor architecture and, if applicable, the operating system) upon which software is developed and its executable artifact is built.

**`Target platform:`** refers to the computing environment upon which software (actually its executable artifact) is intended to run.

<img  src="https://i.imgur.com/S7web3t.png"  width=" 1000">

The shows the cross-development process for real-time embedded
systems. At a high level, some activities need to be conducted on the host platform, while
others need to be performed on the target platform. We next explain each of those activities.

## **Build Target Images**

we will look at the toolchain for building target images, and introduce a
standard object file **format—ELF**

<img align="right" src="https://i.imgur.com/wVubXub.png" width="500">

**The figure** shows the code transformation process involving a chain of cross-development tools: cross compiler/assembler, linker, and dynamic linker.

---

**`native compiler:`** compiler is called that if its output is **intended to directly run on the host platform** (or the same type of environment) where the compiler runs.

**`A cross compiler:`** is a compiler c**apable of generating executable code for a target platform** that is different from the host platform. This statement applies to a cross assembler as well, except that it processes source files written in assembly languages.

 For each compilation unit,\
 the compiler/assembler generates an object file.3 For instance, the C compiler can produce two object files—one.o and two.o—for the example code given before.

> [!NOTE]
> **Each object file contains a symbol table**

> [!NOTE]
> **Symbol:** refers to a **memory location**, the content of which is either data for a variable or code for a function.

> **`Symbol table:`** is an array-like data structure consisting of entries about the global symbols **(i.e., names of global variables and nonstatic functions)** defined in the compilation unit, as well as the external symbols (**with “external linkage”**) referenced in the compilation unit. When the compiler encounters a symbol declaration, it stores that **symbol** and its **attributes** in the symbol table of the object file.

For the example code given before, we have the following:
- **In one.c**, there are four global symbol definitions: global variables c, sa, and sb, and a function main(). It references two external symbols a and b().
  
-  **In two.c**, there are four global symbol definitions: global variables a, sb, and sc, and a function b(). It references the external symbol c. The symbol table also has an entry for temp,**which is a local variable with a static storage duration**. An object file’s symbol table holds information that is needed by a compiler/linker to locate and relocate a program’s symbolic definitions and references.

**An object file’s symbol table holds information that is needed by a compiler/linker to locate and relocate a program’s symbolic definitions and references.**
### **Linker**

**`A complete program (application):`** **is typically composed of multiple object files**, each of which may cross-reference the definitions for data or functions defined in the other object files.

**`static linking:`** The process of combining multiple object files into a single object file, which is performed by a computer program known as a linker or link editor. The compiler automatically invokes the linker as the last step of compiling.

**A linker has two major jobs:**
1. **`Symbol resolution:`** While a linker is processing the application-specific object files, it needs to analyze each object file and determine where symbols with external linkage are defined. It is worth noting that two variables of the same name can be defined in different scopes, a staic variables with same name defined in different scope will not will not confuse the linker **because the compiler has treated them as distinct symbols. Essentially the compiler uses “namespace” to distinguish variables: **a local variable name is tagged by the function to which it belongs and a global variable name is tagged by the file name.** Archived library/sharable object files: application-independent object files—those that define commonly used functions (say, printf) for a wide range of applications.

 **So responsibles for Symbol resolution:**
   1. it needs to analyze each object file and determine where symbols with external linkage are defined.
   2. it to involve sharable object files for commonly use.
  
**`Symbol relocation:`**: **The final object file** produced by a linker contains all the symbol definitions originating from the input object files, as well as those from static library files, As the linker merges the input object files and inserts code from the library files, **the symbol offsets are changed**. By symbol relocation, the linker, via a relocation table, modifies the binary code of the final object file **so that each symbol reference reflects the actual address assigned to that symbol.**

**A linker can generate three types of object files: relocatable file, executable file, and shared object file:**

1. **`Relocatable file`**. A relocatable file holds code and data **suitable for linking with other object files** to create an executable file or a shared object file.


2. **` Executable file`**. An executable file holds code and data **suitable for execution**. In an operating system, the basic unit of execution is called a process or process image, which is dynamically created from an executable file (say, as a result of an exec or spawn call). For systems supporting virtual memory, each process is put into its own address space.


3. **`Shared object file`**. A shared object file holds code and data **suitable for further linking**. It can be combined with other relocatable and shared object files **to create another object file**. Some shared object files are **dynamically linkable**, and are intended to be loaded at run time and can be simultaneously shared by multiple process images.

#### **Dynamic linker**

In order to create a process image for an object file, the object file has to be **loaded from its storage location into RAM**. This job is performed by a program interpreter, which
-  itself is an executable file or a shared object file;
-  is self-interpreted (it does not need another program interpreter);
-  is able to receive control from the system and provide a running environment for the object being loaded.

> [!NOTE]
> **`program loader:`** is a program interpreter that **simply loads a program into memory and transfers the execution control to it.**

This approach works for **stand-alone executable files**.
> [!NOTE]
> **`stand-alone executable file:`** it contains one copy for each library routine used in the program. This makes the executable object easier to distribute to diverse target environments, at the cost of larger memory space.

Another type of program, instead of having an embedded copy for each library routine, c**ontains only reference information of sharable library routines.** Obviously, such a program requires the presence of library files on the target system. It also demands **a dynamic linker**
> [!NOTE]
> **` a dynamic linker:`** a program interpreter that is more powerful than a simple program loader. A dynamic linker, once it has gained control from the system, will first load the whole program into memory to form an initial process image, then resolve symbolic references dynamically by loading and binding external shared libraries to form a complete process image, and finally transfer control to the process. This procedure is also called dynamic linking.

**An advantage of dynamic linking is that multiple applications can share a single copy of a library. This also implies that a deployed system can automatically benefit from bug fixes and upgrades to libraries**
 

