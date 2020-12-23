# 1  Makefile

GNU make的工作分为两个阶段。在第一阶段，make读取makefile文件、内置变量及其值、隐含规则和具体规则、构造所有目标的依赖关系以及所有目标各自的依赖等。在第二阶段，make决定需要重新构造的目标并使用必要的规则进行工作。

make工作第一阶段发生的扩展是立即扩展，直接把变量和函数扩展为makefile文件语句的一部分。make工作第二阶段发生的扩展称为延时扩展。

## 1.1     make命令的使用

**make[option] [target]**

 

**-Cdir**  ：在读入makefile之前，把路径切换到dir下。如果同时使用几个‘-C’选项，则eachis interpreted relative to the previous one。

**-d**     ：在正常处理后打印调试信息。

**-e**     ：设置环境变量的优先权高于makefile文件变量的优先权。

**-ffile**  ：将file设置为makefile文件。

**-i**      ：忽略在执行重建文件命令时产生的所有错误。

**-Idir**  ：指定搜寻makefile文件的路径。如果同时使用几个‘-I’选项，则按照次序搜寻这些路径。

**-k**     ：在出现错误后，尽可能的继续执行。也就是说当一个目标创建失败后，所有依靠它的目标文件将不能重建，而这些目标的其它依赖则可继续处理。

**-n**     ：打印要执行的命令，但却不执行它们。

**-ofile**  ：即使文件file比它的依赖旧，也不重建该文件。

**-p**     ：打印数据库，其中的数据来自读入makefile文件的结果；打印之后执行。

make –qp              打印数据库后不执行。

make –p –f/dev/null    打印预定义的规则和变量的数据库。

**-q**     ：不打印也不执行命令。如果所有目标都已经更新到最新，make的退出状态是0；如果一部分需要更新，退出状态是1；如果make遇到错误，退出状态是2。

**-r**     ：禁止使用预定义的隐含规则，同时也清除了缺省的后缀列表和后缀规则。注意缺省的变量仍然有效。

**-R**     ：禁止使用内建的规则变量。‘-R’自动使‘-r’生效。

**-s**     ：不回显执行的命令。

**-S**     ：使‘-k’失效。除非在递归调用make时，通过变量MAKEFLAGS从上层make继承‘-k’，或环境中设置了选项‘-k’，否则没有必要使用该选项。

**-t**      ：标记文件已经更新到最新，但实际却没有更新它们。

**-w**     ：打印执行makefile文件时涉及的所有工作目录。

**-Wfile** ：Pretendthat the file has been just modified。在和‘-n’一起使用时，将表明更改该文件会发生什么。如果没有和‘-n’一起使用，那么它和在运行make之前对该文件使用touch命令的结果几乎一样，但使用该选项make只是在想象中更改该文件的时间戳。

## 1.2     include的使用

缺省情况下，make按顺序寻找makefile文件：GNUmakefile、makefile和Makefile。如果使用非标准名字的makefile文件，可以使用‘-f’参数指定makefile文件。如果使用多个‘-f’参数，所有的makefile文件按顺序发生作用。一旦使用了‘-f’参数，将不再自动检查是否存在标准名字的makefile文件。

include指令告诉make暂停读取当前的makefile文件，先读完include指定的makefile文件后再继续。include指令在makefile文件占单独一行，其格式如下：

**includefilename**

行首的多余空格是允许的，注意不能以Tab开始。因为，以Tab开始的行，make认为是命令行。多个文件名之间也以空格隔开，多余的空格被忽略。文件名可以包含变量及函数调用，它们在处理时由make进行扩展。

## 1.3     环境变量MAKEFILES

如果定义了环境变量MAKEFILES，make认为该变量的值是一列附加的makefile文件名，文件名之间由空格隔开，并且这些makefile文件应该首先读取，读取方式和include指令的方式基本相同。值得注意的是，缺省最终目标不会出现在这些makefile文件中，而且如果一些makefile文件没有找到也不会出现任何错误信息。

## 1.4     搜索目标/依赖文件

正常情况下，虽然目标/依赖文件名中不含有路径，其实这种文件名的路径部分是空值，代表的是当前目录（**./**）。如果我们要在文件名中给出路径，则有两种方法：

1、绝对路径，必须以‘**/**’开头；

