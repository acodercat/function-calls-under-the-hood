# Prerequisites

> This section provides a brief introduction to some fundamental concepts to help you better understand the following content.



## Function Definition

> A function in computing (sometimes called a procedure or method) provides a way to `encapsulate code`, implementing certain functionality with a set of specified parameters and an optional return value. This function can then be called from different places in the program. The `caller` can pass a set of parameters to the `callee`, and after execution, the `callee` can return the result to the `caller`.

When making a function call, the computer needs to provide the following mechanisms at the low level:

* Control Transfer

  The `control` needs to be transferred to the called function, and after the called function `finishes execution`, it needs to return to the original location to continue execution.

* Data Passing

  The caller can `pass parameters` to the callee, and the callee can also `return data` to the caller.

* Memory Allocation and Deallocation

  At the beginning, `local memory` may need to be allocated for the callee to store `local variables`, and this memory must be deallocated when the function returns.



## Stack

A stack is a data structure that can be understood as a container for data. It has the `First In, Last Out` (FILO, also equivalent to Last In, First Out - LIFO) property, meaning the data that enters the `stack` first will come out last. You can imagine it as a pile of books on your desk - you can only take the top book, and new books can only be placed on top. Or you can think of it like the clothes you wear - the first clothes you put on are the last ones you take off. Therefore, there are two important operations for a `stack`: `push` and `pop`. `Push` means adding new data to the top of the stack, and `pop` means removing data from the top of the stack. The stack data structure has a `memory feature`, meaning the popped value is always the most recently pushed value that is still in the stack.



## Memory

Memory is a `contiguous` storage area in a computer, organized as a collection of countless contiguous units, each `byte` (8 binary bits) in size. Each `memory unit` is provided with a unique `memory address` for the computer to easily access a specific memory unit, meaning every byte has a `memory address`. For example, the address of the first byte is 0, the adjacent second byte's address is 1, and so on. We call the end with larger memory addresses the `high address`, and the end with smaller memory addresses the `low address`.



## Assembly Instructions

`Assembly language` is a language that works directly on the machine. Computers can only recognize binary sequences composed of 0s and 1s, so the CPU can only execute `machine instructions` composed of 0s and 1s. Assembly language is a `mnemonic` for machine instructions, making it easier for humans to identify and remember. Each assembly instruction corresponds to one or more machine instructions. In this article, we will use pseudocode similar to C language to describe the stack changes.



## Registers

Inside the `CPU`, there is a set of storage units used to hold small temporary data, called `registers`. Registers have much faster access speeds than memory, speeding up computer program execution through quick data access. When the CPU wants to access data in memory, it must first transfer the data from memory to registers, and then the CPU reads from the registers. Registers are at the top of the memory hierarchy and are the fastest storage the CPU can read and write. Each register has a specific name, and different registers have different purposes.

