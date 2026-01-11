# Storage of Local Variables

> Both registers and memory can be used to store `data` needed during `function execution`. Registers have much faster access speeds than memory, so data is usually stored in registers first. However, since the number of registers is limited, when registers are insufficient, data is stored in `stack` memory.

**We can move the `stack pointer` (sp) toward the `top of the stack` to allocate memory space in the stack for storing `local data` for the function.**

For example, consider the following C code:

```c
void main() {
    long foo = 100;
    long bar = 200;
}
```

The code defines two `long` type variables `foo` and `bar`. We assume they are both stored on the `stack`. Since the `long` type occupies 8 bytes, we need to allocate 16 bytes of space on the stack.

Allocation process (each cell is 8 bytes):

![](../../images/stack13.png)



Finally, store these two variables in the stack:

![](../../images/stack14.png)
