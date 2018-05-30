---
layout: post
title: Mach-O_introduce
date: 2018-05-29
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Mach-O基础知识
---



# mach-o


![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsamldr58j30ah0bvaaw.jpg)

`mach-o`记录编译后的可执行文件，对象代码，共享库，动态加载代码和内存转储的文件格式。



>* /usr/include/mach-o

```
devzkndeMacBook-Pro:mach-o devzkn$ tree -L 4
.
├── arch.h
├── arm64
│   └── reloc.h
├── compact_unwind_encoding.h
├── dyld.h
├── dyld_images.h
├── fat.h
├── getsect.h
├── i386
│   └── swap.h
├── ldsyms.h
├── loader.h
├── module.map
├── nlist.h
├── ppc
│   ├── reloc.h
│   └── swap.h
├── ranlib.h
├── reloc.h
├── stab.h
├── swap.h
└── x86_64
    └── reloc.h
```

>* `/usr/include/mach-o/loader.h` 重点看

```objc
#include <mach-o/loader.h>
```


####  [Mach-O 文件包含三个区域](https://zhangkn.github.io/2018/03/Hopper/)

>* 1、Mach-O Header：包含字节顺序，magic，cpu 类型，加载指令的数量等


>* 2、Load Commands：包含很多内容的表，包括区域的位置，符号表，动态符号表等。每个加载指令包含一个元信息，比如指令类型，名称，在二进制中的位置等。
>

>* 3、Data：最大的部分，包含了代码，数据，比如符号表，动态符号表等。


#### The existing load commands are listed below:

>* LC_SEGMENT — contains different information on a certain segment: size, number of sections, offset in the file and in memory (after the load)

>* LC_SYMTAB — loads the table of symbols and strings

>* LC_DYSYMTAB — creates an import table; data on symbols is taken from the symbol table

>* LC_LOAD_DYLIB — defines the dependency from a certain third-party library

#### The most important segments are the following

>* __TEXT — the executed code and other read-only data

>* __DATA — data available for writing; including import tables that can be changed by the dynamic loader during lazy binding

>* __OBJC — different information of the standard library of Objective-C language of execution time

>* __IMPORT — import table only for 32-bit architecture (I managed to generate it only on Mac OS 10.5)

>* __LINKEDIT — here, the dynamic loader places its data for already loaded modules (symbol tables, string tables, etc.)



# I、Header

使系统能够快速定位其运行环境以及文件类型

![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsbs2qie6j30c20aoweq.jpg)

>* struct 

```
struct mach_header_64 {
	uint32_t	magic;		/* mach magic number identifier */
	cpu_type_t	cputype;	/* cpu specifier */
	cpu_subtype_t	cpusubtype;	/* machine specifier */
	uint32_t	filetype;	/* type of file */
	uint32_t	ncmds;		/* number of load commands */
	uint32_t	sizeofcmds;	/* the size of all the load commands */
	uint32_t	flags;		/* flags */
	uint32_t	reserved;	/* reserved */
};
```


>* `Fat Binary` Fat Header的数据结构在<mach-o/fat.h>头文件上有定义：
![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsbs8ohnfj30c40owgmj.jpg)

```objc
#define FAT_MAGIC	0xcafebabe
#define FAT_CIGAM	0xbebafeca	/* NXSwapLong(FAT_MAGIC) */
struct fat_header {
  uint32_t	magic;		/* FAT_MAGIC */
  uint32_t	nfat_arch;	/* number of structs that follow */
};
//说明对应Mach-O文件大小、支持的CPU架构、偏移地址
struct fat_arch {
  cpu_type_t	cputype;	/* cpu specifier (int) */
  cpu_subtype_t	cpusubtype;	/* machine specifier (int) */
  uint32_t	offset;		/* file offset to this object file */
  uint32_t	size;		/* size of this object file */
  uint32_t	align;		/* alignment as a power of 2 */
};
```

从上面的例子，可以看出胖文件的fat_magic是0xcafebabe；顺便说一下64位的Mach-O文件的魔数值为#define MH_MAGIC_64 0xfeedfacf

```
file 就是根据Magic 来判断的
file ~/tmp
/Users/devzkn/tmp: Mach-O universal binary with 2 architectures: [arm_v7:Mach-O dynamically linked shared library arm_v7] [arm64:Mach-O 64-bit dynamically linked shared library arm64]
```