2、相对路径，非‘**/**’开头的所有路径。

### 1.4.1    变量VPATH

变量VPATH定义了一组搜索路径。如果一个文件在文件名给出的路径中不存在，就在（VPATH+文件名中的路径）的路径中搜索该文件。

在VPATH定义中，路径的名字由冒号或空格分开。

### 1.4.2    命令vpath

#### 1.4.2.1   vpath pattern directories

vpath允许对符合某一格式的文件名指定一个搜寻路径，路径由冒号或空格隔开。pattern是一个包含‘%’的字符串，例如：

**vpath%.h $(INCDIR)**

如果有多个vpath和一个文件名匹配，则make按次序搜索这些vpath指定的路径。

#### 1.4.2.2   vpath pattern

清除和该格式相关联的搜寻路径。

#### 1.4.2.3   vpath

清除前面所有由vapth指定的搜寻路径。

### 1.4.3    变量GPATH

1．    如果目标文件是根据文件名搜索得到的，此时$@代表文件名。

2．    如果目标文件是通过搜索其它路径得到的，此时$@代表文件名。但是如果搜索路径出现在变量GPATH中，$@将代表（搜索路径+文件名）。

3．    如果目标文件没有被搜索到，意味着需要执行规则命令来创建该目标文件，此时$@代表文件名。

4．    如果依赖文件是根据文件名搜索得到的，而且该依赖文件不需要被更新，此时$<代表文件名。

5．    如果依赖文件是通过搜索其它路径得到的，而且该依赖文件不需要被更新，此时$<代表（搜索路径+文件名）。

6．    如果依赖文件没有被搜索到或是需要被更新，此时$<代表的是当它作为目标文件被创建或更新的规则命令中的$@。

# 2  规则的使用

makefile的基本单位是规则。每一条规则说明一个目标，除了描述该目标所依赖的文件，还要指明生成和更新该目标所需的命令。规则的格式为：

**targetsruleop [prerequisites] [；recipe]**

**{<tab>recipe}**

 

**targets ：**目标文件列表。

**ruleop ：**分割目标文件与依赖文件的符号，常见的是‘：’也可以使用其它一些符号。

‘：：’用于一个目标有多个规则的情形；

‘：^’将依赖文件和目标文件已有的依赖文件合起来，成为新的依赖文件列表；

‘：-’清除目标文件原有的依赖文件，将新的依赖文件作为目标的依赖文件列表；

‘：!’对每个更新过的依赖文件都执行一次命令菜单；

‘：|’只在隐含规则中使用。

**prerequisites：**目标所依赖的文件列表。

**recipe：**命令菜单，重新生成目标文件的命令，可以在tab之后加上以下符号。

-：忽略本命令行的错误返回，继续执行。否则，make会停止执行；

**+：**make始终执行本命令行；

**@：**执行本命令行时不在标准输出上显示。

makefile文件的一些书写规则：

1．每一个命令行的开头都要是一个tab；

2．命令行之间可以插入任意多个空行，这些空行也要以tab开头；

3．第一条命令可以直接跟在依赖文件列表后面，用‘；’隔开；

4．如果一行过长，可以在到达右边界之前放入一个“\”符号，而且“\”和新的一行之间不能有空白；

5．注释以‘#’开始，以换行符结束，如果其它地方用到‘#’，要用双引号引用；

6．makefile中涉及的文件名允许使用通配符。

 

规则不论其形式如何，都按相同的方式扩展：

**immediate: immediate ; deferred**

**deferred**

目标和依赖部分都立即扩展，命令通常都是延时扩展。

一般情况下规则的次序无关紧要，但决定缺省最终目标时却是例外。缺省最终目标是没有指定最终目标时make指定的最终目标。缺省最终目标是makefile文件中的第一条规则的目标。如果第一条规则有多个目标，只有第一个目标被认为是缺省最终目标。所以，我们编写makefile文件时，通常将第一个规则的目标定为编译全部程序（设定一个称为‘all’的目标）。

有两种例外的情况：以‘**.**’开始的目标不是缺省最终目标；格式规则定义的目标不是缺省最终目标。

## 2.1     格式规则

格式规则是指定多个目标并能够根据每个目标名构造对应的依赖名的规则。要使用格式规则，目标文件名必须匹配目标格式，而且符合依赖格式的文件必须存在或可以创建。

格式规则的语法格式：

**targets...: target-pattern: dep-patterns ...**

**commands**

**...**

目标可以含有通配符。**target-pattern**表示目标格式，**dep-patterns**表示依赖格式，通常都包含字符‘%’。目标格式匹配目标时，‘%’代表的字符串称为径(stem)。每个依赖名使用径代替‘%’。在格式规则中使用的‘%’扩展是在所有变量和函数扩展以后进行的，而所有的变量和函数扩展都是在makefile文件读入时完成的。

当目标格式中不包含‘/’，目标文件名中的路径名部分首先被去除，然后再进行匹配，最后路径名将会加在产生的依赖前面。

 

在依赖格式中不包含‘%’也是合法的，此时对所有目标来说，依赖是相同的。

**OBJ= hello1.o hello2.o**

**all:$(OBJ)**

**$(OBJ):%.o: %.c**

**……**

上面的规则展开后等价于下面两条规则：

**hello1.o: hello1.c**

**……**

**hello1.o: hello2.c**

**……**

每一个目标必须和目标格式匹配，如果不符则产生警告。如果您有一系列文件，其中仅有一部分和格式匹配，您可以使用filter函数把不符合的文件移走。

**FILE= test. c test1.o test2.o**

**$(filter %.o, $(FILE) ): %.o: %.c**

**……**

如果一个目标文件符合对于多个格式规则，使用最早出现的格式规则。自己编写的格式规则比预定义的隐含规则优先。

格式规则只能用于规则中指定的目标。而隐含规则可以应用于任何与它匹配的目标，前提是这些目标没有与之对应的规则，而且隐含规则中对应的依赖也可以被搜寻到。如果有多条隐含规则适合，仅执行其中一条规则，按照隐含规则定义的顺序进行选择。

## 2.2     隐含规则

如果根据依赖推断出某一个目标文件需要更新，而对应的规则却没有给出命令，这时make就会从隐含规则中查找符合该目标格式的隐含规则，并根据该隐含规则生成对应的依赖文件（注意隐含规则使用的依赖文件名是隐含规则根据目标格式自己生成的，而不是使用目标文件原有的依赖文件），然后用隐含规则中的命令来更新改目标文件。

如果一条规则的其中一个依赖文件没有通过路径搜索找到，则make会尝试使用隐含规则来创建该文件，如果没有对应的隐含规则可以创建该文件，就会返回error。

### 2.2.1    预定义隐含规则

可以在makefile文件中重载这些隐含规则。也可以取消预定义的隐含规则，只要不在后面写命令就可以了。make命令的参数‘-r’也能把缺省的后缀列表清空，从而删除所有预定义的隐含规则。

如果预定义的隐含规则的依赖出现在后缀列表中，则该预定义的隐含规则也称为后缀规则。如果更改了后缀列表，那些依赖的后缀没有出现在新后缀列表中的预定义的隐含规则将被禁止。

后缀是特殊目标**.SUFFIXES**的依赖名。例如：

**.SUFFIXES:**              # 删除缺省的后缀列表

**.SUFFIXES:.cpp .obj**      #把.cpp和.obj添加到后缀列表中。

### 2.2.2    隐含规则链

有时生成一个文件需要使用多个隐含规则组成的序列，这样的隐含规则序列称为隐含规则链。同一条隐含规则不能在隐含规则链中出现两次或两次以上。

通常情况下，任何在makefile文件中提及的目标和依赖都不是中间文件。但是，我们可以特别指定一些文件为中间文件，只需将这些文件指定为特殊目标**.INTERMEDIATE**的依赖。如果**.INTERMEDIATE**没有依赖文件，它将不会发生作用。

为了阻止自动删除中间文件，可以将需要保留的中间文件指定为特殊目标**.SECONDARY**的依赖。**.SECONDARY**的依赖被处理为中间文件，但它们永远不能自动删除。如果**.SECONDARY**没有依赖文件，则所有的目标都将被处理为中间文件。

也可以将一个隐含规则的目标格式作为特殊目标**.PRECIOUS**的依赖，这样就可以保留那些由该隐含规则创建的中间文件。

### 2.2.3    后缀规则

后缀规则已经被格式规则代替，分为单后缀和双后缀规则。

双后缀规则：                等同于格式规则：

**.c.o:                        %.o: %.c**

**……                        ……**

单后缀规则：                等同于格式规则：

**.c                          %: %.c**

**……                        ……**

后缀规则不能有任何属于它们自己的依赖，而格式规则可以有依赖文件。例如：

**%.o:%.c hello.h**

**……**

没有命令的后缀规则没有意义，它们并不像没有命令的格式规则那样移去以前的规则。

## 2.3     假想目标

假设一个项目最后需要产生两个可执行文件，而且这两个文件是相互独立的，可以使用假想目标来达到这种效果。一个假想目标跟一个正常的目标几乎是一样的，只是这个目标文件是不存在的。例如在makefile的最开始输入：



```
default: exec1 exec2

make把default做为它的主要目标，每次执行时都会尝试把default更新。可既然这个文件并不存在，make就会尝试运用该规则创建default，就检查它的依赖exec1,exec2是否需要更新，如果需要，就把它们更新，从而达到我们的目的。
```





```
假想目标也可以用来描述一组动作。例如，需要把所有make产生的文件删除，你可以在makefile里设立这样一个规则：

**clean:**

**rm \*.o**

使用命令“make clean”，make就会把clean做为它的主要目标，执行rm命令。但是如果恰巧存在一个名字为clean的文件，因为在这个规则里没有任何依赖文件，所以这个目标文件一定是最新的了，所以即使用户使用命令“make clean”，也不会有任何事情发生。解决方法是标明所有的假想目标为**.PHONY**，这就告诉make不用检查它们是否存在，也不用查找任何隐含规则，直接假设指定的目标需要被更新。例如在makefile里加入下面这行规则：

**.PHONY: clean**


```



```
当假想目标A是假想目标B的依赖，则A将作为B的子程序，而且是先执行A中的命令，再执行B中的命令。例如，这里‘make cleanall’用来删除所有文件：

**.PHONY:cleanall cleanobj cleansrc cleanheader**

**cleanall:cleanobj cleansrc clearnheader**

**cleanobj:**

​	**rm \*.o**

**cleansrc:**

​    **rm \*.c**

**cleanheader:**

​		**rm \*.h**

## 
```

## 2.4     没有命令和依赖的规则

如果一个规则只有目标，没有依赖，也没有命令，而且该规则的目标也不存在，则make认为只要该规则运行，其目标就已被更新。这意味着所有以这种规则的目标为依赖的规则，它们的命令将总被执行。

**cleanobj: FORCE**

**rm\*.o**

**FORCE:**

目标FORCR满足上面的条件，所以以其为依赖的目标clean将总执行命令。

还有一个办法可以强制执行一条规则中的命令，只要该规则的目标是假想目标，而且该假想目标没有依赖，而且该假想目标是主要目标的依赖，这样的话该规则的命令就一定会被执行。

**.PHONY:all cleanobj**

**all:cleanobj ……**

**……**

**cleanobj:**

**rm \*.o**

## 2.5     内建的特殊目标名

**.DEFAULT**

**.DEFAULT**指定一些命令，这些命令应用于那些没能找到规则的目标。

## 2.6     具有多个目标的规则

具有多个目标的规则等同于多条独立的规则，这些规则除了目标不同之外，其余部分完全相同，意味着所有的目标有相同的依赖，相同的命令应用于所有目标，可以在命令中使用‘$@’区分实际的目标名称。

## 2.7     具有多条规则的目标

当一个目标出现在多条规则中时，所有的规则必须是同一类型：要么都是双冒号规则，要么都是普通规则。

如果它们都是双冒号规则，则它们之间都是相互独立的。双冒号规则实际就是将具有相同目标的多条规则相互分离，每一条双冒号规则都独立的运行，就像这些规则的目标不同一样。每一个双冒号规则都应该指定命令，如果没有指定命令，则会使用隐含规则。

如果它们都是普通规则。则在所有规则中提及的依赖将合并在一个依赖列表中。如果该目标比任何一个依赖旧，命令将被执行来重建该目标。但是如果有一条以上的规则为该目标文件指定命令，make将使用最后给出的规则，同时打印错误信息。

## 2.8     自动生成依赖

对每一个源程序文件name.c有一个名为name.d的文件和它对应，该文件中列出了name.o所依赖的文件。根据name.c生成name.d的格式规则：

**DEPEND=dependencies**

**depend:**

   **@set -e;\**

   **$(CC) -MM $(SRC)>$(DEPEND)**

然后就可以使用include命令直接将**DEPEND**文件读入。

**include$(DEPEND)**

### 2.8.1    sed用法简介

#### 2.8.1.1   s/regularexpression/replacement/flags

用replacement string替换符合regularexpression的字符串，用法中的‘**/**’可以用任何其它字符代替，在下面的例子中我们就使用了‘，’。

