# 栈帧内存复用



这是一个C语言的例子：

```c
void init_array() {
    int arr[10];
    for (int i = 0; i < 10; ++i) {
        arr[i] = i;
    }
}

void print_array() {
    int arr[10];
    for (int i = 0; i < 10; ++i) {
       printf("%d\t", arr[i]);
    }
}

int main() {
    init_array();
    print_array();
}
```

代码中`init_array`和`print_array`各自有一个长度为10的局部数组`arr`。`init_array`把它内部的`arr`初始化成0~9，print_array把它内部的`arr`遍历输出到控制台，然后在`main`函数中对它们进行调用。

`print_array`执行后输出到控制台：

```bash
0	1	2	3	4	5	6	7	8	9
```

结果有点让人疑惑，似乎这两个函数有着某种关系。

现在取消对`init_array`的调用，再看下结果：

```bash
177988	32765	5497827	21857	2157688	32518	9257904	21857	0	0
```

这次输出了随机值，因为C/C++并不会初始化内存中的值，这些值都是上一次使用这些内存的程序留下的。

对比两次结果发现`init_array`确实对`print_array`中的数组`arr`产生了影响。`init_array`和`print_array`中的`arr`看起来都各自独立，为什么会出现这种情况？

来看下这两个函数各自在返回前的栈帧结构：

![](../../images/stack29.png)

可以发现它们的栈帧结构和大小完全一样。`init_array`执行完之后它的栈帧被销毁，但是原来的值还在内存中。当`print_array`执行时继续给它分配了同一块内存且栈帧结构也一样，这样就把`init_array`在内存中遗留的数据拿到了。

我们对代码稍作修改，让这两个函数都把各自`arr`在内存中的地址输出到控制台：

```c
void init_array() {
    int arr[10];
    printf("%p\n", arr);
}

void print_array() {
    int arr[10];
    printf("%p", arr);
}

int main() {
    init_array();
    print_array();
}    
```

控制台结果：

```bash
0x7ffd2aaf3ff0
0x7ffd2aaf3ff0
```

结果再一次印证了这两个`arr`被放到了是同一块栈内存中。所以`print_array`输出了原先在`init_array`中`arr`的值。