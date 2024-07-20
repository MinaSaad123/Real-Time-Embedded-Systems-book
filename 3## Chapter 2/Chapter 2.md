# **Chapter 2 Cross-Platform Development**

## **Table of Contents**
- **Cross-Platform Development Process** 
- **Hardware Architecture** 
- **Software Developmen**t 
    - Software Design 
    - System Programming Language C/C++ 
         -*Declarations and definitions* 
         - *Scope regions* 
         - *Storage duration* 
         - *Linkage* 
- **Storage-class specifiers** 
    - Test Hardware-Independent Modules 
- **Build Target Images** 
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
**<u>we need to distinguish two terms:</u>**

**<u>host platform:</u>** refers to the computing environment (i.e., the processor architecture and, if applicable, the operating system) upon which software is developed and its executable artifact is built.

 **<u>target platform:</u>** refers to the computing environment upon which software (actually its executable artifact) is intended to run.

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

**<u>native compiler</u>:** compiler is called that if its output is **intended to directly run on the host platform** (or the same type of environment) where the compiler runs.

 **<u>A cross compiler:</u>** is a compiler c**apable of generating executable code for a target platform** that is different from the host platform. This statement applies to a cross assembler as well, except that it processes source files written in assembly languages.

 For each compilation unit,\
 the compiler/assembler generates an object file.3 For instance, the C compiler can produce two object files—one.o and two.o—for the example code given before.

**Each object file contains a symbol table**

**<u>Symbol:</u>** refers to a **memory location**, the content of which is either data for a variable or code for a function.

**<u>Symbol table</u>:** is an array-like data structure
consisting of entries about the global symbols **(i.e., names of global variables and nonstatic functions)** defined in the compilation unit, as well as the external symbols (**with “external linkage”**) referenced in the compilation unit. When the compiler encounters a symbol declaration, it stores that **symbol** and its **attributes** in the symbol table of the object file.

For the example code given before, we have the following:
- **In one.c**, there are four global symbol definitions: global variables c, sa, and sb, and a function main(). It references two external symbols a and b().

-  **In two.c**, there are four global symbol definitions: global variables a, sb, and sc, and a function b(). It references the external symbol c. The symbol table also has an entry for temp,**which is a local variable with a static storage duration**. An object file’s symbol table holds information that is needed by a compiler/linker to locate and relocate a program’s symbolic definitions and references.

**An object file’s symbol table holds information that is needed by a compiler/linker to locate and relocate a program’s symbolic definitions and references.**
### **Linker**