[magic 存放的位置在/usr/share/file/magic，更多信息请看这里](https://zhangkn.github.io/2017/12/usefulCommand/)


>* ` otool -h tmp`  print the mach header


# II、 Load Commands

这些加载命令在Mach-O文件加载解析时，被内核加载器或者动态链接器调用，指导如何设置加载对应的二进制数据段；


>* Any load command starts with the following fields:

```objc
struct load_command
{
  uint32_t cmd;  //command numeric code
  uint32_t cmdsize;  //size of the current command in bytes
};
```

>* Load Commands 是跟在 Header 后面的加载命令区.

```
<!-- 所有 commands 的大小总和即为 Header->sizeofcmds 字段，共有 Header->ncmds 条加载命令。 -->
Mach header
      magic cputype cpusubtype  caps    filetype ncmds sizeofcmds      flags
 0xfeedface      12          9  0x00           6    33       3640 0x00100085

```
>* `otool -l`: `OS X/iOS`发展到今天，已经有40多条加载命令，其中部分是由内核加载器直接使用，而其他则是由动态链接器处理。其中几个主要的Load Commend为`LC_SEGMENT`,` LC_LOAD_DYLINKER`, `LC_UNIXTHREAD`,` LC_MAIN`[LC_SEGMENT 的数据结构的更多信息请查看这里](https://zhangkn.github.io/2017/12/frida/)

```
<!-- 看加载命令区 -->
devzkndeMacBook-Pro:~ devzkn$ otool -l tmp
tmp (architecture armv7):
Mach header
      magic cputype cpusubtype  caps    filetype ncmds sizeofcmds      flags
 0xfeedface      12          9  0x00           6    33       3640 0x00100085

<!--LC_SEGMENT_64  将对应的段中的数据加载并映射到进程的内存空间去 -->
Load command 0
      cmd LC_SEGMENT
  cmdsize 668
  segname __TEXT
   vmaddr 0x00000000
   vmsize 0x00044000

   Load command 1
      cmd LC_SEGMENT
  cmdsize 1144
    segname __DATA
Load command 2
      cmd LC_SEGMENT
  cmdsize 56
  segname __LINKEDIT
   vmaddr 0x0004c000
   vmsize 0x00028000
  fileoff 311296
 filesize 154080
  maxprot 0x00000001
 initprot 0x00000001
   nsects 0
    flags 0x0
Load command 3
          cmd LC_ID_DYLIB
      cmdsize 84
         name /Library/MobileSubstrate/DynamicLibraries/.dylib (offset 24)
   time stamp 1 Thu Jan  1 08:00:01 1970
      current version 1.0.0
compatibility version 1.0.0

Load command 4
            cmd LC_DYLD_INFO_ONLY
        cmdsize 48
     rebase_off 311296

<!-- 符号表信息 -->
Load command 5
     cmd LC_SYMTAB
 cmdsize 24
  symoff 323260
   nsyms 4023
  stroff 372600
 strsize 76988
 <!-- LC_DYSYMTAB符号表  动态符号表信息
 -->
Load command 6
            cmd LC_DYSYMTAB
        cmdsize 80
      ilocalsym 0
<!-- 唯一的 UUID，标示该二进制文件，128bit -->
Load command 7
     cmd LC_UUID
 cmdsize 24
    uuid 5A3F7C11---ADC9-

<!-- 要求的最低系统版本（Xcode中的Deployment Target） -->
Load command 8
      cmd LC_VERSION_MIN_IPHONEOS
  cmdsize 16
  version 9.1
      sdk 10.3

Load command 9
      cmd LC_SOURCE_VERSION
  cmdsize 16
  version 0.0
  <!-- 加密信息   grep cryptid 可以查看是否加密。 -->
Load command 10
          cmd LC_ENCRYPTION_INFO
      cmdsize 20
     cryptoff 16384
    cryptsize 262144
      cryptid 0
<!-- 加载的动态库，包括动态库地址、名称、版本号等 load a dynamically linked shared library,load a dynamically linked shared library that is allowed to be missing -->
Load command 11
          cmd LC_LOAD_DYLIB
      cmdsize 96
         name /System/Library/PrivateFrameworks/StoreServices.framework/StoreServices (offset 24) 名称
   time stamp 2 Thu Jan  1 08:00:02 1970
      current version 1440.12.0  版本号
compatibility version 1.0.0

Load command 27
          cmd LC_RPATH
      cmdsize 32
         path @executable_path (offset 12)

         Load command 28
          cmd LC_RPATH
      cmdsize 56
         path /Library/MobileSubstrate/DynamicLibraries (offset 12)
Load command 29
          cmd LC_RPATH
      cmdsize 36
         path /var/mobile/frameworks/ (offset 12)

<!-- 函数地址起始表 -->

Load command 30
      cmd LC_FUNCTION_STARTS
  cmdsize 16
  dataoff 322000
 datasize 852

Load command 31
      cmd LC_DATA_IN_CODE
  cmdsize 16
  dataoff 322852
 datasize 408
 <!-- 代码签名信息 : code_signature-->
Load command 32
      cmd LC_CODE_SIGNATURE
  cmdsize 16
  dataoff 449600
 datasize 15776

```

#### Segment


Mach-O 文件有多个段（Segment），每个段有不同的功能。然后每个段又分为很多小的 Section。 LC_SEGMENT 意味着这部分文件需要映射到进程的地址空间去

>* section的命名规则

```
命名规则标识段-区的表示方法为(__SEGMENT.__section)SEGMENT所有字母大写，加两个下横线作为前缀；section为小写，同样加两个下横线作为前缀。
```

>* Raw segment data
>```
>一般Mach-O文件有多个段(Segement)，段每个段有不同的功能;
1). __PAGEZERO: 空指针陷阱段，映射到虚拟内存空间的第一页，用于捕捉对NULL指针的引用；<br>
2). __TEXT: 包含了执行代码以及其他只读数据。该段数据的保护级别为：VM_PROT_READ（读）、VM_PROT_EXECUTE(执行)，防止在内存中被修改；<br>
3). __DATA: 包含了程序数据，该段可写；<br>
4). __OBJC: Objective-C运行时支持库；<br>
5). __LINKEDIT: 链接器使用的符号以及其他表<br>
一般的段又会按不同的功能划分为几个区（section）。
>```
>

 
>* `otool -o`
>
```
devzkndeMacBook-Pro:segment_dumper devzkn$ otool -o ~/tmp
/Users/devzkn/tmp (architecture armv7):
Contents of (__DATA,__objc_classlist) section
```

```
devzkndeMacBook-Pro:knMoknKKKKKKokkn.decrypted.4.0 devzkn$ otool -o MKNooJn.dec* |grep password
            name 0x82b11e passwordTextFieldFrame
            name 0x82b135 passwordPlaceholder
                name 0x82b11e passwordTextFieldFrame
                name 0x82b135 passwordPlaceholder
                name 0x82b11e passwordTextFieldFrame
                name 0x82b135 passwordPlaceholder
```

###### LC_SEGMENT(__TEXT) Number of Sections + LC_SEGMENT(__DATA) Number of Sections = Sections Number of sections


![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsaz63dygj30wt0gewim.jpg)

####  [LC_DYSYMTAB符号表](https://zhangkn.github.io/2018/03/symbolicatecrash/)

LC_DYSYMTAB符号表有非常大的作用，捕获到线上 Crash 或者 卡顿 堆栈的地址信息时，需要进行符号还原，进而确认卡顿、崩溃的具体位置，这个使用就要使用到LC_DYSYMTAB符号表；

>* 结构

```
struct symtab_command {
    uint32_t    cmd;        /* LC_SYMTAB */
    uint32_t    cmdsize;    /* sizeof(struct symtab_command) */
    uint32_t    symoff;        /* symbol table offset */
    uint32_t    nsyms;        /* number of symbol table entries */
    uint32_t    stroff;        /* string table offset */
    uint32_t    strsize;    /* string table size in bytes */
};

<!-- 符号表在 Mach-O目标文件中的地址可以通过LC_SYMTAB加载命令指定的 symoff找到，对应的符号名称在stroff，总共有nsyms条符号信息 -->

```

# III、 Section


一个可执行文件包含多个段, 在每一个段内有一些片段。它们包含了可执行文件的不同的部分, 包含了部分的源码及DATA.



```objc 
struct section
{
  char sectname[16];
  char segname[16];
  uint32_t addr;
  uint32_t size;
  uint32_t offset;
  uint32_t align;
  uint32_t reloff;
  uint32_t nreloc;
  uint32_t flags;
  uint32_t reserved1;
  uint32_t reserved2;
};
```

>* sections

```
__TEXT,__text — the code itself

__TEXT,__cstring — constant strings (in double quotes)

__TEXT,__const — different constants

__DATA,__data — initialized variables (strings and arrays)

__DATA,__la_symbol_ptr — table of pointers to imported functions

__DATA,__bss — non-initialized static variables

__IMPORT,__jump_table — stubs for calls of imported functions
```


它的结构体跟随在 LC_SEGMENT 结构体之后，LC_SEGMENT 又在 Load Commands 中，
segment 的数据内容是跟在 Load Commands 之后的。




#### 作用
>* 各节的作用

<table>
  <thead>
    <tr>
      <th>Section</th>
      <th style="text-align: center">作用</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>__text:　</td>
      <td style="text-align: center">主程序代码 </td>
    </tr>
    <tr>
      <td>__stub_helper:　</td>
      <td style="text-align: center"> 用于动态链接的存根</td>
    </tr>
    <tr>
      <td>__symbolstub1:　</td>
      <td style="text-align: center"> 用于动态链接的存根</td>
    </tr>
    <tr>
      <td>__objc_methname:</td>
      <td style="text-align: center"> 　Objective-C 的方法名</td>
    </tr>
    <tr>
      <td>__objc_classname:</td>
      <td style="text-align: center">　Objective-C 的类名 </td>
    </tr>
    <tr>
      <td>__cstring:</td>
      <td style="text-align: center"> 　硬编码的字符串</td>
    </tr>
  </tbody>
</table>

<table>
  <tbody>
    <tr>
      <td>__lazy_symbol:　懒加载，延迟加载节，通过 dyld_stub_binder 辅助链接</td>
    </tr>
    <tr>
      <td>_got:　存储引用符号的实际地址，类似于动态符号表</td>
    </tr>
    <tr>
      <td>__nl_symbol_ptr:　非延迟加载节</td>
    </tr>
    <tr>
      <td>__mod_init_func:　初始化的全局函数地址，在 main 之前被调用</td>
    </tr>
    <tr>
      <td>__mod_term_func:　结束函数地址</td>
    </tr>
    <tr>
      <td>__cfstring:　Core Foundation 用到的字符串（OC字符串）</td>
    </tr>
  </tbody>
</table>

<table>
  <tbody>
    <tr>
      <td>__objc_clsslist:　Objective-C 的类列表</td>
    </tr>
    <tr>
      <td>__objc_nlclslist:　Objective-C 的 +load 函数列表，比 __mod_init_func 更早执行</td>
    </tr>
    <tr>
      <td>__objc_const:　Objective-C 的常量</td>
    </tr>
    <tr>
      <td>__data:　初始化的可变的变量</td>
    </tr>
    <tr>
      <td>__bss:　未初始化的静态变量</td>
    </tr>
  </tbody>
</table>


>* Section 是具体有用的数据存放的地方
<table>
  <thead>
    <tr>
      <th>Section</th>
      <th style="text-align: center">作用</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>TEXT.text</td>
      <td style="text-align: center">只有可执行的机器码</td>
    </tr>
    <tr>
      <td>TEXT.cstring</td>
      <td style="text-align: center">去重后的C字符串（不含中文字符）</td>
    </tr>
    <tr>
      <td>TEXT.const</td>
      <td style="text-align: center">初始化过的常量</td>
    </tr>
    <tr>
      <td>TEXT.stubs</td>
      <td style="text-align: center">符号桩。本质上是一小段会直接跳入lazybinding的表对应项指针指向的地址的代码。</td>
    </tr>
    <tr>
      <td>TEXT.stub_helper</td>
      <td style="text-align: center">辅助函数。上述提到的lazybinding的表中对应项的指针在没有找到真正的符号地址的时候，都指向这。</td>
    </tr>
    <tr>
      <td>TEXT.unwind_info</td>
      <td style="text-align: center">用于存储处理异常情况信息</td>
    </tr>
    <tr>
      <td>TEXT.eh_frame</td>
      <td style="text-align: center">调试辅助信息</td>
    </tr>
    <tr>
      <td>DATA.data</td>
      <td style="text-align: center">初始化过的可变的（静态/全局）数据</td>
    </tr>
    <tr>
      <td>DATA.nl_symbol_ptr</td>
      <td style="text-align: center">非lazy-binding的指针表，每个表项中的指针都指向一个在装载过程中，被动态链机器搜索完成的符号</td>
    </tr>
    <tr>
      <td>DATA.la_symbol_ptr</td>
      <td style="text-align: center">lazy-binding的指针表，每个表项中的指针一开始指向stub_helper</td>
    </tr>
    <tr>
      <td>DATA.const</td>
      <td style="text-align: center">没有初始化过的常量</td>
    </tr>
    <tr>
      <td>DATA.mod_init_func</td>
      <td style="text-align: center">初始化函数，在main之前调用</td>
    </tr>
    <tr>
      <td>DATA.mod_term_func</td>
      <td style="text-align: center">终止函数，在main返回之后调用</td>
    </tr>
    <tr>
      <td>DATA.bss</td>
      <td style="text-align: center">没有初始化的（静态/全局）变量</td>
    </tr>
    <tr>
      <td>DATA.common</td>
      <td style="text-align: center">没有初始化过的符号声明</td>
    </tr>
    <tr>
      <td>TEXT.ustring</td>
      <td style="text-align: center">utf-8编码后的中文字符串</td>
    </tr>
    <tr>
      <td>TEXT.objc_methname</td>
      <td style="text-align: center">OC方法名（不含c方法，所有的方法都在TEXT.text中）</td>
    </tr>
    <tr>
      <td>DATA.cfstring</td>
      <td style="text-align: center">用OC方法创建的字符串，对TEXT段中字符串的引用</td>
    </tr>
    <tr>
      <td>DATA.__objc _classlist节</td>
      <td style="text-align: center">这个节列出了所有的class（metaclass自身也是一种class）。</td>
    </tr>
    <tr>
      <td>DATA.__objc _catlist</td>
      <td style="text-align: center">代表的就是程序里面有哪些Category</td>
    </tr>
    <tr>
      <td>DATA.__objc_protolist</td>
      <td style="text-align: center">代表的就是程序里面有哪些Protocol</td>
    </tr>
    <tr>
      <td>DATA.__objc_classrefs</td>
      <td style="text-align: center">该节是为了标记这个类究竟有没有被引用</td>
    </tr>
    <tr>
      <td>DATA.__objc_selrefs</td>
      <td style="text-align: center">告诉你究竟有哪些SEL对应的字符串被引用了</td>
    </tr>
    <tr>
      <td>DATA.__objc_superrefs</td>
      <td style="text-align: center">在编译期指定方法对应的current_class，以方便后续的superclass方法列表查找</td>
    </tr>
    <tr>
      <td>DATA.__objc_const</td>
      <td style="text-align: center">存放的是一些需要在类加载过程中用到的readonly data</td>
    </tr>
  </tbody>
</table>



#### `otool -t ` print the text section (disassemble with -v)


>* `otool -tv tmp`
>
>* `otool -sv __TEXT __cstring tmp`
>



####  code_signature

里面包含了程序代码的签名，这个签名的作用就是保证签名后 .app 里的文件，包括资源文件，Mach-O 文件都不能够更改。


```
<!-- 代码签名信息 : code_signature-->
Load command 32
      cmd LC_CODE_SIGNATURE
  cmdsize 16
  dataoff 449600
 datasize 15776
```



# IV、  Sequence of Steps in Executing an Image

>* flow_of_process_execution

![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsb3zo76kj315g0tmjui.jpg)
Flow of the various process execution functions in OS X 引用自《Mac OS X and iOS Internals》P519

>* parse_machfile()
>
```
(1)：解析线程状态，UUID和代码签名。相关命令为LC_UNIXTHREAD、LC_MAIN、LC_UUID、LC_CODE_SIGNATURE 
(2)：解析代码段Segment。相关命令为LC_SEGMENT、LC_SEGMENT_64；
(3)：解析动态链接库、加密信息。相关命令为：LC_ENCRYPTION_INFO、LC_ENCRYPTION_INFO_64、LC_LOAD_DYLINKER
```


>* load 函数是如何被调用的
>
![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsb50x2ncj30d00ultbr.jpg)

dyld 是Apple 的动态链接器；在 xnu 内核为程序启动做好准备后，就会将 PC 控制权交给 dyld 负责剩下的工作 （dyld 是运行在 用户态的， 这里由 内核态 切到了用户态）.


# V 、app 的启动过程


>* 1、内核（OS Kernel）创建一个进程，分配虚拟的进程空间等等，加载动态链接器。
>* 2、通过动态链接器加载主二进制程序引用的库、绑定符号。
>* 3、启动程序
>


>* mach-o_execution:
>
![image](https://wx4.sinaimg.cn/large/006tBeITly1frsb6eabgqj30ge0gk75n.jpg)

>* Darwin体系
```
1)Darwin是一种类似unix的操作系统，他的核心是XNU。
2)XNU是一种混合式内核。结合了mach与BSD两种内核。
3)Mach 是微内核实现。
4)BSD 实现在Mach的上层，这一层提供的API 支持了POSIX标准模型。在XNU中主要实现了一些高级的API与模块。
```

![image](https://wx4.sinaimg.cn/large/006tBeITly1frsb8319t6j30u60x0dq8.jpg)

# VI 、iOS安全机制


>*  代码签名:
- 强制访问控制（Mandatory Access Control):系统检测安全属性以便确定一个用户是否有权访问该文件
- sandbox: 沙盒在启动的时候可以设置运行的程序是否可以访问网络、文件、目录


![image](https://wx4.sinaimg.cn/large/006tBeITgy1frsb9zjaprj30gp0an0uv.jpg)

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Mach-O_introduce Mach-O基础知识 -t c Mach-O
> #原来""的参数，需要自己加上""
```

