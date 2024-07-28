# 打开电源时BIOS都干了什么？

1. x86 PC刚开机时CPU处于实模式，CS=0xFFFF,IP=0x0000
2. 寻址0xFFFF0处（ROM BIOS 映射区）
3. 检查硬件
4. 将磁盘0磁道0扇区读入0x7c00处 （512字节）
5. 设置cs和ip指向0x7c00，开始执行bootsect.s中的内容


# bootsect.s 详解

## 转移阵地，规划内存

操作系统开始从这个汇编代码开始执行，让 ds 这个寄存器里的值变成了 0x07c0

```
BOOTSEG  = 0x07c0			! original address of boot-sector

entry start
start:
	mov	ax,#BOOTSEG
	mov	ds,ax
	...
```

ds 是一个 16 位的段寄存器，具体表示数据段寄存器，在内存寻址时充当段基址的作用。

BIOS 规定把操作系统代码加载到内存 0x7c00，那么里面的各种数据自然就全都被偏移了这么多，所以把数据段寄存器 ds 设置为这个值，方便以基址的方式访问内存里的数据。

![](../../images/image.png)


```
BOOTSEG  = 0x07c0			! original address of boot-sector
INITSEG  = 0x9000			! we move boot here - out of the way

entry start
start:
	mov	ax,#BOOTSEG
	mov	ds,ax
	mov	ax,#INITSEG
	mov	es,ax
	mov	cx,#256
	sub	si,si
	sub	di,di
	rep
	movw
	jmpi	go,INITSEG
go:
	...
	...
```

此时 ds 寄存器的值已经是 0x07c0 了，然后又通过同样的方式将 es 寄存器的值变成 0x9000，接着又把 cx 寄存器的值变成 256;

movw 将 ds:si 处的一个字（2字节）复制到 es:di，重复了256次

因此上面的作用是将内存地址 0x07c00 处开始往后的 512 字节的数据，原封不动复制到 0x90000 处。并且跳转到此处往后再稍稍偏移 go 这个标签所代表的偏移地址处

```
go: mov ax,cs
    mov ds,ax
    mov es,ax
    mov ss,ax
    mov sp,#0xFF00
```

这段代码的直接意思很容易理解，就是把 cs 寄存器的值分别复制给 ds、es 和 ss 寄存器，然后又把 0xFF00 给了 sp 寄存器； sp 寄存器被赋值为了 0xFF00 了，所以目前的栈顶地址就是 ss:sp 所指向的地址 0x9FF00 处

![](../../images/image2.png)


## 加载setup扇区和system扇区

```
load_setup:
	mov	dx,#0x0000		! drive 0, head 0
	mov	cx,#0x0002		! sector 2, track 0
	mov	bx,#0x0200		! address = 512, in INITSEG
	mov	ax,#0x0200+SETUPLEN	! service 2, nr of sectors
	int	0x13			! read it
	jnc	ok_load_setup		! ok - continue
	mov	dx,#0x0000
	mov	ax,#0x0000		! reset the diskette
	int	0x13
	j	load_setup
```

注意 int 0x13 中断发起后，CPU 会通过这个中断号，去寻找对应的中断处理程序的入口地址，并跳转过去执行，逻辑上就相当于执行了一个函数。而 0x13 号中断的处理程序是 BIOS 提前给我们写好的，是读取磁盘的相关功能的函数。

dh=磁头号，dl=驱动器号
ch=柱面号，cl=开始扇区
ah=服务号，al=扇区数
ex:bx=内存地址

因此上面的作用就是将硬盘的第 2 个扇区开始，把数据加载到内存 0x90200 处，共加载 4 个扇区也就是setup

![alt text](../../images/image3.png)

```
ok_load_setup:
	...
	mov	ax,#SYSSEG
	mov	es,ax		! segment of 0x010000
	call	read_it
	...
	jmpi	0,SETUPSEG
```

ok_load_setup的主干逻辑如上，省略了如输出等待字符，选择根设备等等与操作系统核心逻辑无关的代码；

其作用是把从硬盘第 6 个扇区开始往后的 240 个扇区，加载到内存 0x10000 处，最后跳转到setup处执行，也就该执行setup.s中的代码了

![alt text](../../images/image4.png)


## 总结

bootsect.s 的主要任务是将操作系统代码加载到内存，然后跳转到 setup.s 的入口处执行。