flags is:

**n** Substitute for just the nth occurrence ofthe regular expression.

**g** Globally substitute.

**| sed 's,\**\*\*\*\*\*\*\*\*\*\*.\*\*\*\*∗\*\*\*\*\*\*\*\*\*\*\*\*.∗\*\**\*\.c[ :]\*,\1.o:,g'>**

可以将输入中以**.c**结尾的字符串都置换为以**.o**的字符串，然后再输出。

#### 2.8.1.2   rere

Defines a sub-expression **re**. A subsequent reference of ‘\n’,where n is a number in the range 1–9。如果**re**是一个regularsub-expression，则‘\n’扩展为第n个**re**匹配的字符串。如果**re**不是一个regular sub-expression，则‘\n’扩展为第n个**re**本身。

#### 2.8.1.3   *

Matches the single-character regularexpression or sub-expression immediately preceding it zero or more times. If *is the first character of a regular expression or sub-expression, it matchesitself.

#### 2.8.1.4   [char-class]

Matches any single character in char-class.To include a ] in char class, it must be the first character. A range ofcharacters may be specified by separating the begin and end characters of therange with ‘-’, for example, a-z specifies the lowercase characters.

# 3  规则中的命令

规则中的命令由一系列shell命令行组成，它们按顺序执行。除第一条命令行可以用分号附属在目标-依赖行后面外，所有的命令行必须以Tab开始。空白行与注释行可在命令行中间出现，处理时它们被忽略。但是必须注意，以Tab开始的空白行不是空白行，它是而命令。

