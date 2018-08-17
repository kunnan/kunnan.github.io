---
layout: post
title: Dynamic_protection
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 反调试、反反调试、反注入、hook检测、完整性校验
---

# 前言



加解密方法、动态传参的分析就依赖动态调试去分析动态解密之后的内容、方法传递的参数。

#    反调试[AntiDebug.](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiDebug.h)

> * [code for antiDebug](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiDebug.m)
>
>   * 阻止调试器附加
>
>     * [anti-anti-debugging的例子](https://github.com/zhangkn/KNAlipayWalletTweakDemo/blob/master/AlipayWalletTweakF/AlipayWalletTweakF.xm)
>       * [Anti ptrace 一种反调试](https://everettjf.github.io/2015/12/20/amap-ios-client-kill-anti-debugging-protect/)
>     * [简单的 AntiDebugging 和 AntiAntiDebugging](https://everettjf.github.io/2015/12/28/simple-ios-antidebugging-and-antiantidebugging/)
>
>     
>
>   * 检测调试器是否存在

#### ptrace

> * 为了方便应用软件的开发和调试，unix的早期版本提供了一种对运行中的进程进行跟踪和控制手段：系统调用ptrace;通过ptrace,可以对另一个进程实现调试跟踪，同时ptrace提供了一个`PT_DENY_ATTACH = 31`参数用于告诉系统阻止调试器的依附。
>
>   *   [anti ptrace code]
>
>     * 直接hook ptrace
>
>       ```
>       static int (*oldptrace)(int request, pid_t pid, caddr_t addr, int data);
>       static int newptrace(int request, pid_t pid, caddr_t addr, int data){
>       	return 0; // just return zero
>       /*
>       	// or return oldptrace with request -1
>       	if (request == 31) {
>       		request = -1;
>       	}
>       	return oldptrace(request,pid,addr,data);
>       */
>       }
>       
>       
>       %ctor {
>       	MSHookFunction((void *)MSFindSymbol(NULL,"_ptrace"), (void *)newptrace, (void **)&oldptrace);
>       }
>       
>       ```
>
>       
>
>     * 返回一个假的ptrace函数
>
>       
>
>       ```
>       #import <mach-o/dyld.h>
>       #import <dlfcn.h>
>       void (*old_sub_bf92)(void);
>       
>       void new_sub_bf92(void)
>       {
>           // old_sub_bf92();
>           NSLog(@"KNiOSRE: anti-anti-debugging");
>       }
>       //sub_bf92
>       %ctor
>       {
>           @autoreleasepool
>           {
>               unsigned long _sub_bf92 = (_dyld_get_image_vmaddr_slide(0) + 0xbf92) | 0x1;
>               if (_sub_bf92) NSLog(@"KNiOSRE: Found sub_bf92!");
>                   MSHookFunction((void *)_sub_bf92, (void *)&new_sub_bf92, (void **)&old_sub_bf92);
>           }
>       }
>       
>       ```
>
>       
>
>   * [ptrace code]
>
>     
>
>     * 方式一
>
>       ```
>       #import <mach-o/dyld.h>
>       #import <dlfcn.h>
>       
>       int main(int argc, char * argv[]) {
>       
>       #ifndef DEBUG
>           typedef int (*ptrace_type)(int request, pid_t pid,caddr_t addr,int data);
>           void *handle = dlopen(0, 0xA);
>           ptrace_type pt = (ptrace_type)dlsym(handle, "ptrace");
>           pt(31,0,0,0);
>           dlclose(handle);
>       #endif
>       
>       	//...
>       }	
>       ```
>
>       
>
>     * 方式二
>
>     ```
>     #ifndef PT_DENY_ATTACH
>         #define PT_DENY_ATTACH 31
>     #endif
>     typedef int (*ptrace_ptr_t)(int _request, pid_t _pid, caddr_t _addr, int _data);
>     
>     void AntiDebug_002(){
>         void *handle = dlopen(0, RTLD_GLOBAL | RTLD_NOW);
>         ptrace_ptr_t ptrace_ptr = (ptrace_ptr_t)dlsym(handle, "ptrace");
>         ptrace_ptr(PT_DENY_ATTACH, 0, 0, 0);
>     }
>     
>     ```
>
>     

> * 小结
>
>   * 当程序运行后，使用 `debugserver *:1234 -a BinaryName` 附加进程出现 `segmentfault 11` 时，一般说明程序内部调用了ptrace 。
>   * 为验证是否调用了ptrace 可以 `debugserver -x backboard *:1234 /BinaryPath`（这里是完整路径），然后下符号断点 `b ptrace`，`c` 之后看ptrace第一行代码的位置，然后 `p $lr` 找到函数返回地址，再根据 `image list -o -f` 的ASLR偏移，计算出原始地址。最后在 IDA 中找到调用ptrace的代码，分析如何调用的ptrace
>   * 开始hook ptrace。
>
>    
>
>    



#### sysctl



当一个进程被调试时，该进程中会有一个标记位来标记它正在被调试；利用`sysctl`函数查看当前进程信息，判断是否有此标志位来检测是否处理调试状态。-------`kinfo_proc.kp_proc.p_flag`

> * [code for isDebuggerPresent]
>
>   ```
>   BOOL isDebuggerPresent(){
>       int name[4];                //指定查询信息的数组
>       
>       struct kinfo_proc info;     //查询的返回结果
>       size_t info_size = sizeof(info);
>       
>       info.kp_proc.p_flag = 0;
>       
>       name[0] = CTL_KERN;
>       name[1] = KERN_PROC;
>       name[2] = KERN_PROC_PID;
>       name[3] = getpid();
>       
>       if(sysctl(name, 4, &info, &info_size, NULL, 0) == -1){
>           NSLog(@"sysctl error : %s", strerror(errno));
>           return NO;
>       }
>       
>       return ((info.kp_proc.p_flag & P_TRACED) != 0);// 通过与操作，判断p_flag 是否包含P_TRACED
>   }
>   
>   ```
>
>   
>
> * 存储进行信息的结构体kinfo_proc 
>
>   
>
>   ```
>   struct kinfo_proc {
>   	struct	extern_proc kp_proc;			/* proc structure */
>   	struct	eproc {
>   		struct	proc *e_paddr;		/* address of proc */
>   		struct	session *e_sess;	/* session pointer */
>   		struct	_pcred e_pcred;		/* process credentials */
>   		struct	_ucred e_ucred;		/* current credentials */
>   		struct	 vmspace e_vm;		/* address space */
>   		pid_t	e_ppid;			/* parent process id */
>   		pid_t	e_pgid;			/* process group id */
>   		short	e_jobc;			/* job control counter */
>   		dev_t	e_tdev;			/* controlling tty dev */
>   		pid_t	e_tpgid;		/* tty process group id */
>   		struct	session *e_tsess;	/* tty session pointer */
>   #define	WMESGLEN	7
>   		char	e_wmesg[WMESGLEN+1];	/* wchan message */
>   		segsz_t e_xsize;		/* text size */
>   		short	e_xrssize;		/* text rss */
>   		short	e_xccount;		/* text references */
>   		short	e_xswrss;
>   		int32_t	e_flag;
>   #define	EPROC_CTTY	0x01	/* controlling tty vnode active */
>   #define	EPROC_SLEADER	0x02	/* session leader */
>   #define	COMAPT_MAXLOGNAME	12
>   		char	e_login[COMAPT_MAXLOGNAME];	/* short setlogin() name */
>   		int32_t	e_spare[4];
>   	} kp_eproc;
>   };
>   
>   ```
>
>   * `	struct	extern_proc kp_proc;			/* proc structure */ ` 包含是否正在被调试的标志位
>
>     * Exported fields for kern sysctls  : extern_proc  中包含是否正在被调试的标志位 ``int	p_flag;			/* P_* flags. */ `
>
>       ```
>       /* Exported fields for kern sysctls */
>       struct extern_proc {
>       	union {
>       		struct {
>       			struct	proc *__p_forw;	/* Doubly-linked run/sleep queue. */
>       			struct	proc *__p_back;
>       		} p_st1;
>       		struct timeval __p_starttime; 	/* process start time */
>       	} p_un;
>       #define p_forw p_un.p_st1.__p_forw
>       #define p_back p_un.p_st1.__p_back
>       #define p_starttime p_un.__p_starttime
>       	struct	vmspace *p_vmspace;	/* Address space. */
>       	struct	sigacts *p_sigacts;	/* Signal actions, state (PROC ONLY). */
>       	int	p_flag;			/* P_* flags. */
>       	char	p_stat;			/* S* process status. */
>       	pid_t	p_pid;			/* Process identifier. */
>       	pid_t	p_oppid;	 /* Save parent pid during ptrace. XXX */
>       	int	p_dupfd;	 /* Sideways return value from fdopen. XXX */
>       	/* Mach related  */
>       	caddr_t user_stack;	/* where user stack was allocated */
>       	void	*exit_thread;	/* XXX Which thread is exiting? */
>       	int		p_debugger;		/* allow to debug */
>       	boolean_t	sigwait;	/* indication to suspend */
>       	/* scheduling */
>       	u_int	p_estcpu;	 /* Time averaged value of p_cpticks. */
>       	int	p_cpticks;	 /* Ticks of cpu time. */
>       	fixpt_t	p_pctcpu;	 /* %cpu for this process during p_swtime */
>       	void	*p_wchan;	 /* Sleep address. */
>       	char	*p_wmesg;	 /* Reason for sleep. */
>       	u_int	p_swtime;	 /* Time swapped in or out. */
>       	u_int	p_slptime;	 /* Time since last blocked. */
>       	struct	itimerval p_realtimer;	/* Alarm timer. */
>       	struct	timeval p_rtime;	/* Real time. */
>       	u_quad_t p_uticks;		/* Statclock hits in user mode. */
>       	u_quad_t p_sticks;		/* Statclock hits in system mode. */
>       	u_quad_t p_iticks;		/* Statclock hits processing intr. */
>       	int	p_traceflag;		/* Kernel trace points. */
>       	struct	vnode *p_tracep;	/* Trace to vnode. */
>       	int	p_siglist;		/* DEPRECATED. */
>       	struct	vnode *p_textvp;	/* Vnode of executable. */
>       	int	p_holdcnt;		/* If non-zero, don't swap. */
>       	sigset_t p_sigmask;	/* DEPRECATED. */
>       	sigset_t p_sigignore;	/* Signals being ignored. */
>       	sigset_t p_sigcatch;	/* Signals being caught by user. */
>       	u_char	p_priority;	/* Process priority. */
>       	u_char	p_usrpri;	/* User-priority based on p_cpu and p_nice. */
>       	char	p_nice;		/* Process "nice" value. */
>       	char	p_comm[MAXCOMLEN+1];
>       	struct 	pgrp *p_pgrp;	/* Pointer to process group. */
>       	struct	user *p_addr;	/* Kernel virtual addr of u-area (PROC ONLY). */
>       	u_short	p_xstat;	/* Exit status for wait; also stop signal. */
>       	u_short	p_acflag;	/* Accounting flags. */
>       	struct	rusage *p_ru;	/* Exit information. XXX */
>       };
>       
>       ```
>
>       * 当p_flag  为`#define	P_TRACED	0x00000800	/* Debugged process being traced */ ` 的时候代表正在被调试
>
>         ```
>             return ((info.kp_proc.p_flag & P_TRACED) != 0);
>         
>         ```
>
>         



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dynamic_protection 反调试、反反调试、反注入、hook检测、完整性校验 -t security
> #原来""的参数，需要自己加上""
```

