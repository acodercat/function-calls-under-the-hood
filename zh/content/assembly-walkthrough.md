# C语言函数栈帧实例

> 我们通过把一段具有函数调用的C语言代码编译成汇编，逐步分析函数栈帧生命周期的完整变化过程。



有如下C语言代码：

```c
long callee(long arg1, long arg2, long arg3, long arg4, long arg5, long arg6, long arg7, long arg8) {
    return arg7 + arg8;
}

int main() {
    long a = 7;
    long b = 8;
    callee(1, 2, 3, 4 ,5 ,6, a, b);
    return 0;
}
```

代码中`callee`函数有8个参数分别是`arg1~arg8`，它返回`arg7`和`arg8`相加后的结果。由于我们的代码是运行在`X64`的机器上，所以`arg1~arg6`会通过寄存器来传递，`arg7`和`arg8`通过栈来传递。`main`中定义了两个局部变量`a`和`b`，它对`callee`发起调用，局部变量`a,b`分别对应`callee`函数的`arg7,arg8`。

将上面C语言代码编译成汇编，把由编译器产生的其他与我们分析函数栈帧不相关的指令删除，得到如下指令（根据编译器版本以及操作系统的不同编译出的汇编指令会有所差异，这里使用的编译器和操作系统分别是`gcc9.0`和`ubuntu 20.04 x-64`）：

```ASM
callee:
    pushq	%rbp
    movq	%rsp, %rbp
    movq	%rdi, -8(%rbp)
    movq	%rsi, -16(%rbp)
    movq	%rdx, -24(%rbp)
    movq	%rcx, -32(%rbp)
    movq	%r8, -40(%rbp)
    movq	%r9, -48(%rbp)
    movq	16(%rbp), %rdx
    movq	24(%rbp), %rax
    addq	%rdx, %rax
    popq	%rbp
    ret
main:
    pushq	%rbp
    movq	%rsp, %rbp
    subq	$16, %rsp
    movq	$7, -16(%rbp)
    movq	$8, -8(%rbp)
    pushq	-8(%rbp)
    pushq	-16(%rbp)
    movl	$6, %r9d
    movl	$5, %r8d
    movl	$4, %ecx
    movl	$3, %edx
    movl	$2, %esi
    movl	$1, %edi
    call	callee
    addq	$16, %rsp
    movl	$0, %eax
    leave
    ret
```

指令中的`callee:`和`main:`表示的是为它下面的指令取一个名字，可以理解为函数名。

**在`X64`中`栈指针`和`栈基址`分别叫`rsp,rbp`。**

我们逐步分析这些指令对堆栈的影响，首先是`main`的前两行指令：

```asm
pushq	%rbp
movq	%rsp, %rbp
```

实际上`main`函数是被系统内一个叫做`_start`的函数所调用，所以第1条指令将`调用者`_start的`栈基址`压栈保存，第2条指令移动`栈基址`使它指向和`栈指针`同样的位置。

栈变化过程（在一开始我们`栈指针`和`栈基址`指向`_start`的栈帧，这里没有画出`_start`的栈帧也就没有标注它们）：

![](../../images/stack22.png)



初始化局部变量并准备`callee`的后两个参数：

```asm
subq	$16, %rsp
movq	$7, -16(%rbp)
movq	$8, -8(%rbp)
pushq	-8(%rbp)
pushq	-16(%rbp)
```

第1条指令中的`subq`是减法指令，这里用于把`栈指针`减去16，使它向低地址移动来给局部变量`a,b`分配16字节的空间（long占8个字节）。

第2~3条指令中的`movq`是数据移动指令，在这里它通过对`栈基址`进行适当偏移来对`a,b`赋值。例如`movq $7, -16(%rbp)`表示把7放到相对于`栈基址`-16的内存中。下图中可以看到这两条指令执行完以后`-16`和`-8`的位置刚好放置了变量`a,b`。

第4~5条指令把栈帧中`a,b`的值复制一份然后把它们压栈，这是为了准备函数`callee`的后2个参数`arg7`与`arg8`。

栈变化过程（图中右侧的数字表示基于`栈基址`的偏移量，每个格子8字节，由于栈空间是向低地址发展的，所以相对于`栈基址`上面内存的偏移量是负的）：

![](../../images/stack23.png)





把`callee`的前6个参数`arg1~arg6`存放在寄存器中：

```asm
movl	$6, %r9d
movl	$5, %r8d
movl	$4, %ecx
movl	$3, %edx
movl	$2, %esi
movl	$1, %edi
```