## 3.1     递归调用

可以在makefile文件中将make作为一个命令使用。例如，假设有一个子目录subdir，该目录中有它自己的makefile文件，可以按下述方式编写：

**subsystem:**

**cd subdir && $(MAKE)**

或者：

**subsystem:**

**$(MAKE) -C subdir**

注意递归调用make的命令总是使用变量MAKE。

缺省方式下，只有环境变量或在命令行中定义的变量才能传递给内层make。如果要将主makefile中的变量输出给子make，用export指令，格式如下：

**exportvariable ……**

要将阻止一些变量输出给子make，用unexport指令，格式如下：

**unexportvariable ……**

可以同时定义并输出一个变量：

**exportvariable = value**

如果您要将所有的变量都输出，您可以单独使用export。这就告诉make把export和unexport没有提及的变量统统输出，但unexport提及的变量仍然不能输出，而且名字中含有字母、数字和下划线以外字符的变量将不能输出，这些变量只能使用export指令才能输出。

需要注意的是，有两个变量，一个是SHELL，一个是MAKEFLAGS，这两个变量不管是否export，其总是要传递到下层makefile中。

参数‘-C’，‘-f’，‘-o’和‘-W’即使在MAKEFLAGS中也不能向下传递。在命令行中使用的参数也将通过MAKEFLAGS传递给子make。如果你不想往下层传递参数，你可以：

**subsystem:**

**cd subdir && $(MAKE) MAKEFLAGS=**

变量**MAKELEVEL**的值在向下层传递时发生变化。该变量的值是字符型，它用十进制数表示层的深度。‘0’代表顶层make，‘1’代表子make，‘2’代表子-子-make，以此类推。

如果您使用几层make递归调用，使用‘-w’可以显示每个make开始处理以及处理完成的目录。当使用‘-C’选项时，‘-w’选项已经自动打开。

## 3.2     定义固定次序的命令(定义多行变量)

可以使用define指令定义固定次序的命令。如果前缀字符（@，-，和+）在引用固定次序的命令行中使用，则该前缀字符将应用到固定次序的每一行中。固定次序实际是一个变量，因此它的名字不能和其它的变量名冲突。

**definename**

**……**

**endef**

使用：

**$(name)**

# 4  变量的使用

在makefile文件读入时，除规则命令中的变量、‘=’右边的变量、以及使用define定义的变量此时不扩展外，makefile文件其它各个部分的变量和函数都将扩展。

变量名不能含有‘：’、‘#’、‘=’；前导或结尾空格。变量名是区分大小写的。

make将忽略环境变量SHELL的值，所以如果要使用变量SHELL，我们必须自己定义。

## 4.1     变量的定义

变量名中也可以包含变量引用和函数调用，它们在读入时扩展。

除了空格，‘=’前面不能有tab或其它任何符号，否则都会被make当作变量名的一部分。‘=’后面的字符串可以包含任意字符，但会去除前面的所有空格和tab。

注意：变量值包含所有的结尾空格。

变量的定义可以出现在三个地方：

1．在makefile中定义；

2．在make命令行中定义。由于命令行中用空格作为不同参数的分隔符，因此当变量值中含有空格时，要用引号将整个变量值包含起来。

3．载入环境变量定义。

**make命令行变量定义>makefile中变量定义>shell中变量定义>内定义变量定义**

### 4.1.1    immediate = deferred / define

递归调用扩展型变量。适用于那些需要根据上下文才能确定值的变量的定义。

如果该变量值包含对其它变量的引用，这些引用在变量替换时才被扩展。如果在定义中使用函数，则函数只有在完成变量替换时才会执行。

### 4.1.2    immediate ?= deferred

仅在变量还没有定义的情况下有效。

### 4.1.3    immediate := immediate

简单扩展型变量，在定义的时候变量的值就确定了。适用于那些能够在一开始就确定值的变量的定义。

如果该变量值包含对其它变量的引用，这些引用在定义的时候就已经展开。如果在定义中使用函数，则函数在定义的时候就已经执行。

### 4.1.4    immediate += deferred orimmediate

变量原来的值加上一个空格，再加上后面的字符串，作为新的变量值。

如果变量在以前没有定义过，则‘+=’的作用和‘=’相同。如果变量在以前定义过，‘+=’的作用依赖于原始定义的特色。

### 4.1.5    特定目标变量的值

make中变量的值一般是全局性的，特定目标变量的值是个例外：

**target... : variable-assignmen**

或这样：

**target... : override variable-assignmen**

variable-assignmen使用任何赋值方式都是有效的：=、：=、+=、?=。在命令行中定义的变量值优先，而使用override定义的变量值优先。

当定义一个特定目标变量时，该变量值对特定目标target ...的所有依赖有效，除非这些依赖用它们自己的特定目标变量值将该变量重载。

### 4.1.6    特定格式变量的值

