# Manually Modifying Stack Frame Data

> We can get the memory address of a local variable and then offset that address to modify data at other locations in the stack. As long as we understand the stack structure well enough, we can modify whatever data we want.



## Modifying Local Storage

> Since C/C++ doesn't check for array out-of-bounds access, we can use this feature to modify data in the stack frame.



Here is the code:

```c
int main() {
    long foo = 10;
    long arr[2] = {1, 2};
    arr[-1] = 20;
    printf("foo is %ld", foo);
    return 0;
}
```

In the code, the array `arr` and variable `foo` are stored contiguously in the stack:

![](../../images/stack30.png)

The diagram also shows that variable `foo` is placed at a lower address, so when modifying `arr[-1]`, we're also modifying variable `foo`.

Console output:

```bash
foo is 20
```



### Stack Canary

In this example's stack frame, there's an extra `canary` at the bottom, which is used to prevent stack overflow attacks. When entering a function, an arbitrary value is first placed above the `return address`, and then when returning, this value is checked to see if it has been modified. If it has been modified, it indicates that other data in the stack frame may also have been modified, and a related exception is triggered. This value is called a `canary`.

The compiler only generates `canary`-related instructions when there are pointer operations within the function. We use an array here, and array operations in C/C++ are actually pointer operations, so a `canary` is generated here.



## Modifying the Return Address

> Similarly, we can offset a memory address appropriately to modify a function's return address, thereby controlling the function's return path.



Here is the code:

```c
void bar() {
    printf("This is bar!\n");
}

void foo() {
    printf("This is foo!\n");
    long arr[2] = {1, 2};
    arr[5] = bar;
}

int main() {
    foo();
    return 0;
}
```

In this example, the return address is stored at position `arr[5]`. To understand why it's 5, let's first look at the stack frame structure before `foo` returns (green is main's stack frame, blue is foo's stack frame):

![](../../images/stack31.png)

Now counting down from `arr[0]`, the function return address happens to be at position `arr[5]`. We modify it to the address of function `bar` (in C/C++, a function name represents the address of the function's first instruction) and then execute. We get the following console output:

```bash
This is foo!
This is bar!
Segmentation fault (core dumped)
```

As expected, we successfully jumped to `bar`, but we also got a `segmentation fault`. Why does this `segmentation fault` occur? Let's look at the stack frame changes before and after `bar` runs (green is main's stack frame, blue is bar's stack frame):

![](../../images/stack32.png)



The left diagram above shows the stack frame state before the `ret` instruction executes. At this point, the `stack pointer` and `base pointer` have been restored to the state before calling `foo`. The right diagram shows after the `ret` instruction executes - at this point `bar addr` has been popped from the stack, then jumped to `bar` for execution, and `bar` has also created its own stack frame. Now let's imagine what happens when `bar` returns. Obviously, it will use `_start rbp` as the `return address`. `_start rbp` is not an instruction address, so when the CPU treats it as an instruction address and tries to find an instruction, a `segmentation fault` occurs.



**In both examples, we modified stack frame data but no exceptions occurred - the `canary` seems to have not worked. The reason no exception was triggered is because I skipped the `canary` and didn't modify it. The `canary` can prevent modification of a contiguous block of space; if that space happens to contain the `canary`, an exception will be triggered.**



**These two examples also demonstrate that array out-of-bounds access can lead to very serious bugs**