开始调用`callee`：

```asm
call	callee
```

这条指令对应了两个操作，首先将它下面一条指令的地址也就是`addq $16, %rsp`的地址（返回地址）压入栈中，随后修改`程序计数器`（PC）为`callee`的第一条指令的地址，最后CPU就从`callee`处开始执行。

这条指令执行后的栈：

![](../../images/stack24.png)

现在CPU从`callee`开始执行：

```asm
pushq	%rbp
movq	%rsp, %rbp
```

先压入`main`栈帧的`栈基址`，然后移动`栈基址`使它指向和`栈指针`同样的位置。此时产生了`callee`的栈帧：

第2条指令使`栈基址`发生了改变，相应的右图中基于`栈基址`的偏移量也需要发生变化（由于栈空间是向低地址发展的，所以相对于`栈基址`下面内存的偏移量是正的）：

![](../../images/stack25.png)



复制前6个参数到栈帧中：

```asm
movq	%rdi, -8(%rbp)
movq	%rsi, -16(%rbp)
movq	%rdx, -24(%rbp)
movq	%rcx, -32(%rbp)
movq	%r8, -40(%rbp)
movq	%r9, -48(%rbp)
```

这6个寄存器`rdi,rsi,rdx,rcx,r8,r9`分别存放了`callee`的前6个参数，现在将它们从寄存器中复制到`callee`自己的栈帧中。编译器这样做有几个原因：(1) 函数体内可能需要获取参数的地址（如`&arg1`），而寄存器没有内存地址；(2) 寄存器数量有限，复杂函数需要腾出寄存器给其他计算使用；(3) 这里未启用编译器优化（`-O0`），启用优化后这些冗余操作会被消除。

执行之后的栈结构：

![](../../images/stack26.png)

执行到这里奇怪的事情发生了，`栈指针`并没有继续向低地址移动，让它指向栈顶的位置。这其实与X64 ABI中的**红区（Red Zone）**概念有关。

> **红区（Red Zone）**：在X64 System V ABI中，栈指针下方的128字节被称为"红区"。叶子函数（不调用其他函数的函数）可以使用这块区域存储临时数据，而无需移动栈指针。这样可以节省`subq`和`addq`指令，提高效率。但如果函数调用了其他函数，红区的数据可能会被覆盖，所以只有叶子函数才能使用红区。

由于`callee`没有再继续调用其他任何函数（是一个叶子函数），因此可以直接使用红区而无需修改`栈指针`。

执行相加：

```asm
movq    16(%rbp), %rdx
movq	24(%rbp), %rax
addq	%rdx, %rax
```

将`arg7`与`arg8`相加，然后把结果放在`rax`寄存器，`main`函数可以访问`rax`寄存器来获取返回值，以此来达到函数返回值传递的目的。



返回到`main`：

```asm
popq	%rbp
ret
```

恢复`栈基址`并跳转到返回地址处开始继续执行。

栈变化过程：

![](../../images/stack27.png)





返回到`_start`：

```asm
addq	$16, %rsp
movl	$0, %eax
leave
ret
```

第1条指令中的`addq`是加法指令，这里用于把`栈指针`加上16，使它向高地址移动以此来释放`arg7,arg8`的内存。通过这一步可以发现，函数执行完后会立马释放参数的栈内存。

第2条指令把`main`的返回值0放入到`eax`寄存器中。

第3条指令`leave`隐含执行了两个操作，它等价与下面两条指令：

```asm
movq	%rbp, %rsp
popq	%rbp
```

先修改`栈指针`使它与`栈基址`指向同样的位置，这一步主要用于释放局部空间，然后恢复`栈基址`。此时`栈指针`和`栈基址`就分别指向了`_start`的栈帧。

到这里我们发现`main`和`callee`的返回过程不一样，这是由于在`callee`一开始没有通过`subq`指令修改`栈指针`来分配局部空间，所以返回的时候不需要通过`leave`指令来恢复`栈指针`。由此我们可以得出一个结论：如果某个函数调用了其他函数和没有调用其他函数，它们在返回时的过程有略微的不一致。

第4条指令`ret`使CPU返回到`_start`中去执行，最后一切又恢复了平静。

栈变化过程：

![](../../images/stack28.png)



**最后我们发现数据仍然还保存在栈中，因为释放栈空间并不会修改里面的数据，只是对`栈指针`和`栈基址`做了移动。**
