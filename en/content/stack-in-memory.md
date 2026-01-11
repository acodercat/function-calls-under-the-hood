# The Stack in Memory

> The operating system allocates `memory space` for a `process` (a running application) to store the code and data needed during program execution. Based on different memory usage patterns, memory is divided into several regions with different functions. One of these regions is called the `stack` (in this article specifically referring to the call stack), which has the same `Last In, First Out` property as the stack data structure. The `stack` is used to record information generated during function calls, such as function local variables and parameters. The `stack` mentioned later in this article refers to this memory stack.

The CPU provides instructions for `push` and `pop` operations on stack memory, and there is also a register called the `stack pointer` (sp) that holds the `memory address` of the `top of the stack`. `Stack memory` grows from `high addresses` to `low addresses`, which is a bit counterintuitive - when you `push` new data onto the `stack`, the stack address decreases.

**For convenience, each cell in all stack diagrams in this article represents 8 bytes, and we also assume that push and pop instructions operate on 8 bytes at a time**.

The diagram below shows a `stack` of 80 bytes (each cell in the diagram is 8 bytes), with an address range of 0~79. The `stack pointer` points to the top of the stack:

![](../../images/stack1.png)



**The instructions mentioned later are pseudocode similar to C language, used only to describe the stack changes.**

## Push

When pushing, the `stack pointer` moves toward `lower addresses` (i.e., the stack pointer decreases), and then data is written to the memory starting at the `stack pointer`.

For example, to push the value `123` onto the stack, the corresponding instruction is:

```c
push(123);
```

Its effect is equivalent to these two instructions:

```c
sp = sp - 8;
*sp = 123;
```

Since we assume the push instruction operates on 8 bytes at a time, the first instruction moves `sp` (stack pointer) 8 bytes toward lower addresses. Then the second instruction writes the data to the 8 bytes of memory starting at `sp`. `*sp` represents the memory space pointed to by the `stack pointer`, not the `stack pointer` itself.

Stack change process:

![](../../images/stack15.png)



## Pop

When popping, first retrieve the 8 bytes of data starting at the `stack pointer` (we assume pop operates on 8 bytes at a time), then move the `stack pointer` 8 bytes toward higher addresses (i.e., increase the stack pointer).

To pop the data at the `top of the stack` from the push example into variable `rax`, the corresponding instruction is:

```c
pop(rax);
```

Its effect is equivalent to these two instructions:

```c
rax = *sp;
sp = sp + 8;
```

First, put the 8 bytes of data starting at the `stack pointer` into variable `rax`. Since we pushed 123 in the push example, the data popped is also 123. Finally, move the `stack pointer` 8 bytes toward higher addresses.

Stack change process:

![](../../images/stack16.png)
