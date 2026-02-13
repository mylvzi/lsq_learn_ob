```c
 int execve(const char *pathname, char *const argv[],
                  char *const envp[]);
```

>       execve()  executes  the program referred to by pathname.  This causes the program that is currently being run by the calling process to be replaced with a new program, with newly initialized stack,
       heap, and (initialized and uninitialized) data segments.

* 调用进程正在执行的程序会被替换为另一个需要执行的程序, 伴随着新的初始化堆栈,初始化和未初始化的代码段
* pathname要么是一个二进制的可执行程序,要么是一个带有解释器的脚本
* argv 是传递给新程序的参数数组, 数组的第一个元素必须包含要执行程序的文件名
* envp也是一个字符串数组, 用作新程序的环境变量
![[Pasted image 20251124154602.png]]
* argv和envp都可以从调用进程的**main函数**中获取
>       One  sometimes sees execve() (and the related functions described in exec(3)) described as "executing a new process" (or similar).  This is a highly misleading description: there is no new process;
       many attributes of the calling process remain unchanged (in particular, its PID).  All that execve() does is arrange for an existing process (the calling process) to execute a new program.

* exec系列的函数并不会**执行一个新的进程**,实际上调用exec函数的进程很多属性并没有发生改变, 调用进程仅仅是作为一个**执行进程**去执行一个新的程序