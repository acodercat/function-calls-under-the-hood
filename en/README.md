# Function Calls Under the Hood

**What happens when you call a function?**

A visual guide to function call principles through assembly language.

This visual guide explains how function calls work at the low level, using detailed diagrams to illustrate the complete stack evolution during function execution. Through hands-on examples on the x86-64 platform, you'll gain a solid understanding of how C code translates to assembly instructions. The guide concludes with practical examples demonstrating interesting stack frame behaviors.

## Table of Contents

* [Prerequisites](content/pre-knowledge.md)
* [The Stack in Memory](content/stack-in-memory.md)
* [Control Transfer](content/control-transfer.md)
* [Data Passing](content/parameter-passing.md)
* [Register Saving and Restoring](content/register-conventions.md)
* [Storage of Local Variables](content/local-variables.md)
* [Function Stack Frame](content/function-stack-frame.md)
* [C Function Stack Frame Example](content/assembly-walkthrough.md)
* [Stack Frame Memory Reuse](content/memory-reuse.md)
* [Manually Modifying Stack Frame Data](content/buffer-overflow.md)
* [Summary](content/summary.md)

## What You Will Learn

* How control transfers between functions
* How data is passed between functions
* How stack frames are created and destroyed
* The dangers of array out-of-bounds access in C/C++
* Why inline functions are more efficient
* Why loops are preferred over recursion
* Why static variables persist after function returns
* What stack overflow attacks are and how they work

## Notes

This guide does not cover `memory alignment`, as it falls outside the scope of function calls. The examples were carefully constructed to avoid compiler-generated alignment instructions. If you're interested in memory alignment, there are many online resources available.

## References

* Books:
  * "Computer Systems: A Programmer's Perspective" (3rd Edition)

* Online Courses:
  * ["Programming Paradigms" (Stanford CS107)](https://see.stanford.edu/course/cs107)

## Acknowledgments

All diagrams were created with [Excalidraw](https://excalidraw.com/).
