# Register Saving and Restoring

> During a `function call`, it's possible that the `caller` uses a certain register, and the `callee` also uses this register and modifies its value. If the caller still needs to use the previous value in this register after the call ends, the `caller's` state would be `corrupted`. Therefore, during function calls, the value in this register needs to be saved, and restored to its `pre-call state` when the function returns.

Since the `stack` data structure has a `memory feature` - when you push an element onto the stack and then `pop`, the `popped` element is still the element you previously pushed - the `stack` is commonly used for `saving and restoring` registers. First, push the register values onto the `stack` to save them, then modify them, and finally restore the previously saved values from the `stack` back to the registers one by one.

The following code saves the `ax` and `bx` registers onto the stack:

```c
push(ax);
push(bx);
```



Saving process:

![](../../images/stack11.png)



Restore the values from the stack back to the registers one by one:

```c
pop(bx);
pop(ax);
```



Restoring process:

![](../../images/stack12.png)

Note that the pop and push orders are reversed. Since the `top of the stack` now contains the previous value of `bx`, when restoring, we first pop the value at the `top of the stack` into `bx`, then pop the next value into `ax`. This way, the values are matched to the correct registers.



**Not all registers need to be `saved and restored`. By convention, registers are divided into `callee-saved` and `caller-saved`:**

In the X64 System V ABI, registers are classified as follows:

| Type | Registers |
|------|-----------|
| Callee-saved | rbx, rbp, r12, r13, r14, r15 |
| Caller-saved | rax, rcx, rdx, rsi, rdi, r8, r9, r10, r11 |
| Stack pointer | rsp (special, must be preserved) |

* **Callee-saved**

  The `callee` is responsible for `saving and restoring` this set of registers. Since every function is a `callee` (because functions only run after being called), we can conclude that any function modifying these registers must `save and restore` them.

  For example, when function P calls function Q, when Q is executed, Q first pushes the registers from this set that it needs to modify onto the `stack` for saving. Finally, when returning, Q is responsible for popping the saved values from the stack back to these registers for restoration.

* **Caller-saved**

  Registers other than `callee-saved` and the `stack pointer` (sp) are called `caller-saved` registers. Since every function is a `callee`, we can conclude that any function modifying these registers does not need to `save and restore` them.

  For example, when function P calls function Q, before Q is executed, P first pushes the registers from this set that it doesn't want to `change` when Q returns onto the `stack` for saving. Finally, after Q returns, P is responsible for popping the saved values from the stack back to these registers for restoration.
