# linux 0.11 源码剖析

本项目是对于linux 0.11 源码的剖析，对于每个模块的剖析，详见各个子目录的readme文件；


## linux 0.11目录结构

```
.
├── boot                        （启动相关目录）
│   ├── bootsect.s                            bootsect.s 编译成二进制文件，存放在启动区的第一扇区
│   ├── head.s 
│   └── setup.s
├── fs
│   ├── bitmap.c
│   ├── block_dev.c
│   ├── buffer.c
│   ├── char_dev.c
│   ├── exec.c
│   ├── fcntl.c
│   ├── file_dev.c
│   ├── file_table.c
│   ├── inode.c
│   ├── ioctl.c
│   ├── Makefile
│   ├── namei.c
│   ├── open.c
│   ├── pipe.c
│   ├── read_write.c
│   ├── stat.c
│   ├── super.c
│   └── truncate.c
├── include
│   ├── a.out.h
│   ├── asm
│   │   ├── io.h
│   │   ├── memory.h
│   │   ├── segment.h
│   │   └── system.h
│   ├── const.h
│   ├── ctype.h
│   ├── errno.h
│   ├── fcntl.h
│   ├── linux
│   │   ├── config.h
│   │   ├── fdreg.h
│   │   ├── fs.h
│   │   ├── hdreg.h
│   │   ├── head.h
│   │   ├── kernel.h
│   │   ├── mm.h
│   │   ├── sched.h
│   │   ├── sys.h
│   │   └── tty.h
│   ├── signal.h
│   ├── stdarg.h
│   ├── stddef.h
│   ├── string.h
│   ├── sys
│   │   ├── stat.h
│   │   ├── times.h
│   │   ├── types.h
│   │   ├── utsname.h
│   │   └── wait.h
│   ├── termios.h
│   ├── time.h
│   ├── unistd.h
│   └── utime.h
├── init
│   └── main.c
├── kernel
│   ├── asm.s
│   ├── blk_drv
│   │   ├── blk.h
│   │   ├── floppy.c
│   │   ├── hd.c
│   │   ├── ll_rw_blk.c
│   │   ├── Makefile
│   │   └── ramdisk.c
│   ├── chr_drv
│   │   ├── console.c
│   │   ├── keyboard.S
│   │   ├── Makefile
│   │   ├── rs_io.s
│   │   ├── serial.c
│   │   ├── tty_io.c
│   │   └── tty_ioctl.c
│   ├── exit.c
│   ├── fork.c
│   ├── Makefile
│   ├── math
│   │   ├── Makefile
│   │   └── math_emulate.c
│   ├── mktime.c
│   ├── panic.c
│   ├── printk.c
│   ├── sched.c
│   ├── signal.c
│   ├── sys.c
│   ├── system_call.s
│   ├── traps.c
│   └── vsprintf.c
├── lib
│   ├── close.c
│   ├── ctype.c
│   ├── dup.c
│   ├── errno.c
│   ├── execve.c
│   ├── _exit.c
│   ├── Makefile
│   ├── malloc.c
│   ├── open.c
│   ├── setsid.c
│   ├── string.c
│   ├── wait.c
│   └── write.c
├── mm
│   ├── Makefile
│   ├── memory.c
│   └── page.s      
└── tools
    └── build.c     
```


# 参考资料

[ 哈工大 操作系统 李治军 ](https://www.bilibili.com/video/BV19r4y1b7Aw/?p=1)

[《linux源码趣读》](https://book.douban.com/subject/36573361/)

