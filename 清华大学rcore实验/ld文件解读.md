```rust
/* 有关 Linker Script 可以参考：https://sourceware.org/binutils/docs/ld/Scripts.html */

/* 目标架构 */
OUTPUT_ARCH(riscv)

/* 执行入口 */
ENTRY(_start)

/* 数据存放起始地址 */
BASE_ADDRESS = 0x80200000;

SECTIONS
{
    /* . 表示当前地址（location counter） */
    . = BASE_ADDRESS;
/* start 符号表示全部的开始位置 */
kernel_start = .;

text_start = .;

/* .text 字段 */
.text : {
    /* 把 entry 函数放在最前面 */
    *(.text.entry)
    /* 要链接的文件的 .text 字段集中放在这里 */
    *(.text .text.*)
}

    //将所有(*符号代表任意输入文件)输入文件的.text section合并成一个.text section,该section的地址由定位器符号的值指定, .
rodata_start = .;

/* .rodata 字段 */
.rodata : {
    /* 要链接的文件的 .rodata 字段集中放在这里 */
    *(.rodata .rodata.*)
}

data_start = .;
//连接器每读完一个section描述后, 将定位器符号的值*增加*该section的大小. 注意: 
/* .data 字段 */
.data : {
    /* 要链接的文件的 .data 字段集中放在这里 */
    *(.data .data.*)
}

bss_start = .;

/* .bss 字段 */
.bss : {
    /* 要链接的文件的 .bss 字段集中放在这里 */
    *(.sbss .bss .bss.*)
}

/* 结束地址 */
kernel_end = .;
}
```

ENTRY(SYMBOL) :将符号SYMBOL的值设置成入口地址。

入口地址(entry point): 进程执行的第一条用户空间的指令在进程地址空间的地址)

OUTPUT(FILENAME) : 定义输出文件的名字

BASE_ADDRESS = 0x80200000:变量赋值

我们首先使用 OUTPUT_ARCH 指定了架构，随后使用 ENTRY 指定了入口点为 `_start` 函数，即程序第一条被执行的指令所在之处。在这个链接脚本中我们并未看到 `_start` ，回忆上一章，我们为了移除运行时环境依赖，重写了入口 `_start` 。所以，链接脚本宣布整个程序会从那里开始运行。

链接脚本的整体写在 `SECTION{ }` 中，里面有多个形如 `output section: { input section list }` 的语句，每个都描述了整个程序内存布局中的一个输出段 output section 是由各个文件中的哪些输入段 input section 组成的。

我们可以用 `*( )` 来表示将各个文件中所有符合括号内要求的输入段放在当前的位置。而括号内，你可以直接使用段的名字，也可以包含通配符 `*`。

单独的一个 `.` 为**当前地址（Location Counter）**，可以对其赋值来从设置的地址继续向高地址放置各个段。如果不进行赋值的话，则默认各个段会紧挨着向高地址放置。将一个符号赋值为 `.` 则会记录下这个符号的地址。

到这里我们大概看懂了这个链接脚本在做些什么事情。首先是从 BASE_ADDRESS 即 0x80200000 开始向下放置各个段，依次是 .text，.rodata，.data，.stack 和 .bss。同时我们还记录下了每个段的开头和结尾地址，如 .text 段的开头、结尾地址分别就是符号 stext 和 etext 的值，我们接下来会用到。



