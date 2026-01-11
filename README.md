# Function Calls Under the Hood

**What happens when you call a function?**

A visual guide to function call principles through assembly language.

[English](en/README.md) | [中文](zh/README.md)

---

## About This Guide

This visual guide explains how function calls work at the low level, using detailed diagrams to illustrate the complete stack evolution during function execution. Through hands-on examples on the x86-64 platform, you'll gain a solid understanding of how C code translates to assembly instructions. The guide concludes with practical examples demonstrating interesting stack frame behaviors.

### What You Will Learn

- How control transfers between functions
- How data is passed between functions
- How stack frames are created and destroyed
- The dangers of array out-of-bounds access in C/C++
- Why inline functions are more efficient
- Why loops are preferred over recursion
- Why static variables persist after function returns
- What stack overflow attacks are and how they work


## Table of Contents

1. [Prerequisites](en/content/pre-knowledge.md)
2. [The Stack in Memory](en/content/stack-in-memory.md)
3. [Control Transfer](en/content/control-transfer.md)
4. [Data Passing](en/content/parameter-passing.md)
5. [Register Saving and Restoring](en/content/register-conventions.md)
6. [Storage of Local Variables](en/content/local-variables.md)
7. [Function Stack Frame](en/content/function-stack-frame.md)
8. [C Function Stack Frame Example](en/content/assembly-walkthrough.md)
9. [Stack Frame Memory Reuse](en/content/memory-reuse.md)
10. [Manually Modifying Stack Frame Data](en/content/buffer-overflow.md)
11. [Summary](en/content/summary.md)

## References

- Books:
  - "Computer Systems: A Programmer's Perspective" (3rd Edition)

- Online Courses:
  - ["Programming Paradigms" (Stanford CS107)](https://see.stanford.edu/course/cs107)

## Acknowledgments

All diagrams were created with [Excalidraw](https://excalidraw.com/).

## License

MIT
