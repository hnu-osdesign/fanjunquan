## 调整内存布局

#### 内存分布:

1.对于普通用户程序来说，代码和数据一般放在低地址空间上的。

2.OS 内核，一般都将其地址空间放在高地址上

#### **程序内存分布图**

![img](https://rcore-os.github.io/rCore-Tutorial-deploy/docs/lab-0/pics/typical-layout.png)

### 链接文件示例



##### ***\*- 1 -\****

ENTRY(SYMBOL) :将符号SYMBOL的值设置成入口地址。


入口地址(entry point): 进程执行的第一条用户空间的指令在进程地址空间的地址)

ld有多种方法设置进程入口地址, 按一下顺序: (编号越前, 优先级越高)
1, ld命令行的-e选项
2, 连接脚本的ENTRY(SYMBOL)命令
3, 如果定义了start符号, 使用start符号值
4, 如果存在.text section, 使用.text section的第一字节的位置值
5, 使用值0

 

术语：把定位器符号

一般就是那个. s
以下脚本将输出文件的text section定位在0x10000, data section定位在0x8000000:
SECTIONS
{
. = 0x10000;
.text : { *(.text) }
. = 0x8000000;
.data : { *(.data) }
.bss : { *(.bss) }
}

解释一下上述的例子:
. = 0x10000 : 把定位器符号置为0x10000 (若不指定, 则该符号的初始值为0).

.text : { *(.text) } : 将所有(*符号代表任意输入文件)输入文件的.text section合并成一个.text section,该section的地址由定位器符号的值指定, 即0x10000.

. = 0x8000000 ：把定位器符号置为0x8000000
.data : { *(.data) } : 将所有输入文件的.text section合并成一个.data section, 该section的地址被置为0x8000000.

.bss : { *(.bss) } : 将所有输入文件的.bss section合并成一个.bss section，该section的地址被置为0x8000000+.data section的大小.

连接器每读完一个section描述后, 将定位器符号的值*增加*该section的大小. 注意: 此处没有考虑对齐约束.



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

rodata_start = .;

/* .rodata 字段 */
.rodata : {
    /* 要链接的文件的 .rodata 字段集中放在这里 */
    *(.rodata .rodata.*)
}

data_start = .;

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
