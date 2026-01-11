# Function Stack Frame

> When a function is running, a `stack frame` needs to be created for it in the `stack` to record information generated during runtime. Therefore, each function creates a stack frame before execution and destroys it when returning.

**In this section's diagrams, we use colors to distinguish between the caller's and callee's stack frames: blue represents the callee, and green represents the caller.**

## Creating a Stack Frame

Typically, a register called the `base pointer` (bp) is used to store the `starting address` of the currently running function's stack frame. Since the `stack pointer` (sp) always stores the address of the top of the stack, the `stack pointer` also stores the `ending address` of the currently running function's stack frame.

![](../../images/stack3.png)

> Each time a function call occurs, the `base pointer` (bp) must be modified to store the new stack frame's `starting address`, which will cause it to be overwritten. Therefore, we can use the stack to save and restore the `base pointer` as discussed in `Register Saving and Restoring`.



Initially, the `base pointer` and `stack pointer` point to the caller's stack frame's `starting address` and `ending address` respectively. When creating the frame, first push the caller's stack frame's `starting address` (which is the current `base pointer`) onto the stack to save it. Since the `base pointer` is a `callee-saved` register, it is stored in the callee's stack frame.

Stack change process:

![](../../images/stack18.png)



Then modify the `base pointer` (bp) to the current value of the `stack pointer` (sp), making them both point to the same location (left diagram below). If the called function needs more stack space, it can continue moving the `stack pointer` (sp) toward lower addresses to allocate space (right diagram below). Finally, the `base pointer` and `stack pointer` again point to the `callee's` stack frame's `starting address` and `ending address` respectively.

Stack change process:

![](../../images/stack19.png)



The stack frame stores function arguments, return address, saved registers, and local variables. A complete stack structure might look like this:



![](../../images/stack20.png)

**Explanation of each part in the diagram:**

* Arguments

  In `X64`, if the function has more than 6 parameters, the first 6 are passed through registers, and the rest are passed through the stack. When there are 6 or fewer parameters, or no parameters, this part of the stack frame can be ignored.

  When parameters need to be passed through the stack, the `calling function` first pushes the parameters onto its own stack frame, and then the `called function` accesses the parameters from the `calling function's` stack frame. That's why the arguments section is in the caller's stack frame in the diagram.

* Return Address (ret addr)

  After pushing function arguments onto the stack, the address of the next instruction at the call site needs to be pushed, so the called function can return to the original location to continue execution after finishing. This address is the return address.

* Saved Registers (saved regs)

  This stores registers that need to be saved by the `callee`, such as the `old base pointer` (old bp).

* Local Variables (local vars)

  This part stores local variables in the stack rather than registers. If the function has no local variables or all local variables are stored in registers, this part of the stack frame can be ignored.



**If another function call occurs, the entire stack frame creation process repeats, so recursive functions are no different from regular functions.**

> **Stack Alignment Requirement**: In X64, the stack pointer must be 16-byte aligned during function calls (before the `call` instruction executes, rsp must be a multiple of 16). This is because certain SSE instructions require 16-byte aligned operands. The compiler automatically inserts padding to meet alignment requirements, so sometimes you'll see unused space in the stack frame.



## Destroying a Stack Frame

> When a function returns, the stack frame previously created for it is `destroyed` to release space.

When destroying, first move the `stack pointer` (sp) to the current `base pointer` (bp) position. At this point, the `stack pointer` and `base pointer` both point to the same location.

Stack change process:

![](../../images/stack5.png)



Now the top of the stack happens to be the caller's `stack frame's` `base pointer` that we saved when creating the stack frame. Pop it into the `base pointer` (bp) to get the stack structure shown below:

![](../../images/stack7.png)

At this point, the `callee's` stack frame has been destroyed and the space released. However, the function return process is not complete. The `caller's` stack frame still holds the `return address`. Now the `return address` needs to be popped into the `Program Counter` (PC) to resume execution at the original location. The stack frame after returning:

![](../../images/stack6.png)





**In C/C++, destroying a stack frame does not clear the data in the destroyed stack frame.**
