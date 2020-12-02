#     		       实验 #1 (实现最小内核)

|   班级   | 姓名   | 学号         |
| :------: | ------ | ------------ |
| 智能1802 | 范峻铨 | 201808010809 |



# 概要说明

本实验分为三个部分：第一部分安装必要的工具链；第二部分编写裸机程序（独立式可执行程 序）；第三部分构建最小的”内核“系统。 由于我们的目标是编写一个操作系统，所以我们需要创建一个独立于操作系统的可执行程序， 又称独立式可执行程序（freestanding executable）或裸机程序（bare-metal executable）。这意味着 所有依赖于操作系统的库我们都不能使用。比如 std 中的大部分内容（io, thread, file system, etc.） 都需要操作系统的支持，所以这部分内容我们不能使用。 但是，不依赖与操作系统的 rust 的语言特性我们还是可以继续使用的，比如：迭代器、模式匹 配、字符串格式化、所有权系统等。这使得 rust 依旧可以作为一个功能强大的高级语言，帮助我们 编写操作系统。

# 一.搭建rust基本环境

### (1)RUST 安装

步骤1：下载Rust，输入curl https://sh.rustup.rs -sSf | sh>

问题1.1:未安装curl

解决方法1-1：输入sudo apt install curl安装curl



###  (2)curl是什么

curl是一个利用URL语法在命令行下工作的文件传输工具，1997年首次发行。它支持文件上传和下载，所以是综合传输工具，但按传统，习惯称cURL为下载工具。cURL还包含了用于程序开发的libcurl。