使用特定格式变量的值，可以为匹配指定格式的目标定义变量。

**pattern... : variable-assignment**

或这样：

**pattern... : override variable-assignment**

这里的pattern是‘%’格式。

variable-assignmen使用任何赋值方式都是有效的：=、：=、+=、?=。在命令行中定义的变量值优先，而使用override定义的变量值优先。

## 4.2     变量的引用

**$(变量名)**

当变量只有单个字符时，可以省略括号。

**${SRC:oldend=newend}**

如果SRC中某个字以字符串“oldend”的结尾，则将“oldend”替换为“newend”。

例如：**OBJ=${SRC:.c=.o}**

**${SRC:oldpattern=newpattern}**

pattern中包含‘%’，将SRC中的oldpattern替换为newpattern，‘%’代表的部分不改变。

例如：**OBJ=$(SRC:$(SRCDIR)/%.c=$(OBJDIR)/%.o)**

## 4.3     内定义变量

### 4.3.1    用于规则命令

**$@**       目标文件名，如果目标是形如libname(member)的库成员，$@等于库名libname。

**$%**    目标文件名，如果目标是形如libname(member)的库成员，$%等于成员名member。注意此时$%不再遵循目录搜索的规则，只能代表member。

**$>**        目标文件名，只能用于目标文件是库成员的情形，$>等于库名libname。

**$\***        如果目标名以一种内建的后缀结尾，$*为目标名去掉其后缀的部分。如果目标名不以内建的后缀结尾，则$*在该规则中设置为空值。

 

用于表示依赖文件的内建变量不区分库名和成员名，仍然代表整个libname(member)。

**$<**        第一个依赖文件名。

**$?**        所有依赖文件中比目标文件新的文件列表。

**$^**     目标文件在本规则中的所有依赖文件，省略了重复的依赖。

**$+**     目标文件在本规则中的所有依赖文件，保留了重复的依赖。

**$&**        目标文件在所有规则中的所有依赖文件；

 

以下两个内定义变量对以上变量通用（#可以是@，*，%，>，&，^，+，<，?）：

