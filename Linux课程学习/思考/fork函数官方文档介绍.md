`fork : 叉,分支`
![[Pasted image 20251029170611.png]]
* 创建一个子进程
`函数声明`
pid_t fork(void);
`描述`
![[Pasted image 20251029170847.png]]
* 通过**复制**调用进程的方式创建一个新的进程, 调用进程作为父进程,新创建的进程是子进程
* 父子进程在两个独立的内存空间中运行(互不影响),子进程和父进程中的内容是相同的

`return value`
注:父子进程共享的内容是相同的, 在fork函数中,首先创建子进程,子进程也会继承fork函数剩余的代码,所以也要接受返回值, 所以,fork函数调用一次会返回两个值,一个返回给calling process, 一个返回给child process
![[Pasted image 20251029171026.png]]
* 成功 : calling process -- the pid of the new created child process's pid     
		childprocess -- 0
* 失败 : calling process -- 0