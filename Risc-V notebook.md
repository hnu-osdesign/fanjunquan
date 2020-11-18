**RISC-V架构中：所有的整数有符号数，均由二进制补码表示**

六种基本指令格式，分别是：

- 用于**寄存器-寄存器**操作的 **R     类型指令** 
- 用于**短立即数和访存     load**操作的**     I 型指令**
- 用于**访存     store**操作的     **S 型指令** 
- 用于**条件跳转**操作的 **B     类型指令** 
- 用于**长立即数**的 **U     型指令** 
- 用于**无条件跳转**的 **J     型指令**

### RI32

⚫ 32 位字节可寻址的地址空间

 ⚫ 所有指令均为 32 位长 

⚫ 31 个寄存器，全部 32 位宽，寄存器 0 硬连线为零

 ⚫ 所有操作都在寄存器之间（没有寄存器到内存的操作） 

⚫ 加载/存储字加上有符号和无符号加载/存储字节和半字

 ⚫ 所有算术，逻辑和移位指令都有立即数版本的指令 

⚫ 立即数总是符号扩展

 ⚫ 仅提供一种数据寻址模式（寄存器+立即数）和 PC 相对分支

 ⚫ 无乘法或除法指令 

 ⚫ 一个指令，用于将大立即数加载到寄存器的高位，这样加载 32 位常量到寄存器只 需要两条指令 

- 
**与ARM对比**

### ARM在指令格式中设定，

```markdown
只能用指令机器码32位中的低12位来表示要操作的常数。ARM处理器是按32位来处理数据的，ARM处理器处理的数据是32位，如果简单的用这12位来表示，显然范围太小了，为了扩展到32位，因此使用了构造的方法，在12位中用8位表示基本数据值，用4位表示位移值，通过用8位基本数据值往右循环移动4位位移值*2次，来表示要操作的常数。这里要强调最终的循环次数是4位位移值乘以2得到的，所以得到的最终循环次数肯定是一个偶数，为什么要乘以2呢，实质还是因为范围不够，4位表示位移次数，最大才15次（移位值==0：等于没有循环，不算），加上8位数据还是不够32位，这样只能通过ALU的内部结构设计将4位位移次数乘以2，这样就能用12位表示32位常数了
```



