# Control Transfer

> When making a function call, `control` needs to be transferred to the `called function`, and after the called function finishes execution, it needs to return to the original location to continue execution.



We already know that the operating system divides a process's `memory space` into several regions with different functions. In addition to the `stack segment` (also called the stack) mentioned earlier, there is also a region called the `code segment`, which stores the machine instructions needed during the process's execution. When the CPU reads instructions, it needs to know which instruction to execute next. Therefore, the CPU provides a register called the `Program Counter` (PC) to store the memory address of the instruction the CPU is currently executing in the `code segment`.

Since the CPU always gets the instruction address from the `PC`, when a function call occurs, we can modify the `PC` to the `starting address` of the `callee's` instructions, and the CPU will start executing from the `callee`. After the `callee` finishes execution, to let the CPU continue executing from the original location, we can modify the `PC` again to the address of the `next instruction` after the call site. This address is called the function's `return address`.

Consider the following C code:

```c
void Q() {
    printf("this is Q.");
    return;
}

void P() {
    printf("readying to call Q.");
    Q();
    return;
}
```

Let's assume the `line number` is the `instruction address`. Initially, PC is 7, which is function `P`'s starting instruction address. As the CPU executes downward and encounters a function call, it modifies PC to the starting instruction address 2 of the called function `Q`. After `Q` finishes execution, PC is modified to the address 9 of the next instruction after calling `Q` in `P`, and the entire call process ends.



Now there's a problem with saving the function's `return address`. Consider when there are many nested function calls - each function call generates a return address, and these return addresses need to be associated with each call. To meet this requirement, we need to use a `stack` to store the function's `return addresses`. Each time a call occurs, the `return address` is pushed onto the `stack`, and after the function finishes execution, it is popped from the stack into the `PC`.

Here's an example of nested function calls in C. Let's explain both the call and return processes:

```c
void Q() {
    printf("this is Q.\n");
    return;
}

void P() {
    printf("readying to call Q.\n");
    Q();
    return;
}

void main() {
    printf("readying to call P.\n");
    P();
    return;
}
```

**Call Process:**

Again assuming the `line number` is each instruction's address, initially PC is 13, which is the first line of the `main` function. The program continues to line 14, where it calls function `P`. First, the address 15 of the next instruction after the call to `P` is pushed onto the stack, then `PC` is set to P's starting instruction address 7. The program continues to line 8, where it calls function `Q`. Similarly, first the address 9 of the next instruction after calling `Q` is pushed onto the stack, then PC is set to `Q`'s starting instruction address 2. At this point, the stack stores two `return addresses`: 9 and 15.

Stack change process:

![](../../images/stack8.png)





**Return Process:**

After function `Q` finishes execution, the function begins to return. When returning, the previously saved `return address` is popped from the stack into PC. At this point, the top of the stack is instruction address 9, which is popped from the stack into PC, and the function successfully returns to `P`. After `P` finishes execution, instruction address 15 at the top of the stack is popped into PC, and finally the function returns to `main`, with the stack restored to its state before the calls.

Stack change process:

![](../../images/stack9.png)





This entire process can be extended to any level of function calls and recursive calls - the stack perfectly preserves function return addresses. For convenience in this example, we treated one line of C code as one instruction. In reality, instructions are `machine instructions` after compilation, and one line of C code may correspond to multiple machine instructions, each with its own address in memory.
