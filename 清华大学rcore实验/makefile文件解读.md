```makefile
TARGET      := riscv64imac-unknown-none-elf
MODE        := debug
KERNEL_FILE := target/$(TARGET)/$(MODE)/os
BIN_FILE    := target/$(TARGET)/$(MODE)/kernel.bin

USER_DIR    := ../user
USER_BUILD  := $(USER_DIR)/build
IMG_FILE    := $(USER_BUILD)/disk.img

OBJDUMP     := rust-objdump --arch-name=riscv64
OBJCOPY     := rust-objcopy --binary-architecture=riscv64
#$符号表示引用变量,这里利用变量就是精简后面的命令


```

```makefile
.PHONY: doc kernel build clean qemu run env
uild: $(BIN_FILE)

# 通过 Rust 文件中的注释生成文档
doc:
	@cargo doc --document-private-items

# 编译 kernel
kernel:
	@cargo build

# 生成 kernel 的二进制文件
$(BIN_FILE): kernel
	@$(OBJCOPY) $(KERNEL_FILE) --strip-all -O binary $@

# 查看反汇编结果
asm:
	@$(OBJDUMP) -d $(KERNEL_FILE) | less

# 清理编译出的文件
clean:
	@cargo clean

# 运行 QEMU
qemu: build
	@qemu-system-riscv64 \
    		-machine virt \
    		-nographic \
    		-bios default \
    		-device loader,file=$(BIN_FILE),addr=0x80200000 \
    		-drive file=$(IMG_FILE),format=qcow2,id=sfs \
    		-device virtio-blk-device,drive=sfs

# 一键运行
run: build qemu

# 一键 gdb
debug: build
	@tmux new-session -d \
		"qemu-system-riscv64 -machine virt -nographic -bios default -device loader,file=$(BIN_FILE),addr=0x80200000 \
		-drive file=$(IMG_FILE),format=qcow2,id=sfs -device virtio-blk-device,drive=sfs -s -S" && \
		tmux split-window -h "riscv64-unknown-elf-gdb -ex 'file $(KERNEL_FILE)' -ex 'set arch riscv:rv64' -ex 'target remote localhost:1234'" && \
		tmux -2 attach-session -d
```

```markdown
PHONY解释:
目标并非实际的文件名：只是在显式请求时执行命令的名字。有两种理由需要使用PHONY 目标：避免和同名文件冲突(防止存在和PHONY执行目标的名称相同的文件,虽然在写比较小的makefile工程的时候看不出来.PHONY的好处,但是当面对一些大工程的时候,.PHONY则显得相当有用)，改善性能。
　　如果编写一个规则，并不产生目标文件，则其命令在每次make 该目标时都执行。
　　例如：
　　clean:
　　rm *.o temp
　　因为"rm"命令并不产生"clean"文件，则每次执行"make clean"的时候，该命令都会执行。如果目录中出现了"clean"文件，则规则失效了：没有
  依赖文件，文件"clean"始终是最新的，命令永远不会执行；为避免这个问题，可使用".PHONY"指明该目标。如：
　　.PHONY : clean
　　这样执行"make clean"会无视"clean"文件存在与否。
　　已知phony 目标并非是由其它文件生成的实际文件，make 会跳过隐含规则搜索。这就是声明phony 目标会改善性能的原因，即使你并不担心实际文件存在与否。
　　完整的例子如下：
　　.PHONY : clean
　　clean :
　　rm *.o temp
　　phony 目标不应是真正目标文件的依赖。如果这样，每次make 在更新此文件时，命令都会执行。只要phony 目标不是真正目标的依赖，规则的命令只有在指定此目标时才执行。
```

