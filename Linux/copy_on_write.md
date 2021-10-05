# COW (Linux Copy-on-Write)

Linux写时拷贝技术：在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，处于效率考虑，Linux引入了写时复制技术，只有进程空间中的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

[Linux COW](https://www.cnblogs.com/lengender-12/p/7054896.html)

