# Function Calls Under the Hood

**当你调用一个函数时，究竟发生了什么？**

从汇编视角深入理解函数调用原理。

本文通过详细的示意图，展示函数调用时栈的完整变化过程。结合 x86-64 平台的具体示例，分析 C 代码如何转换为汇编指令，帮助你理解底层机制。最后通过实践案例，探索栈帧的有趣行为。

## 目录

* [前置知识](content/pre-knowledge.md)
* [内存中的栈](content/stack-in-memory.md)
* [控制转移](content/control-transfer.md)
* [数据传递](content/parameter-passing.md)
* [寄存器的保存与恢复](content/register-conventions.md)
* [局部变量的存储](content/local-variables.md)
* [函数栈帧](content/function-stack-frame.md)
* [C语言函数栈帧实例](content/assembly-walkthrough.md)
* [栈帧内存复用](content/memory-reuse.md)
* [手动修改栈帧数据](content/buffer-overflow.md)
* [总结](content/summary.md)

## 你将学到

* 函数间如何转移控制
* 函数间如何传递数据
* 栈帧的创建与销毁
* C/C++ 数组越界的危害
* 为什么内联函数效率更高
* 为什么循环优于递归
* 为什么静态变量在函数返回后不会被释放
* 什么是栈溢出攻击及其原理

## 补充说明

本文未涉及`内存对齐`概念，因为它不属于函数调用的范畴。在构造示例时，我刻意避免了编译器为内存对齐生成额外指令的情况。如果你对内存对齐感兴趣，可以通过网络资源进一步了解。

## 参考资料

* 书籍：
  * 《深入理解计算机系统》（第三版）
  * 《汇编语言》（王爽 第三版）

* 公开课：
  * [《编程范式》（斯坦福 CS107）](https://see.stanford.edu/course/cs107)

## 致谢

文中所有图片均使用 [Excalidraw](https://excalidraw.com/) 绘制。