[**RISC-V 常见指令**](https://www.cnblogs.com/truelycloud/p/10807398.html)

**算术运算**

**add rd, rs1, rs2**

x[rd] = x[rs1] + x[rs2]

把寄存器 x[rs2]加到寄存器 x[rs1]上，结果写入 x[rd]。忽略算术溢出。

 

**addi rd, rs1, immediate**

x[rd] = x[rs1] + sext(immediate)

把符号位扩展的立即数加到寄存器 x[rs1]上，结果写入 x[rd]。忽略算术溢出。

 

**sub rd, rs1, rs2**

x[rd] = x[rs1] − x[rs2]

x[rs1]减去 x[rs2]，结果写入 x[rd]。忽略算术溢出。

 

**div rd, rs1, rs2**

x[rd] = x[rs1] ÷s x[rs2]

用寄存器 x[rs1]的值除以寄存器 x[rs2]的值，向零舍入，将这些数视为二进制补码，把商写 入 x[rd]。

 

**mul rd, rs1, rs2**

x[rd] = x[rs1] × x[rs2]

把寄存器 x[rs2]乘到寄存器 x[rs1]上，乘积写入 x[rd]。忽略算术溢出。

 

**rem rd, rs1, rs2**

x[rd] = x[rs1] %𝑠 x[rs2]

求余数。x[rs1]除以 x[rs2]，向 0 舍入，都视为 2 的补码，余数写入 x[rd]。

 

**neg rd, rs2**

x[rd] = −x[rs2]

把寄存器 x[rs2]的二进制补码写入 x[rd]。

 

**逻辑运算**

**and rd, rs1, rs2**

x[rd] = x[rs1] & x[rs2]

将寄存器 x[rs1]和寄存器 x[rs2]位与的结果写入 x[rd]。

 

**andi rd, rs1, immediate**

x[rd] = x[rs1] & sext(immediate)

把符号位扩展的立即数和寄存器 x[rs1]上的值进行位与，结果写入 x[rd]。

 

**or rd, rs1, rs2**

x[rd] = ~x[rs1]

把寄存器 x[rs1]和寄存器 x[rs2]按位取或，结果写入 x[rd]。

 

**xor rd, rs1, immediate**

x[rd] = x[rs1] ^ sext(immediate)

x[rs1]和有符号扩展的 immediate 按位异或，结果写入 x[rd]。

 

**位运算**

**sll rd, rs1, rs2**

x[rd] = x[rs1] ≪ x[rs2]

逻辑左移（空位补0）

 

**slli rd, rs1, shamt**

立即数逻辑左移

 

**srl rd, rs1, rs2**

x[rd] = (x[rs1] ≫𝑢 x[rs2])

逻辑右移（空位补0）

 

**srli rd, rs1, shamt**

立即数逻辑右移

 

**sra rd, rs1, rs2**

x[rd] = (x[rs1] ≫𝑠 x[rs2])

算术右移（空位用最高位填充）

 

**srai rd, rs1, shamt**

立即数逻辑右移

 

**not td, rs1**

x[rd] = ~x[rs1]

把寄存器 x[rs1]对于 1 的补码（即按位取反的值）写入 x[rd]。实际被扩展为 xori rd, rs1, -1。

 

**条件控制指令**

**beq rs1, rs2, offset**

if (rs1 == rs2) pc = sext(offset)

若寄存器 x[rs1]和寄存器 x[rs2]的值相等，把 pc 的值设为当前值加上符号位扩展的偏移 offset。

 

**bge rs1, rs2, offset**

if (rs1 ≥s rs2) pc = sext(offset)

若寄存器 x[rs1]的值大于等于寄存器 x[rs2]的值（均视为二进制补码），把 pc 的值设为当前 值加上符号位扩展的偏移 offset。

 

**blt rs1, rs2, offset**

if (rs1 <s rs2) pc = sext(offset)

若寄存器 x[rs1]的值小于寄存器 x[rs2]的值（均视为二进制补码），把 pc 的值设为当前值加 上符号位扩展的偏移 offset。

 

**bne rs1, rs2, offset**

if (rs1 ≠ rs2) pc = sext(offset)

若寄存器 x[rs1]和寄存器 x[rs2]的值不相等，把 pc 的值设为当前值加上符号位扩展的偏移 offset。

 

**跳转指令**

**j offset**

pc += sext(offset)

把 pc 设置为当前值加上符号位扩展的 offset，等同于 jal x0, offset。

 

- jal(jump and link )     使用20位立即数（有符号）作为偏移量（offset），该偏移量乘以2，然后与**该指令的PC相加**，生成得到最终的跳转目标地址。**可以跳转到前后1MB的地址区间**。将下一条指令PC（当前PC+4)的值，写入结果寄存器rd中。
            PS:一般，跳转的目标往往使用汇编程序中的label，汇编器根据label自动计算偏移量
- jalr(jump and link     register) ：12位偏移量，加上rs1中的值，得到最终的跳转目标地址。将下一条指令PC（当前PC+4)的值，写入结果寄存器rd中。

 

**ret** 

pc = x[1]

从子过程返回。实际被扩展为 jalr x0, 0(x1)。

 

**加载与存储指令**

**la rd, symbol**

x[rd] = &symbol

将 symbol 的地址加载到 x[rd]中。

 

**li rd, immediate**

x[rd] = immediate

将常量加载到 x[rd]中。

 

**lw rd, offset(rs1)**

x[rd] = sext(M[x[rs1] + sext(offset)][31:0])

从地址 x[rs1] + sign-extend(offset)读取四个字节，写入 x[rd]。

 

**sw rs2, offset(rs1)**

M[x[rs1] + sext(offset) = x[rs2][31: 0]

将 x[rs2]的低位 4 个字节存入内存地址 x[rs1]+sign-extend(offset)。