**$(#D)**  $#的路径部分，结尾‘/’已经移走。如果$?不包含‘/’，则值是‘**.**’。

**$(#F)**  $#的文件名部分

### 4.3.2    用于依赖文件列表

**$$@**   目标文件名，如果目标是形如libname(member)的库成员，$$@等于库名libname。

**$$%**   目标文件名，如果目标是形如libname(member)的库成员，$$%等于成员名member。

**$$\***    如果目标名以一种内建的后缀结尾，

∗为目标名去掉其后缀的部分。如果目标名不以内建的后缀结尾，则∗为目标名去掉其后缀的部分。如果目标名不以内建的后缀结尾，则

*在该规则中设置为空值。



**$$>**    目标文件名，只能用于目标文件是库成员的情形，等于库名libname。

## 4.4     文件名和文件路径变量的使用

**d**  仅展开路径，不包括文件名

**b**  展开文件名，不包括扩展名

**f**   展开文件名，包括扩展名

 

**FILE=/user/myprog/main.c**

**$(FILE:d)     =   /user/myprog**

**$(FILE:b)     =   main**

**$(FILE:f)     =   main.c**

**$(FILE:db)       =   /user/myprog/main**

**$(FILE:df)    =   /user/myprog/main.c**

文件名前面没有任何目录的文件被认为是位于当前工作目录下，路径名为‘**.**’。

# 5  通配符的使用

make中的通配符是*、?和[…]。通配符在规则中（目标、依赖和命令）可以正常使用，但在变量定义中或在函数的参数中通配符一般不能正常使用。

**objects= \*.o**

变量objects的值实际就是字符串“*.o”。

如果通配符前面是反斜杠‘\’，则该通配符失去通配能力。

# 6  makefile文件的条件语句

在指令行前面允许有多余的空格，但是不允许有Tab。如果一行以Tab开始，那么该行将被认为是规则的命令行。

## 6.1     条件语句的语法

对于没有else指令的条件语句的语法为：

**conditional-directive**

**……**

**endif**

完整的条件语句的语法为：

**conditional-directive**

**……**

**else**

**……**

**endif**

### 6.1.1    ifeq (arg1, arg2) /ifneq (arg1, arg2)

扩展参数arg1、arg2中的所有变量引用，并且比较它们。

### 6.1.2    ifdef variable/ ifndef variable

# 7  文本转换函数

函数调用的格式如下：

**$(functionarg1, arg2, ...)**

或这样：

**${functionarg1, arg2, ...}**

参数和函数名之间是用空格或Tab隔开。每一个参数经过变量替换或函数调用处理，最终得到参数的值。注意变量替换是按照变量在参数中出现的次序依次进行的。

逗号，空格和不成对出现的圆括号、大括号不能作为文本出现在参数中，如果需要使用这些字符，首先定义变量comma和space：

**comma:=,**

**empty:=**

**space:=$(empty)$(empty)**

## 7.1     字符串替换和分析函数

### 7.1.1    $(subst from,to,text)

在文本text中使用to替换每一处from。

### 7.1.2    $(patsubstpattern,replacement,text)

寻找text中符合pattern的字，并用replacement替换它们。这里的pattern中包含‘%’。如果replacement中也含有‘%’，就用和pattern中‘%’匹配的部分代替。

### 7.1.3    $(strip text)

去掉前导和结尾空格，并将字中间的多个空格压缩为单个空格。

### 7.1.4    $(findstring string,text)

在text中搜寻string，如果找到返回值是string，否则返回值为空。

### 7.1.5    $(filter pattern...,text)

返回在text中由空格隔开且匹配pattern...的字，并将不符合pattern...的字移出。

### 7.1.6    $(filter-out pattern...,text)

filter的反函数。

### 7.1.7    $(sort text)

将text中的字按字母排序，并取掉重复的字。输出的是由空格隔开的字的列表。

## 7.2     文件名函数

函数的参数是一组文件名，文件名之间用空格隔开（前导和结尾空格被忽略）。列表中的每一个文件名都采用相同的方式转换，而且结果用单个空格串联在一起。

### 7.2.1    $(dir names...)

抽取每一个文件名的路径部分，文件名的路径部分包括从文件名的开始到最后一个斜杠（含斜杠）之前的一切字符。如果文件名中没有斜杠，路径部分是“**./**”。

### 7.2.2    $(notdir names...)

抽取每一个文件名中除路径部分外一切字符（文件名）。如果一个文件名仅包含路径部分（以‘/’结束），则结果为空。

### 7.2.3    $(suffix names...)

抽取每一个文件名的后缀。如果文件名没有后缀，则结果为空。

### 7.2.4    $(basename names...)

抽取每一个文件名中除后缀外一切字符。

### 7.2.5    $(addsuffix suffix,names...)

将suffix附加在每一个文件名的后面。

### 7.2.6    $(addprefix prefix,names...)

将preffix附加在每一个文件名的前面。

### 7.2.7    $(join list1,list2)

两个参数的第一个字串联起来形成结果的第一个字，两个参数的第二个字串联起来形成结果的第二个字，以此类推。如果一个参数比另一个参数的字多，则多余的字原封不动的拷贝到结果中。

### 7.2.8    $(word n,text)

返回text中的第n个字。n的合法值从1开始。如果n超出范围，则返回空值。

### 7.2.9    $(wordlist s,e,text)

返回text中从第s个字开始到第e个字结束的字。s、e的合法值从1开始。如果s超出范围，则返回空值；如果e超出范围，则返回从第s个字开始到text结束的所有字；如果s比e大，不返回任何值。

### 7.2.10 $(words text)

返回text中字的数目。例如可以得到text中的最后一个字：

**$(word$(words text),text)**

### 7.2.11 $(firstword text)

返回第一个字。

### 7.2.12 $(wildcard pattern)

pattern是一个文件名格式，可以包含通配符，也可以包含有目录。wildcard函数将返回和pattern相符合的所有文件名。例如可以得到所有的源文件：

**SRC=$(wildcard$(SRCDIR)/\*.c)**

## 7.3     函数foreach

**$(foreachvar,list,text)**

把list中的字逐一取出赋给变量var，然后再执行text。每一次text会返回一个字。text所返回的每个字会以空格分隔，当整个循环结束时，foreach函数的返回值由text所返回的所有字组成（以空格分隔）。

**find_files= $(wildcard $(dir)/\*)**

**dirs:= /src /inc**

**files:= $(foreach dir,$(dirs),$(find_files))**

在定义变量find_file时，使用了=，因此该变量是递归调用型变量，这样find_file的值中的函数将在变量替换时执行。

## 7.4     函数if

**$(ifcondition,then-part[,else-part])**

首先把condition的前导/结尾空格去掉，然后扩展。如果扩展结果非空，则condition为真；如果扩展结果为空，则condition为假。

如果condition为真，计算then-part的值，并将该值作为整个函数的返回值。

如果condition为假，计算else-part的值，并将该值作为整个函数的返回值；如果else-part不存在，函数返回空值。

## 7.5     函数call

**$(callvariable,param1,param2,...)**

将每一个param赋值给临时变量$(1)、$(2)等；变量$(0)的值是变量variable。在给临时变量赋值以前首先扩展param。

variable是一个变量名，而不是对该变量的引用。如果变量名是内建函数名，则该内建函数将被执行。

call函数的目的就是通过在变量variable中使用临时变量$(1)、$(2)等来完成一定的功能，临时变量$(1)、$(2)等就是通过call函数进行赋值的。例如：

**reverse= $(2) $(1)**

**foo= $(call reverse,a,b)**

## 7.6     函数origin

**$(originvariable)**

函数origin的结果如下：

undefined      ：variable没有定义。

default        ：variable是缺省定义。如果重新定义了一个缺省变量，将返回下面的值。

environment    ：variable是环境变量定义，选项‘-e’没有打开。

file            ：variable在makefile中定义。

command line   ：variable在命令行中定义。

override       ：variable在makefile中用override指令定义。

automatic      ：variable是自动变量，定义它是为了执行每个规则中的命令。

environment override：variable是环境变量定义，选项‘-e’打开。

## 7.7     shell函数

虽然在规则的命令中使用的就是shell命令，但是如果想利用shell命令的输出结果，就需要使用shell函数。shell函数的参数就是shell命令，该函数把shell命令的输出作为函数的返回值：

**contents:= $(shell cat hello.c)**

# 8  makefile中库的使用

## 8.1     整个库的使用

当一个库作为目标时，和普通目标文件的使用相同，并可以使用各种创建库的方法来创建或更新目标库。

当一个库作为依赖时，可以使用

1．库文件的文件名（可以含有路径）。

2．‘-lname’。make首先将‘-lname’替换为libname.so，并在当前路径下、VPATH指定的路径下、与vpath匹配的路径下，以及/lib，/usr/lib和/usr/local/lib下进行搜索。如果没有找到libname.so文件，接着将‘-lname’替换为libname.a，仍在上述目录下进行搜索。具体替换的方式可以通过变量**.LIBPATTERNS**设置，缺省值是“lib%.so lib%.a”。注意name中不能包含路径名。

如果使用第二种方法，最好能够保证库能够被找到而且不需要被更新，此时$<代表的是（搜索路径+libname.so/libname.a）。如果库没有被搜索到或是需要被更新，则根据目录搜寻原则，此时$<代表的是把‘-lname’作为目标文件创建或更新的规则命令中的$@。但是如果‘-lname’作为目标，‘-lname’就不会再进行上述的名字替换。这样的话根据目录搜索的原则$@可能会代表：-lname或（搜索路径+-lname）。所以原规则（库作为依赖的规则）中的$<将失去它的作用，对编写规则的命令很不利。

所以如果库作为依赖有可能找不到或者需要被更新时，最好使用第一种形式。

## 8.2     库成员的使用

库成员也可以用作目标或依赖。使用‘libname(member1 member2 …)’的格式。这种格式只能在目标和依赖中使用，不能出现在规则的命令中。

当库成员作为目标时，更新库成员的命令必须使用ar。例如：

**mylib.a(hello.o):hello.o**

**ar cr mylib.a hello.o**

$@等于库名libname，$@的取值遵循目录搜索规则。而$%等于成员名member，$%的取值不再遵循目录搜索的规则，$%只能代表member。

当库成员作为依赖时，$<等于libname(member)，$@的取值遵循目录搜索规则，有一点除外：如果依赖文件没有被搜索到或是需要被更新，此时$<代表不再是当它作为目标文件被创建或更新的规则命令中的$@，而是将$@中的libname替换为libname(member)。

# 9  makefile文件惯例

SHELL=/bin/sh         # 设置shell

VPATH=             #设置所有文件搜索的路径（或者在下面用vpath设置）

GPATH=             #通常和VPATH（或者vpath）设置成一致的值

.SUFFIXES：         # 清除原有的后缀列表

.SUFFIXES：.c .o     # 设置makefile中隐含规则使用的后缀

 

CC=                 #使用的编译器（gcc/g++）

CFLAGS=            #编译器的参数（-MM）

 

\# 如果是debug版本就包含该变量的定义；如果是release版本就不包含该变量的定义。

DEBUG=1

ifdef DEBUG

SUFFIX**:=**.dbg

CFLAGS+= -g

else

SUFFIX**:**=

CFLAGS+= -O2

endif

 

EXECNAME**:=**        # 生成的可执行文件的文件名

DEPEND**:=**              # 存放关联信息的文件

 

\# 下面三项将会应用于所有的源程序文件。

\# 如果某源程序文件需要其它库或头文件的支持，则需要对该源程序文件进行单独处理。

LIB**:**=                # 支持库，书写格式：-lname（对应的库为libname.so或libname.a）

INCDIR**:**=            # 搜索头文件的目录

LIBDIR**:**=            # 搜索库文件的目录

 

\# 下面三个目录是确定的

SRCDIR:=            #所有存放源文件的目录

OBJDIR:=            #所有存放目标文件的目录（通常所有的目标文件存放在同一个目

\# 录下。要是存放在多个目录，还需要在makefile中指定各个目标

\# 文件分别存放到哪个目录。）

BINDIR:=            #存放可执行文件的目录（一般也只有一个目录，理由同上。）

 

vpath %.c SRCDIR     # 设置源程序文件的搜索目录

vpath %.h INCDIR     # 设置头文件的搜索目录

vpath %.o OBJDIR     # 设置目标文件的搜索目录

 

\#以下的程序默认OBJDIR和BINDIR都只有一个目录

 

\# SRC的值是所有源程序的文件名（包含路径）

SRC=$(foreach DIR,$(SRCDIR),$(wildcard$(DIR)/*.c))

 

\# OBJ的值是所有目标文件的文件名（包含路径）

OBJ=$(addprefix $(OBJDIR)/,$(patsubst %.c,%.o$(SUFFIX),$(notdir$(SRC))))

 

\# BIN的值是可执行文件的文件名（包含路径）

BIN=$(BINDIR)/$(EXECNAME)$(SUFFIX)

 

.PHONY:default compile install uninstallclean depend tags

 

\# 默认目标，也就是主要目标。

\# 如果默认目标的依赖是一个假想目标，该假想目标的命令就一定会被执行。

defaultall: depend compile tags

 

depend:

@set -e;\

$(CC) -MM $(CFLAGS) -I$(INCDIR)$(SRC)    \

\# sed的功能是在目标文件前面加上路径名，在后面加上后缀。

| sed 's,.∗.∗\.o[ :]*,$(OBJDIR)/\1.o$(SUFFIX):,g'  \

\>$(DEPEND)

 

include $(DEPEND)

 

compile:$(OBJ)

\# 动态链接，在install时需要拷贝动态链接库到合适的目录。

@$(CC) -o $(BIN) $(OBJ) -W1,-Bdynamic$(LIBDIR) $(LIB)

\# 静态链接，在install时不需要拷贝静态态链接库。

@$(CC) -o $(BIN) $(OBJ) -W1,-Bstatic$(LIBDIR) $(LIB)

 

\# 生成目标文件的隐含规则

$(OBJDIR)/%.o$(SUFFIX): %.c

@$(CC) -c -o $@ $(CFLAGS)$(filter %$<,$(SRC))

 

install:

将可执行程序、库文件等拷贝到预定的目录下。

 

uninstall:

 

clean:

@rm -rf $(OBJDIR)

@rm -f $(DEPEND)

 

tags:

@ctags $(INC) $(SRC)

 

**ctags**

功能说明：为源程序文件产生一个tag file。

语法：ctags[--append=yes|no][--links=yes|no][-L 列表文件][-f tag文件名][文件\目录…]

-L        列表文件中指定的文件生成tag文件

-f         设定生成的tag文件的名字（默认是tags）

--append   用生成的tag重写tag文件（默认是no）或者是将生成的tag添加到tag文件中

--links     是否添加symbol links（默认是yes）

--recurse   是否递归处理目录。如果[文件\目录…]为空，而且也没有用-L选项指定列表文

件，则默认递归处理当前目录。

 

 ===========================================================================================

SHELL=/bin/sh         # 设置shell
VPATH=             # 设置所有文件搜索的路径（或者在下面用vpath设置）
GPATH=             # 通常和VPATH（或者vpath）设置成一致的值
.SUFFIXES：         # 清除原有的后缀列表
.SUFFIXES：.c .o     # 设置makefile中隐含规则使用的后缀

CC=                 # 使用的编译器（gcc/g++）
CFLAGS=           # 编译器的参数（-MM）

\# 如果是debug版本就包含该变量的定义；如果是release版本就不包含该变量的定义。
DEBUG=1
ifdef DEBUG
SUFFIX:= .dbg
CFLAGS+= -g
else
SUFFIX:=
CFLAGS+= -O2
endif

EXECNAME:=         # 生成的可执行文件的文件名
DEPEND:=           # 存放关联信息的文件

\# 下面三项将会应用于所有的源程序文件。
\# 如果某源程序文件需要其它库或头文件的支持，则需要对该源程序文件进行单独处理。
LIB:=               # 支持库，书写格式：-lname（对应的库为libname.so或libname.a）
INCDIR:=             # 搜索头文件的目录
LIBDIR:=             # 搜索库文件的目录

\# 下面三个目录是确定的
SRCDIR:=           # 所有存放源文件的目录
OBJDIR:=           # 所有存放目标文件的目录（通常所有的目标文件存放在同一个目
                   \# 录下。要是存放在多个目录，还需要在makefile中指定各个目标
                   \# 文件分别存放到哪个目录。）
BINDIR:=             # 存放可执行文件的目录（一般也只有一个目录，理由同上。）

vpath %.c $(SRCDIR)     # 设置源程序文件的搜索目录
vpath %.h $(INCDIR)     # 设置头文件的搜索目录
vpath %.o $(OBJDIR)     # 设置目标文件的搜索目录

\#以下的程序默认OBJDIR和BINDIR都只有一个目录

\# SRC的值是所有源程序的文件名（包含路径）
SRC=$(foreach DIR,$(SRCDIR),$(wildcard $(DIR)/*.c))

\# OBJ的值是所有目标文件的文件名（包含路径）
OBJ=$(addprefix $(OBJDIR)/,$(patsubst %.c,%.o$(SUFFIX),$(notdir $(SRC))))

\# BIN的值是可执行文件的文件名（包含路径）
BIN=$(BINDIR)/$(EXECNAME)$(SUFFIX)

.PHONY:default compile install uninstall clean depend tags

\# 默认目标，也就是主要目标。
\# 如果默认目标的依赖是一个假想目标，该假想目标的命令就一定会被执行。
default: depend compile tags

depend:
   @set -e;\
   $(CC) -MM $(CFLAGS) -I$(INCDIR) $(SRC)     \
   \# sed的功能是在目标文件前面加上路径名，在后面加上后缀。
   | sed 's,.∗.∗\.o[ :]*,$(OBJDIR)/\1.o$(SUFFIX):,g'   \
   \>$(DEPEND)

include $(DEPEND)

compile:$(OBJ)
   \# 动态链接，在install时需要拷贝动态链接库到合适的目录。
   @$(CC) -o $(BIN) $(OBJ) -W1,-Bdynamic $(LIBDIR) $(LIB)
   \# 静态链接，在install时不需要拷贝静态态链接库。
   @$(CC) -o $(BIN) $(OBJ) -W1,-Bstatic $(LIBDIR) $(LIB)

\# 生成目标文件的隐含规则
$(OBJDIR)/%.o$(SUFFIX): %.c
   @$(CC) -c -o $@ $(CFLAGS) $(filter %$<,$(SRC))

install:
   将可执行程序、库文件等拷贝到预定的目录下。

uninstall:

clean:
   @rm -rf $(OBJDIR)
   @rm -f $(DEPEND)

tags:
   @ctags $(INC) $(SRC)

================================Makefile的一个模板（Simple）============================================================



SHELL:=/bin/sh

.SUFFIXES:

.SUFFIXES:.cpp  .o

 

CC:=g++

CFLAGS+=

 

INCDIR:=.  ./subdir

INCPATH:=$(addprefix-I,$(INCDIR))

 

SRCDIR:=.  ./subdir

SRC:=$(foreachDIR,$(SRCDIR),$(wildcard $(DIR)/*.cpp))

OBJ:=$(patsubst %.cpp,%.o,$(SRC))

BIN:=run

 

.PHONY:build clean

 

build:$(OBJ)

   @$(CC) -o $(BIN) $(OBJ)

%.o:%.cpp

   @$(CC) $(INCPATH) -c$(CFLAGS) -o $@ $<

 

clean:

   @rm -f $(BIN)

   @rm -f $(OBJ)



================================Makefile的一个模板============================================================

**SHELL:=/bin/sh**

 

**.SUFFIXES:**

**.SUFFIXES:.cpp .o**

 

**CC:=g++**

**CFLAGS+=**

 

**EXECNAME:=run**

**DEPEND:=depend**

 

**# external libs (libname.so or libname.a), in format:-lname**

**LIB:=**

**LIBDIR:=**

 

**INCDIR:=.**

**SRCDIR:=.**

**OBJDIR:=.**

**BINDIR:=.**

 

**# initialize**

**SRC:=$( foreach  DIR, $(SRCDIR), $(wildcard $(DIR)/\*.cpp) )**

**OBJ:=$( addprefix  $(OBJDIR)/, $(patsubst %.cpp, %.o, $(notdir $(SRC) ) ) )**

**BIN:=$(BINDIR)/$(EXECNAME)**

 

**.PHONY:default build depend clean**

 

**default:depend build**

 

**depend:**

​    **@set -e;\**

​    **$(CC)  -MM  -I$(INCDIR)  $(SRC)\**

​    **| sed 's, \**\*\*\*\*\*\*\*\*\*\*.\*\*\*\*∗\*\*\*\*\*\*\*\*\*\*\*\*.∗\*\**\*\.o[ :]\*, $(OBJDIR)/\1.o:, g'\**

  **>$(DEPEND)**

 

**include $(DEPEND)**

 

**build:$(OBJ)**

​    **@$(CC)  -o  $(BIN)  $(OBJ) $(LIBDIR)  $(LIB)**

 

**# default rules for obj files**

**$(OBJDIR)/%.o:%.cpp**

​    **@$(CC)  -c  -o  $@  $(CFLAGS)  $( filter %$<, $(SRC) )**

 

**clean:**

​    **@rm  -f  $(DEPEND)**

​    **@rm -f  $(BIN)**

​    **@rm -f  $(OBJ)**