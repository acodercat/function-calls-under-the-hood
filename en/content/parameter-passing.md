# Data Passing

> Sometimes the caller needs to `pass parameters` to the callee, and the callee may also need to `return data` to the caller.

## Parameter Passing

By default, in `X64` (Intel 64-bit), the first 6 parameters are stored in a set of designated registers, and the remaining parameters are pushed onto the `stack` in `right-to-left` order. Therefore, the callee can access this set of registers or the `stack` to obtain parameters. In `X86` (Intel 32-bit), all parameters are directly pushed onto the stack in `right-to-left` order, so the callee only needs to access the `stack` to obtain parameters.

In the X64 System V AMD64 ABI, the first 6 integer/pointer parameters are passed using the following registers in order:

| Parameter Order | Register |
|----------------|----------|
| 1st | rdi |
| 2nd | rsi |
| 3rd | rdx |
| 4th | rcx |
| 5th | r8 |
| 6th | r9 |

Additionally, if the called function references the memory address of a parameter, that parameter must also be placed on the stack, because registers don't have memory addresses.

Example of parameter passing in C (using `X64`):

```C
void Q(long arg1, long arg2, long arg3, long arg4, long arg5, long arg6, long arg7, long arg8) {
    printf("this is Q.\n");
    return;
}

void main() {
    printf("readying to call Q.\n");
    Q(1, 2, 3, 4 ,5 ,6 ,7 ,8);
    return;
}
```

In the code above, function `Q` has 8 parameters. The first 6 parameters are passed through registers, and the extra 2 parameters `arg7` and `arg8` are pushed onto the stack in `right-to-left` order, so the push order here is `arg8` first, then `arg7`. We use `long` type here for easier illustration, as it occupies 8 bytes, which corresponds to one cell in the diagram.

Push process:

![](../../images/stack10.png)

## Return Value

A function can have at most one return value, so it's usually placed directly in a register, and then the caller retrieves the return value from this register. In X64, the return value is stored in the `rax` register.
