# Stack Frame Memory Reuse



Here's a C language example:

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

In the code, `init_array` and `print_array` each have a local array `arr` of length 10. `init_array` initializes its internal `arr` to 0~9, `print_array` iterates through its internal `arr` and outputs to the console, and then they are called in the `main` function.

Output to console after `print_array` executes:

```bash
0	1	2	3	4	5	6	7	8	9
```

The result is a bit puzzling - it seems these two functions have some kind of relationship.

Now let's remove the call to `init_array` and see the result:

```bash
177988	32765	5497827	21857	2157688	32518	9257904	21857	0	0
```

This time it outputs random values, because C/C++ doesn't initialize memory values - these values were left behind by the previous program that used this memory.

Comparing the two results, we can see that `init_array` did indeed affect the array `arr` in `print_array`. The `arr` in `init_array` and `print_array` appear to be independent of each other, so why does this happen?

Let's look at the stack frame structure of these two functions before they return:

![](../../images/stack29.png)

We can see that their stack frame structures and sizes are exactly the same. After `init_array` finishes executing, its stack frame is destroyed, but the original values remain in memory. When `print_array` executes, it's allocated the same block of memory and has the same stack frame structure, so it picks up the data left behind by `init_array` in memory.

Let's slightly modify the code to have both functions output their respective `arr` memory addresses to the console:

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

Console result:

```bash
0x7ffd2aaf3ff0
0x7ffd2aaf3ff0
```

The result once again confirms that these two `arr` arrays were placed in the same stack memory. So `print_array` outputs the values that were originally in `arr` from `init_array`.