![计算机生成了可选文字: fanjunquan@fanjunquan-virtual-machine：·/桌面/OS fanjunquan@fanjunquan-virtual-machine:—/桌面/0S$curlhttps://sh.rustup.rs 序“。“"尚耒安装。您可以使用以下命令安装： -sSf sudOaptinstallcurl fanjunquan@fanjunquan-virtual-machine． 谳的密成 正在分析软件包的依赖关系树 正在读取状态信息 完成 级0 fanjunquan@fanjunquan-virtual-machine． ·/桌面/OSSsud0aptinstall 升级了o个软件包，新安装了o个软件包，要卸载o个软件包，有267个软件包耒被升 桌面/OSS0](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144140885-1745326538.png)

![计算机生成了可选文字: Currentinstallationoptions: defaulthosttriple:x86_64-unknown-Iinux-gnu defaulttoolchatn:stable(default) profile:default modifyPATHvariable:yes 1） 2） 3 Proceedwithinstallation(default) Customizeinstallation Cancelinstallation](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144140593-1538613060.png)

### (3)Cargo 是什么

```
Cargo 是 Rust 的构建系统和包管理器。

Rust 开发者常用 Cargo 来管理 Rust 工程和获取工程所依赖的库。在上个教程中我们曾使用 cargo new greeting 命令创建了一个名为 greeting 的工程，Cargo 新建了一个名为 greeting 的文件夹并在里面部署了一个 Rust 工程最典型的文件结构。这个 greeting 文件夹就是工程本身。

cargo是包管理器，我们可以通过这个工具轻松导入或者发布开源库。官方的管理仓库是：

cargo-fmt是源代码格式化工具

rustc是编译器

rustfmt是源代码格式化工具

rust-lldb是调试器

rls是为编辑器准备的代码提示工具，

rustdoc文档生成器

rust-gdb是调试器

rustup管理这套工具链下载更新的工具。

下载好的软件都在 home/.cargo/bin文件夹下,需要添加路径才可以在本地终端使用
```



 

![计算机生成了可选文字: fanunquan@fanunuan-virtual-machine:、桌面/OS$ 程“、、t。，，尚安。您可以使用以下命零安装． sudOaptinstallrustc fanjunquan@fanjunquan-virtual-machine． PATH >exportPATH="SHONE/.cargo/btn:SPATH fanjunquan@fanjunquan-virtual-machine． rustc1。47。O（18bf6b4f32020-10-07) fanjunquan@fanjunquan-virtual-machine． 桌面/OSS exportPATH;"SHONE/.cargO/btn:S 桌面/OSS ——VerStOn 桌面/OSS0](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144140319-1061559045.png)

### (4)设置环境变量 

1.打开终端并输入：

sudo gedit ~/.bashrc。

2.输入用户密码。这时输入的密码是不可见的。

前面的步骤会打开.bashrc文件，在其末尾添加：

export PATH=/opt/EmbedSky/4.3.3/bin:$PATH

其中/opt/EmbedSky/4.3.3/bin为你自己需要设置的环境变量路径。

![Ubuntu设置环境变量](http://e.hiphotos.baidu.com/exp/w=500/sign=42d319434bed2e73fce9862cb703a16d/54fbb2fb43166d223be3e353472309f79152d265.jpg)

4

使其立即生效，在终端执行：

source ~/.bashrc







### (5)安装nutyly

安装成功



![计算机生成了可选文字: info:installing component'rustfmt' nightly-x86_64-unknown-Iinux-gnuinstalled 020一10一24） info:checkingfo「self-updates 0 fanjunquan@fanjunquan-virtuaI-machine:—Sa rustc1.49.O-ntghtIy(ffa2e7ae82](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144139917-1307258955.png)



默认使用nightly版本

![计算机生成了可选文字: fanjunquan@fanjunquan-virtuaI-machine:—Srustupdefaultnightly info：usingexistinginstallfo「'nightly-x8664-unknown-Itnux-gnu' info：defaulttoolchatnsetto'nightly-x8664-unknown-Itnux-gnu' nightly-x86_64-unknown-Iinux-gnuunchanged rustc1.49.O-ntghtIy(ffa2e7ae82 020一10一24）](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144139622-1551769540.png)



遇见错误

![计算机生成了可选文字: ufanjunquan@fanjunquan-virtuaI-machine.—Scargoinstallboottmage--version*O．7 aCannOtpa rSe the -vers•provided *O．7．3． 。7。31aSa](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144139229-1490433862.png)

原因:命令行多了空格,命令识别不出来以及^符号书写不正确导致



![计算机生成了可选文字: fanjunquan@fanjunquan-virtuaI-machine:—Scargoinstallboottmage--verstonAO.7.3 Updatingcrates.toindex Fetch[二 ]10。40％](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144138845-565389139.png)





## 成功

![计算机生成了可选文字: fanjunquan@fanjunquan-virtual-machine： Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Compiling Finished Installing Installing Installed JSOnVO。11。15 eitherVI。6。1 IIvm-toolsVO。1。1 crossbeam-uttlsVO。8。O memoffsetVO。5。6 rayonVI．5．O error-chainVO。12。4 locate-ca「go-manifestVO。1。O num_cpusv1。13。O watt-timeoutVO。2。O semverVO。9。O tomlVO。5。7 quoteVI。O。7 crossbeam-epochVO．9．O crossbeam-channelVO．5．O crossbeam-dequeVO。8。O ca「gometadataVO。7。4 boottmageVO．7．10 release[optimized]target(s)tn2伊04s /home/fanjunquan/.ca「go/btn/boottmage /home/fanjunquan/「go/btn/ca「go-boottmage package．boottmageVO．7．10．(executables．boottmage cargo-boottm 0 fanjunquan@fanjunquan-virtual-machine：，．$](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144138514-1772891909.png)





### (6)安装xbuild

![计算机生成了可选文字: Installing Installing Installing Installing Installing Installing Installing Installing Installing Installing Installing Installing Installing Installed /home/fanjunquan/。ca「go/btn/cargo /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/．ca「go/btn/ca「go /home/fanjunquan/「「 /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/「go/btn/ca「 /home/fanjunquan/。ca「go/btn/cargo /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/．ca「go/btn/ca「go /home/fanjunquan/．ca「go/btn/ca「go /home/fanjunquan/．ca「go/btn/ca「go -xbutld -XC -xcheck —XdOC —Xfix -xtnstall -Xt -xtest package「gO-XbUtIdVO．6．3．(executables•cargo-xb cargo-xbutl ca「go-xcheck cargo-xcltppy Ca「go—XC ca「go-xpubltsh' go-xtnstall Carg -xe ca「go-Xtest'） anjunquan@fanjunquan-virtuaI-machine:—Saa](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144138086-683433373.png)

成功



### (7)安装rust-src成功



![计算机生成了可选文字: Installing Installing Installing Installing Installing Installing Installing Installed /home/fanjunquan/.ca「go/btn/ca「go /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/.ca「go/btn/ca「go /home/fanjunquan/.ca「go/btn/ca「go /home/fanjunquan/.ca「go/btn/ca「go /home/fanjunquan/。ca「go/btn/ca「go /home/fanjunquan/「go/btn/ca「go-xpubltsh package「gO-XbUtIdVO．6．3．(executables•cargo-xb cargo-xbutl -xtnstall -Xt -xtest d． ca「go-xcheck ca「go-xcltppy Ca「go—XC cargo-xpubltsh' rgo-xtnstall Carg 0一xt． ca「go-Xtest'） fanjunquan@fanjunquan-virtuaI-machine:—Srustupcomponentaddrust-src info：downloadingcomponent'rust-src' info：installingcomponent'rust-src' info：Defaultingto533《3習iBunpack「a伊 0 fanjunquan@fanjunquan-virtual-machine：，．$](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144136671-1241926727.png)

**现象4-4：安装llvm-tools-preview成功**

![计算机生成了可选文字: ：anjunquan@fanjunquan-virtuaI-machine:—SrustupcomponentaddIIvm-tools-prevtew .nfO：downloadingcomponent'llvm-tools-p「evtew' .nfO：installingcomponent'llvm-tools-prevtew' .nfO：Defaultingto533．3習iBunpack「a伊 23。1習iB/23。1習iB（13e） 9．9MtB/stn3sETA： Os](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144136331-2050914844.png)



### (8)安装qemu

```
输入 sudo apt-get install qemu 安装
```



![image-20201031135814018](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144136033-880456043.png)

证明安装完成，在终端输入qemu，会弹出qemu窗口，如果没有弹出，可以先输入sudo ln -s /usr/bin/qemu-system-i386 /usr/bin/qemu将qemu与qemu-system-i386进行链接即可

### (9)**启动qumu**

![计算机生成了可选文字: t耒到'qe伊沪命令，您要输入的是否是． 命令qe伊沪来圭于包qe伊沪 命令'qtemu'来圭于包'qtemu' qemu：耒找到命令 fanjunquan@fanjunquan-virtuaI-machine:—SsudOIn s「/btn/qemu -machine:—Sqemu -S/usr/btn/qemu-system-t386/u](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144135641-1948484593.png)









# 二.创建裸机程序

### (1).禁用rust标准库!

利用cargo创建项目文档:

```shell
cargo new fjq_os
```

然后得到以下文件树

```
blog_os
├── Cargo.toml
└── src
    └── main.rs
```

在这里，`Cargo.toml`文件包含了包的**配置**（configuration），比如包的名称、作者、[semver版本](https://link.zhihu.com/?target=http%3A//semver.org/)和项目依赖项；`src/main.rs`文件包含包的**根模块**（root module）和main函数。我们可以使用`cargo build`来编译这个包，然后在`target/debug`文件夹内找到编译好的`blog_os`二进制文件。

### (2)利用cargo包编译

```
cargo build
```

###### ①出现以下错误一:

<img src="OS实验一.assets/image-20201029113446370.png" alt="image-20201029113446370" style="zoom: 67%;" />

###### ②错误1原因

出现这个错误的原因是，[println!宏](https://link.zhihu.com/?target=https%3A//doc.rust-lang.org/std/macro.println.html)是标准库的一部分，而我们的项目不再依赖于标准库。我们选择不再打印字符串。这也能解释得通，因为`println!`将会向**标准输出**（[standard output](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Standard_streams%23Standard_output_.28stdout.29)）打印字符，它依赖于特殊的文件描述符，而这是由操作系统提供的特性。 

所以我们可以移除这行代码，使用一个空的main函数再次尝试编译：

```
#![no_std]

fn main() {
   
}

```

###### ③出现错误2

```
> cargo build
error: `#[panic_handler]` function required, but not found
error: language item required, but not found: `eh_personality`
```

现在我们发现，编译器缺少一个**panic处理函数**（panic handler function）和一个**语言项**（language item）。

### (3)实现panic处理函数

```
// in main.rs

use core::panic::PanicInfo;

/// 这个函数将在panic时被调用
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```



### (4)eh_personality语言项

语言项是一些编译器需求的特殊函数或类型。举例来说，Rust的`Copy` trait是一个这样的语言项，告诉编译器哪些类型需要遵循**复制语义**（[copy semantics](https://link.zhihu.com/?target=https%3A//doc.rust-lang.org/nightly/core/marker/trait.Copy.html)）——当我们查找`Copy` trait的[实现](https://link.zhihu.com/?target=https%3A//github.com/rust-lang/rust/blob/485397e49a02a3b7ff77c17e4a3f16c653925cb3/src/libcore/marker.rs%23L296-L299)时，我们会发现，一个特殊的`#[lang = "copy"]`属性将它定义为了一个语言项，达到与编译器联系的目的。

### (5)禁用栈展开

在其它一些情况下，栈展开不是迫切需求的功能；因此，Rust提供了**在panic时中止**（[abort on panic](https://link.zhihu.com/?target=https%3A//github.com/rust-lang/rust/pull/32900)）的选项。这个选项能禁用栈展开相关的标志信息生成，也因此能缩小生成的二进制程序的长度。有许多方式能打开这个选项，最简单的方式是把下面的几行设置代码加入我们的`Cargo.toml`：

```text
[profile.dev]
panic = "abort"

[profile.release]
panic = "abort"
```

这些选项能将**`dev`配置**（dev profile）和**`release`配置**（release profile）的panic策略设为`abort`。`dev`配置适用于`cargo build`，而`release`配置适用于`cargo build --release`。现在编译器应该不再要求我们提供`eh_personality`语言项实现。

###### ①出现错误1

![image-20201029124238604](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144135382-1786968539.png)

### (6)start语言项

这里，我们的程序遗失了`start`语言项，它将定义一个程序的**入口点**（entry point）。

我们通常会认为，当运行一个程序时，首先被调用的是`main`函数。但是，大多数语言都拥有一个**运行时系统**（[runtime system](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Runtime_system)），它通常为**垃圾回收**（garbage collection）或**绿色线程**（software threads，或green threads）服务，如Java的GC或Go语言的协程（goroutine）；这个运行时系统需要在main函数前启动，因为它需要让程序初始化。

在一个典型的使用标准库的Rust程序中，程序运行是从一个名为`crt0`的运行时库开始的。`crt0`意为C runtime zero，它能建立一个适合运行C语言程序的环境，这包含了栈的创建和可执行程序参数的传入。这之后，这个运行时库会调用[Rust的运行时入口点](https://link.zhihu.com/?target=https%3A//github.com/rust-lang/rust/blob/bb4d1491466d8239a7a5fd68bd605e3276e97afb/src/libstd/rt.rs%23L32-L73)，这个入口点被称作**start语言项**（"start" language item）。Rust只拥有一个极小的运行时，它被设计为拥有较少的功能，如爆栈检测和打印**堆栈轨迹**（stack trace）。这之后，这个运行时将会调用main函数。

我们的独立式可执行程序并不能访问Rust运行时或`crt0`库，所以我们需要定义自己的入口点。实现一个`start`语言项并不能帮助我们，因为这之后程序依然要求`crt0`库。所以，我们要做的是，直接重写整个`crt0`库和它定义的入口点。

### (7)重写入口点

```
#![no_std]
#![no_main]

use core::panic::PanicInfo;

/// 这个函数将在panic时被调用
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

，我们转而编写一个`_start`函数：

```rust
#[no_mangle]
pub extern "C" fn _start() -> ! {
    loop {}
}
```

我们使用`no_mangle`标记这个函数，来对它禁用**名称重整**（[name mangling](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Name_mangling)）——这确保Rust编译器输出一个名为`_start`的函数；否则，编译器可能最终生成名为`_ZN3blog_os4_start7hb173fedf945531caE`的函数，无法让链接器正确辨别。

我们还将函数标记为`extern "C"`，告诉编译器这个函数应当使用[C语言的调用约定](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Calling_convention)，而不是Rust语言的调用约定。函数名为`_start`，是因为大多数系统默认使用这个名字作为入口点名称。

### (8)链接错误

![image-20201029130221143](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144135086-43470707.png)

为了解决这个错误，我们需要告诉链接器，它不应该包含（include）C语言运行环境。我们可以选择提供特定的**链接器参数**（linker argument），也可以选择编译为**裸机目标**（bare metal target）。

### (9)编译为裸机目标

在默认情况下，Rust尝试适配当前的系统环境，编译可执行程序。举个栗子，如果你使用`x86_64`平台的Windows系统，Rust将尝试编译一个扩展名为`.exe`的Windows可执行程序，并使用`x86_64`指令集。这个环境又被称作你的**宿主系统**（"host" system）。



为了描述不同的环境，Rust使用一个称为**目标三元组**（target triple）的字符串。要查看当前系统的目标三元组，我们可以运行`rustc --version --verbose`：

![image-20201029152945092](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144134758-1689153204.png)

##### ①添加thumbv7em

```
rustup target add thumbv7em-none-eabihf
```

这行命令将为目标下载一个标准库和core库。这之后，我们就能为这个目标构建独立式可执行程序

### (10)运行结果

了：

```text
cargo build --target thumbv7em-none-eabihf
```

我们传递了`--target`参数，来为裸机目标系统**交叉编译**（[cross compile](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Cross_compiler)）我们的程序。我们的目标并不包括操作系统，所以链接器不会试着链接C语言运行环境，因此构建过程成功完成，不会产生链接器错误。

我们将使用这个方法编写自己的操作系统内核。我们不将编译到`thumbv7em-none-eabihf`，而是使用描述`x86_64`环境的**自定义目标**（[custom target](https://link.zhihu.com/?target=https%3A//doc.rust-lang.org/rustc/targets/custom.html)）。



![image-20201030170215461](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144134188-2038714210.png)

# 三.构建最小内核

### (1)目标配置清单

我们将把我们的内核编译到`x86_64`架构，所以我们的配置清单将和上面的例子相似。现在，我们来创建一个名为`x86_64-blog_os.json`的文件——当然也可以选用自己喜欢的文件名——里面包含这样的内容：

```json
{
  "llvm-target": "x86_64-unknown-none",
  "data-layout": "e-m:e-i64:64-f80:128-n8:16:32:64-S128",
  "arch": "x86_64",
  "target-endian": "little",
  "target-pointer-width": "64",
  "target-c-int-width": "32",
  "os": "none",
  "executables": true,
  "linker-flavor": "ld.lld",
  "linker": "rust-lld",
  "panic-strategy": "abort",
  "disable-redzone": true,
  "features": "-mmx,-sse,+soft-float"
}
```

需要注意的是，因为我们要在**裸机**（bare metal）上运行内核，我们已经修改了`llvm-target`的内容，并将`os`配置项的值改为`none`。

### (2)编写内核程序

```rust
#![no_std] // 不链接Rust标准库
#![no_main] // 禁用所有Rust层级的入口点

use core::panic::PanicInfo;

/// 这个函数将在panic时被调用
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}

#[no_mangle] // 不重整函数名
pub extern "C" fn _start() -> ! {
    // 因为编译器会寻找一个名为`_start`的函数，所以这个函数就是入口点
    // 默认命名为`_start`
    loop {}
}
```

尝试编译内核

```shell
 cargo build --target x86_64-blog_os.json
```

报错:

```
error[E0463]: can't find crate for `core` 
（或者是下面的错误）
```

通常状况下，`core`库以**预编译库**（precompiled library）的形式与Rust编译器一同发布——这时，`core`库只对支持的宿主系统有效，而我们自定义的目标系统无效。如果我们想为其它系统编译代码，我们需要为这些系统重新编译整个`core`库。

解决方法：安装cargo xbuild库，即输入`cargo install cargo-xbuild`

再次编译

```
 cargo xbuild --target x86_64-fjq_os.json
```

![image-20201029163909658](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144133419-1696142009.png)

我们能看到，`cargo xbuild`为我们自定义的目标交叉编译了`core`、`compiler_builtin`和`alloc`三个部件。这些部件使用了大量的**不稳定特性**（unstable features），所以只能在nightly版本的Rust编译器中工作。这之后，`cargo xbuild`成功地编译了我们的`blog_os`包。

现在我们可以为裸机编译内核了；但是，我们提供给引导程序的入口点`_start`函数还是空的。我们可以添加一些东西进去，不过我们可以先做一些优化工作。

### (3)设置默认目标

为了避免每次使用`cargo xbuild`时传递`--target`参数，我们可以覆写默认的编译目标。我们创建一个名为`.cargo/config`的[cargo配置文件](https://link.zhihu.com/?target=https%3A//doc.rust-lang.org/cargo/reference/config.html)，添加下面的内容：

```text
# in .cargo/config

[build]
target = "x86_64-fjq_os.json"
```

这里的配置告诉`cargo`在没有显式声明目标的情况下，使用我们提供的`x86_64-blog_os.json`作为目标配置。这意味着保存后，我们可以直接使用：

```text
cargo xbuild
```

出现错误

![image-20201029165500952](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144132648-1013725123.png)

上面说的是没有找到x86_64-fqj_os.json文件

于是把文件复制到home中:再次

```
cargo xbuild
```

![image-20201029224710798](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144132262-1537390513.png)

出现错误,上面显示的是bootloader的版本不能匹配,因此选择0.9.8

成功:

![image-20201029225140205](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144131911-86009007.png)

### (4)打印到屏幕:

最简单的方式是写入**VGA字符缓冲区**（[VGA text buffer](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/VGA-compatible_text_mode)）：这是一段映射到VGA硬件的特殊内存片段，包含着显示在屏幕上的内容。通常情况下，它能够存储25行、80列共2000个**字符单元**（character cell）；每个字符单元能够显示一个ASCII字符，也能设置这个字符的**前景色**（foreground color）和**背景色**（background color）。

```rust
我们的实现就像这样：
static HELLO: &[u8] = b"Hello World!";

#[no_mangle]
pub extern "C" fn _start() -> ! {
    let vga_buffer = 0xb8000 as *mut u8;

    for (i, &byte) in HELLO.iter().enumerate() {
        unsafe {
            *vga_buffer.offset(i as isize * 2) = byte;
            *vga_buffer.offset(i as isize * 2 + 1) = 0xb;
        }
    }

    loop {}
}
```

### (5)制作img文件:

````
> cargo bootimage
````

![image-20201030170605430](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144131646-762700604.png)

结果在运行这行命令之后，我们应该能在`target/x86_64-blog_os/debug`目录内找到我们的映像文件`bootimage-blog_os.bin`。我们可以在虚拟机内启动它，也可以刻录到U盘上以便在真机上启动。（需要注意的是，因为文件格式不同，这里的bin文件并不是一个光驱映像，所以将它刻录到光盘



### (6)在QEMU中启动内核

现在我们可以在虚拟机中启动内核了。为了在[QEMU](https://link.zhihu.com/?target=https%3A//www.qemu.org/)中启动内核，我们使用下面的命令：

执行命令:

```
 qemu-system-x86_64 -drive format=raw,file=bootimage-fjq_os.bin
```



![image-20201029211854446](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144131225-1226620803.png)



结果显示



![image-20201029211828678](https://img2020.cnblogs.com/blog/1686010/202010/1686010-20201031144130594-1035810287.png)

# 四、实验重难点

(1)裸机程序讲解

```rust
#! [ no_std ] // don ’ t l i n k the Rust standard library 
//禁用rust标准可以命令
#! [ no_main ] // disable all Rust level entry points 
//由于我们禁用了标准库,所以我们需要
use core : : panic : : PanicInfo ; 
//panicinfo参数包含了panic发生的文件名、代码行数和可选的错误信息，这个函数从不返回，所以他被标记为发散函数，发散函数的返回类型成为never类型，记为！。
#[ no_mangle ] // don’t mangle the name of this function 
//使用no_mangle来标记start函数，来对他禁用名称重整，这确保rust编译器输出一个名为_start的函数，不然编译器无法辨别
pub extern ”C” fn _start () > ! { 
//将函数标记为extern “C”，告诉编译器这个函数应当使用c语言的调用约定，而不是rust语言的调用约定。函数名为_start是因为大多数系统默认使用这个名字作为入口点名称。

loop {} 
} 
//这个函数的返回值类型为!，它定义了一个发散函数，或者说一个不允许返回的函数，因为这个入口点将不被任何函数调用，而直接被操作系统或者引导程序调用，所以作为函数返回的替换，这个入口点应该调用操作系统提供的exit系统调用函数。当一个独立式可执行程序返回时，不会留下任何需要做的事情，现在，我们只需要添加一个无限循环，用来满足对返回值类型的需求。
/// This function i s called on panic . 
#[ panic_handler ] 
//属性定义了一个函数，他会在panic发生时被调用。标准库中提供了panic处理函数，但在当前的no_std环境中，我们需要定义一个自己的panic处理函数
fn panic ( _info : &PanicInfo ) > ! { 
loop {} 
    //这里网上 有panic的源代码,这里暂时定为循环
}

```

(2)裸机程序.toml文件配置:

```json
[package]
name = "fjq_os"
version = "0.1.0"
authors = ["fanjunquan"]
edition = "2018"

# the p r o f i l e used for ‘ cargo build ‘ 
[ p r o f i l e . dev ] 
panic = ” abort ” # disable stack unwinding on panic 
//将dev配置的panic策略设为abort，dev配置适用于cargo build，现在编译器应该不再要求我们提供eh_personality语言项实现。禁用panic时栈展开
 # the p r o f i l e used for ‘ cargo build’ --release 
[ p r o f i l e . r e l e a s e ] 
panic = ” abort ” # disable stack unwinding on panic 
//将release配置的panic策略设为abort，releas配置适用于cargo build --release，现在编译器应该不再要求我们提供eh_personality语言项实现。禁用panic时栈展开
# See more keys and their d e f i n i t i o n s at https : //doc . rust lang . org/cargo/reference/manifest . html 
[ dependencies ]

```

### (3)实现最小内核

然后在制作一个最小内核程序的时候,我们链接了C语言,通过VGA显存地址打印出每一个字符的种类和颜色然后再在显示出来,最后在qemu的虚拟机上显示出了""hello world"

```rust
# in Cargo.toml

[dependencies]
bootloader = "0.9.8"
```

要将可执行程序转换为**可引导的映像**（bootable disk image），我们需要把它和引导程序链接。这里，引导程序将负责初始化CPU并加载我们的内核。

编写引导程序并不容易，所以我们不编写自己的引导程序，而是使用已有的bootloader包[[5\]](https://zhuanlan.zhihu.com/p/56433770#ref_5)；无需依赖于C语言，这个包基于Rust代码和内联汇编，实现了一个五脏俱全的BIOS引导程序。为了用它启动我们的内核，我们需要将它添加为一个依赖项，如上,

只添加引导程序为依赖项，并不足以创建一个可引导的磁盘映像；我们还需要内核编译完成之后，将内核和引导程序组合在一起。

为了解决这个问题，我们建议使用`bootimage`工具——它将会在内核编译完毕后，将它和引导程序组合在一起，最终创建一个能够引导的磁盘映像。

### 最小内核源代码

```rust

pub extern ”C” fn _start () > ! { 
//将函数标记为extern “C”，告诉编译器这个函数应当使用c语言的调用约定，而不是rust语言的调用约定。函数名为_start是因为大多数系统默认使用这个名字作为入口点名称。
l e t vga_buffer = 0xb8000 as *mut u8 ; 
//首先将整数0xb80000转换为一个裸指针
for ( i , &byte ) in HELLO. i t e r ( ) . enumerate () { 
unsafe { 
    *vga_buffer . o f f s e t ( i as i s i z e * 2) = byte ; 
    *vga_buffer . o f f s e t ( i as i s i z e * 2 + 1) = 0xb ; } 
} 
//迭代HELLO的每个字节，使用enumerate获得一个额外的序号变量i，在for语句的循环体中，使用offest偏移裸指针，解引用它，来将字符串的每个字节和对应的颜色字节——0xb代表淡青色——写入内存位置。所有的裸指针内存操作都被一个unsafe语句块包围。这是因为，此时编译器不能确保我们创建的裸指针是有效的；一个裸指针可能指向任何一个你内存位置；直接解引用并写入它，也许会损坏正常的数据。使用unsafe语句块时，程序员其实在告诉编译器，自己保证语句块内的操作是有效的。
loop {} 
} 


```

自定义targetJson文件



 ```json
{
  "llvm-target": "x86_64-unknown-none",
  "data-layout": "e-m:e-i64:64-f80:128-n8:16:32:64-S128",
  "arch": "x86_64",
  "target-endian": "little",
  "target-pointer-width": "64",
  "target-c-int-width": "32",
  "os": "none",
  "executables": true,
  "linker-flavor": "ld.lld",
  "linker": "rust-lld",
    //在这里，我们不使用平台默认提供的链接器，因为它可能不支持Linux目标系统。为了链接我们的内核，我们使用跨平台的LLD链接器（LLD linker），它是和Rust打包发布的。
  "panic-strategy": "abort",
    //这个配置项的意思是，我们的编译目标不支持panic时的栈展开（stack unwinding），所以我们选择直接在panic时中止（abort on panic）。这和在Cargo.toml文件中添加panic = "abort"选项的作用是相同的，所以我们可以不在这里的配置清单中填写这一项。
  "disable-redzone": true,
    //我们正在编写一个内核，所以我们应该同时处理中断。要安全地实现这一点，我们必须禁用一个与红区（redzone）有关的栈指针优化：因为此时，这个优化可能会导致栈被破坏
  "features": "-mmx,-sse,+soft-float"
    //features配置项被用来启用或禁用某个目标CPU特征（CPU feature）。通过在它们前面添加-号，我们将mmx和sse特征禁用；添加前缀+号，我们启用了soft-float特征。
}
 ```



### 自己遇到最困难的一点:

**设置默认目标**,因为设置xbuild的默认目标需要对.cargo里面的config文件进行自定义调整,这里首先我们需要对linux下的文件目录比较熟悉,然后我在重复的用cargo build 命令执行的时候,一直各种报错,这里也让我学会了如何在一大堆报错文字下面找到问题,首先要在相应目录下建立文件夹。然后又报错重名的错误,然后就改了一下文件名字等等,一步一步的解决了错误,让我对找错更加得心应手了

# 五、总结

这次实验是制作微型操作系统的一个预先准备实验。

(1)首先我们需要安装整个rust环境

(2)在安装rust环境的时候我利用了curl工具下载rust已经下载了rustc,然后设置环境变量

(3) 接着安装了各种依赖,链接,以及qemu虚拟机

以上教我学会了如下在linux下载东西,并且如何设置环境变量使用,验证各种东西是否安装成功

然后就是利用rust创建裸机程序以及构建最小内核,载创建裸机程序的时候,我们为了让rust禁用标准库,把rust语言改得面部全非,已经配上以及特有的指令让他运行了起来。 

然后在制作一个最小内核程序的时候,我们链接了C语言,通过VGA显存地址打印出每一个字符的种类和颜色然后再在显示出来,最后在qemu的虚拟机上显示出了""hello world"!,这里让我发现和<<30天自制操作系统>>这本书很多地方有相似的地址,感觉两者之间最大的区别就是一个在window上利用各种bat脚本文件运行的,另一个是直接在ubuntu上用准确的利用每一行命令让我切身体会到,自制操作系统的艰难😊

