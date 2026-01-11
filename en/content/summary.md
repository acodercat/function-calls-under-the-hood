# Summary

> So far we have mastered all the details of function calls. Now let's use this knowledge to answer some function-related questions.



### Why should we prefer loops over recursive calls?

Each function call requires initializing and destroying a stack frame, which incurs additional overhead. Also, when there are too many recursive calls, it will exhaust stack memory, eventually causing a stack overflow.

### Why are inline functions more efficient?

Inline functions are different from regular functions, but for developers, you can treat inline functions almost the same way as regular functions. In the instructions actually compiled by the compiler, the inline function's instructions are just copied into the caller's function, so no stack frame is initialized for it. This avoids the overhead brought by stack frames, and using inline functions can improve execution efficiency.

### Why aren't static variables released after the function returns?

This is because static variables are not in the stack memory region at all - they are stored in a place called the static area. So when the function returns, what's destroyed is the stack memory, which doesn't affect the static area.

### What is a stack overflow attack?

In the previous section, we discussed manually modifying a function's return address. Imagine what would happen if this address was modified by an attacker to the address of their own instructions? At this point, when the function returns, it would successfully switch to executing the attacker's code. This is a very old attack method, and there are now many protection methods, such as stack canary, non-executable stack data, and address space layout randomization (ASLR).

### What are the dangers of C/C++ array out-of-bounds access?

C/C++ doesn't check for array out-of-bounds access. This can cause the out-of-bounds memory you access to contain adjacent other variables, or even important data like function return addresses. If you also modify it, the consequences are hard to imagine, and array out-of-bounds bugs are usually very difficult to debug.

### Why can't you return a pointer to a local variable?

Local variables are stored in the stack frame. After the function returns, the stack frame is destroyed, and that memory may be overwritten by subsequent function calls. The returned pointer points to memory that is already invalid, and accessing it is undefined behavior, which may cause the program to crash or produce unpredictable results.

### What is tail recursion optimization?

When a recursive call is the last operation of a function, the compiler can reuse the current stack frame instead of creating a new one, thereby converting recursion into a loop and avoiding stack overflow. This optimization is called Tail Call Optimization (TCO). It requires optimization level `-O2` or higher to be enabled.

### What is the stack size limit?

The default stack size on Linux is usually 8MB (viewable and modifiable via `ulimit -s`), and Windows defaults to 1MB. Stack size is limited, which is why deep recursion or allocating overly large local arrays causes stack overflow.
