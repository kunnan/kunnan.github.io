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
>
>
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


>* Fat Binary

```objc
struct fat_header
{
  uint32_t magic;
  uint32_t nfat_arch;
};


struct fat_arch
{
  cpu_type_t cputype;
  cpu_subtype_t cpusubtype;
  uint32_t offset;
  uint32_t size;
  uint32_t align;
};

```


>* ` otool -h tmp`
>


# II、 Load Commands


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
>* otool -l

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
<!-- 加载的动态库，包括动态库地址、名称、版本号等 -->
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


>* 段名

```
__PAGEZERO:　空指针陷阱段，映射到虚拟内存空间的第一页，用于捕捉对 NULL 指针的引用。


__TEXT:　包含了执行代码以及其他只读数据。该段数据可以 VM_PROT_READ(读)、VM_PROT_EXECUTE(执行)，不能被修改。


__DATA:　程序数据，该段可写 VM_PROT_WRITE/READ/EXECUTE。


__LINKEDIT:　链接器使用的符号以及其他表。


```
 


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


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Mach-O_introduce Mach-O基础知识 -t c Mach-O
> #原来""的参数，需要自己加上""
```

