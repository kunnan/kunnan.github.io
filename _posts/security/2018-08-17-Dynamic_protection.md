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





#### syscall



`int	 syscall(int, ...); `

为了实现从用户态到内核态的切换，系统提供了 系统调用syscall函数，从下面可以知道ptrace对应的编号是26，因此`syscall(26,31,0,0,0);`  相当于调用了ptrace `ptrace(PT_DENY_ATTACH, 0, 0, 0);`

> * [Kernel Syscalls](https://www.theiphonewiki.com/wiki/Kernel_Syscalls)
>
>   * List of system calls from [iOS](https://www.theiphonewiki.com/wiki/IOS) 6.0 GM: `$ joker -u ~/Documents/projects/iOS.6.0.iPod4.kernel `
>
>     ```
>     Suppressing enosys (0x800b3429)  T = Thumb
>     1. exit                  801d4a74 T
>     2. fork                  801d7980 T
>     3. read                  801eb584 T
>     4. write                 801eb958 T
>     5. open                  800b13a4 T
>     6. close                 801ccab4 T
>     7. wait4                 801d56bc T
>     9. link                  800b18e8 T
>     10. unlink               800b1ff0 T
>     12. chdir                800b0c60 T
>     13. fchdir               800b0af0 T
>     14. mknod                800b14bc T
>     15. chmod                800b2b40 T
>     16. chown                800b2c9c T
>     18. getfsstat            800b088c T
>     20. getpid               801dc20c T
>     23. setuid               801dc4c0 T
>     24. getuid               801dc290 T
>     25. geteuid              801dc2a0 T
>     26. ptrace               801e812c T
>     27. recvmsg              8020a8fc T
>     28. sendmsg              8020a444 T
>     29. recvfrom             8020a528 T
>     30. accept               80209dfc T
>     31. getpeername          8020abc8 T
>     32. getsockname          8020ab18 T
>     33. access               800b24ac T
>     34. chflags              800b2928 T
>     35. fchflags             800b29f0 T
>     36. sync                 800b0320 T
>     37. kill                 801dfdcc T
>     39. getppid              801dc214 T
>     41. dup                  801cab04 T
>     42. pipe                 801edbe4 T
>     43. getegid              801dc318 T
>     46. sigaction            801deee8 T
>     47. getgid               801dc308 T
>     48. sigprocmask          801df42c T
>     49. getlogin             801dd0e8 T
>     50. setlogin             801dd160 T
>     51. acct                 801c54ec T
>     52. sigpending           801df5d0 T
>     53. sigaltstack          801dfd10 T
>     54. ioctl                801ebd1c T
>     55. reboot               801e8090 T
>     56. revoke               800b43f8 T
>     57. symlink              800b1b58 T
>     58. readlink             800b282c T
>     59. execve               801d4448 T
>     60. umask                800b43d0 T
>     61. chroot               800b0d30 T
>     65. msync                801d84d0 T
>     66. vfork                801d7018 T
>     73. munmap               801d857c T
>     74. mprotect             801d85b0 T
>     75. madvise              801d8668 T
>     78. mincore              801d86d4 T
>     79. getgroups            801dc328 T
>     80. setgroups            801dd02c T
>     81. getpgrp              801dc21c T
>     82. setpgid              801dc3c8 T
>     83. setitimer            801e7b78 T
>     85. swapon               8021be68 T
>     86. getitimer            801e7a30 T
>     89. getdtablesize        801ca6dc T
>     90. dup2                 801caf54 T
>     92. fcntl                801cb420 T
>     93. select               801ebfc8 T
>     95. fsync                800b3238 T
>     96. setpriority          801dd494 T
>     97. socket               802098a4 T
>     98. connect              80209e1c T
>     100. getpriority          801dd388 T
>     104. bind                 80209970 T
>     105. setsockopt           8020aa30 T
>     106. listen               80209adc T
>      111. sigsuspend           801df5f8 T
>     116. gettimeofday         801e7840 T
>     117. getrusage            801de22c T
>     118. getsockopt           8020aa94 T
>     120. readv                801eb810 T
>     121. writev               801ebbb0 T
>     122. settimeofday         801e789c T
>     123. fchown               800b2dac T
>     124. fchmod               800b2c70 T
>     126. setreuid             801dc80c T
>     127. setregid             801dcba0 T
>     128. rename               800b3428 T
>     131. flock                801ce20c T
>     132. mkfifo               800b1798 T
>     133. sendto               8020a168 T
>     134. shutdown             8020aa00 T
>     135. socketpair           8020a00c T
>     136. mkdir                800b3d1c T
>     137. rmdir                800b3d5c T
>     138. utimes               800b2e60 T
>     139. futimes              800b3034 T
>     140. adjtime              801e79a0 T
>     142. gethostuuid          801ed6a4 T
>     147. setsid               801dc384 T
>     151. getpgid              801dc224 T
>     152. setprivexec          801dc1f4 T
>     153. pread                801eb774 T
>     154. pwrite               801ebad0 T
>     157. statfs               800b03c0 T
>     158. fstatfs              800b0678 T
>     159. unmount              800afe88 T
>     165. quotactl             800b03bc T
>     167. mount                800af068 T
>     169. csops                801dafd0 T
>     170. 170  old table       801db4bc T
>     173. waitid               801d5ab4 T
>     180. kdebug_trace         801c2db4 T
>     181. setgid               801dc9a4 T
>     182. setegid              801dcab0 T
>     183. seteuid              801dc710 T
>     184. sigreturn            8021e7e4 T
>     185. chud                 8021d4f4 T
>     187. fdatasync            800b32b0 T
>     188. stat                 800b2588 T
>     189. fstat                801ccfec T
>     190. lstat                800b26d4 T
>     191. pathconf             800b27c8 T
>     192. fpathconf            801cd048 T
>     194. getrlimit            801de074 T
>     195. setrlimit            801dd93c T
>     196. getdirentries        800b3f94 T
>     197. mmap                 801d7fc0 T
>     199. lseek                800b2068 T
>     200. truncate             800b30b4 T
>     201. ftruncate            800b3174 T
>     202. __sysctl             801e2478 T
>     203. mlock                801d8820 T
>     204. munlock              801d8878 T
>     205. undelete             800b1cf0 T
>     216. mkcomplex            800b12c4 T
>     220. getattrlist          8009b060 T
>     221. setattrlist          8009b0d8 T
>     222. getdirentriesattr    800b44e0 T
>     223. exchangedata         800b469c T
>     225. searchfs             800b48dc T
>     226. delete               800b202c T
>     227. copyfile             800b32cc T
>     228. fgetattrlist         80098488 T
>     229. fsetattrlist         8009b7e0 T
>     230. poll                 801ec72c T
>     231. watchevent           801ed054 T
>     232. waitevent            801ed1f8 T
>     233. modwatch             801ed368 T
>     234. getxattr             800b5550 T
>     235. fgetxattr            800b568c T
>     236. setxattr             800b578c T
>     237. fsetxattr            800b5898 T
>     238. removexattr          800b5994 T
>     239. fremovexattr         800b5a5c T
>     240. listxattr            800b5b1c T
>     241. flistxattr           800b5c00 T
>     242. fsctl                800b4dd4 T
>     243. initgroups           801dcea8 T
>     244. posix_spawn          801d351c T
>     245. ffsctl               800b5474 T
>     250. minherit             801d8630 T
>     266. shm_open             8020eb24 T
>     267. shm_unlink           8020f604 T
>     268. sem_open             8020df80 T
>     269. sem_close            8020e718 T
>     270. sem_unlink           8020e4e0 T
>     271. sem_wait             8020e76c T
>     272. sem_trywait          8020e834 T
>     273. sem_post             8020e8d8 T
>     274. sem_getvalue         8020e97c T
>     275. sem_init             8020e974 T
>     276. sem_destroy          8020e978 T
>     277. open_extended        800b11d8 T
>     278. umask_extended       800b4380 T
>     279. stat_extended        800b2530 T
>     280. lstat_extended       800b267c T
>     281. fstat_extended       801ccdd0 T
>     282. chmod_extended       800b2a30 T
>     283. fchmod_extended      800b2b74 T
>     284. access_extended      800b21a0 T
>     285. settid               801dcd2c T
>     286. gettid               801dc2b0 T
>     287. setsgroups           801dd03c T
>     288. getsgroups           801dc37c T
>     289. setwgroups           801dd040 T
>     290. getwgroups           801dc380 T
>     291. mkfifo_extended      800b16f4 T
>     292. mkdir_extended       800b3b30 T
>     294. shared_region_check_np 8021c3a4 T
>     296. vm_pressure_monitor  8021cb08 T
>     297. psynch_rw_longrdlock 802159ac T
>     298. psynch_rw_yieldwrlock 80215c60 T
>     299. psynch_rw_downgrade  80215c68 T
>     300. psynch_rw_upgrade    80215c64 T
>     301. psynch_mutexwait     80212bd8 T
>     302. psynch_mutexdrop     80213b9c T
>     303. psynch_cvbroad       80213bf0 T
>     304. psynch_cvsignal      802141c0 T
>     305. psynch_cvwait        80214648 T
>     306. psynch_rw_rdlock     80214d7c T
>     307. psynch_rw_wrlock     802159b0 T
>     308. psynch_rw_unlock     80215c6c T
>     309. psynch_rw_unlock2    80215f64 T
>     310. getsid               801dc254 T
>     311. settid_with_pid      801dcdcc T
>     312. psynch_cvclrprepost  80214c7c T
>     313. aio_fsync            801c5ed0 T
>     314. aio_return           801c60a8 T
>     315. aio_suspend          801c6330 T
>     316. aio_cancel           801c5a48 T
>     317. aio_error            801c5e24 T
>     318. aio_read             801c6088 T
>     319. aio_write            801c6544 T
>     320. lio_listio           801c6564 T
>     322. iopolicysys          801de420 T
>     323. process_policy       8021a72c T
>     324. mlockall             801d88b4 T
>     325. munlockall           801d88b8 T
>     327. issetugid            801dc4b0 T
>     328. __pthread_kill       801dfa44 T
>     329. __pthread_sigmask    801dfaa4 T
>     330. __sigwait            801dfb54 T
>     331. __disable_threadsignal 801df720 T
>     332. __pthread_markcancel 801df73c T
>     333. __pthread_canceled   801df784 T
>     334. __semwait_signal     801df924 T
>     336. proc_info            80218618 T
>     338. stat64               800b25d4 T
>     339. fstat64              801cd028 T
>     340. lstat64              800b2720 T
>     341. stat64_extended      800b2624 T
>     342. lstat64_extended     800b2770 T
>     343. fstat64_extended     801cd00c T
>     344. getdirentries64      800b4340 T
>     345. statfs64             800b06e0 T
>     346. fstatfs64            800b0828 T
>     347. getfsstat64          800b0a38 T
>     348. __pthread_chdir      800b0d28 T
>     349. __pthread_fchdir     800b0c58 T
>     350. audit                801c1a74 T
>     351. auditon              801c1a78 T
>     353. getauid              801c1a7c T
>     354. setauid              801c1a80 T
>     357. getaudit_addr        801c1a84 T
>     358. setaudit_addr        801c1a88 T
>     359. auditctl             801c1a8c T
>     360. bsdthread_create     80216ab8 T
>     361. bsdthread_terminate  80216d30 T
>     362. kqueue               801cf594 T
>     363. kevent               801cf614 T
>     364. lchown               800b2d94 T
>     365. stack_snapshot       801c520c T
>     366. bsdthread_register   80216d94 T
>     367. workq_open           802179e8 T
>     368. workq_kernreturn     80217e50 T
>     369. kevent64             801cf8ac T
>     370. __old_semwait_signal 801df7f8 T
>     371. __old_semwait_signal_nocancel 801df82c T
>     372. thread_selfid        80218354 T
>     373. ledger               801ed70c T
>     380. __mac_execve         801d4468 T
>     381. __mac_syscall        8027d0a8 T
>     382. __mac_get_file       8027cd50 T
>     383. __mac_set_file       8027cf98 T
>     384. __mac_get_link       8027ce74 T
>     385. __mac_set_link       8027d098 T
>     386. __mac_get_proc       8027c844 T
>     387. __mac_set_proc       8027c904 T
>     388. __mac_get_fd         8027cbfc T
>     389. __mac_set_fd         8027ce84 T
>     390. __mac_get_pid        8027c778 T
>     391. __mac_get_lcid       8027c9b8 T
>     392. __mac_get_lctx       8027ca7c T
>     393. __mac_set_lctx       8027cb38 T
>     394. setlcid              801dd228 T
>     395. getlcid              801dd310 T
>     396. read_nocancel        801eb5a4 T
>     397. write_nocancel       801eb978 T
>     398. open_nocancel        800b1434 T
>     399. close_nocancel       801ccad0 T
>     400. wait4_nocancel       801d56dc T
>     401. recvmsg_nocancel     8020a91c T
>     402. sendmsg_nocancel     8020a464 T
>     403. recvfrom_nocancel    8020a548 T
>     404. accept_nocancel      80209b1c T
>     405. msync_nocancel       801d84e8 T
>     406. fcntl_nocancel       801cb440 T
>     407. select_nocancel      801ebfe4 T
>     408. fsync_nocancel       800b32a8 T
>     409. connect_nocancel     80209e34 T
>     410. sigsuspend_nocancel  801df6b4 T
>     411. readv_nocancel       801eb830 T
>     412. writev_nocancel      801ebbd0 T
>     413. sendto_nocancel      8020a188 T
>     414. pread_nocancel       801eb794 T
>     415. pwrite_nocancel      801ebaf0 T
>     416. waitid_nocancel      801d5ad0 T
>     417. poll_nocancel        801ec74c T
>     420. sem_wait_nocancel    8020e788 T
>     421. aio_suspend_nocancel 801c6350 T
>     422. __sigwait_nocancel   801dfb8c T
>     423. __semwait_signal_nocancel 801df958 T
>     424. __mac_mount          800af08c T
>     425. __mac_get_mount      8027d2a0 T
>     426. __mac_getfsstat      800b08b0 T
>     427. fsgetpath            800b5ce4 T
>     428. audit_session_self   801c1a68 T
>     429. audit_session_join   801c1a6c T
>     430. fileport_makeport    801ce2f0 T
>     431. fileport_makefd      801ce494 T
>     432. audit_session_port   801c1a70 T
>     433. pid_suspend          8021c180 T
>     434. pid_resume           8021c1f0 T
>     435. pid_hibernate        8021c268 T
>     436. pid_shutdown_sockets 8021c2c0 T
>     438. shared_region_map_and_slide_np 8021c954 T
>     439. kas_info             8021cb50 T   ; Provides ASLR information to user space 
>                                            ; (intentionally crippled in iOS, works in ML)
>     440. memorystatus_control 801e62a0 T   ;; Controls JetSam - supersedes old sysctl interface
>     441. guarded_open_np      801cead0 T  
>     442. guarded_close_np     801cebdc T
>     
>     ```
>
>     
>
>     





#### ARM  (通过汇编调用svc实现用户态到内核态的转换)



```
void AntiDebug_006(){
#ifdef __arm__
    asm volatile(
                 "mov r0,#31\n"
                 "mov r1,#0\n"
                 "mov r2,#0\n"
                 "mov r12,#26\n"
                 "svc #80\n"
                 );
#endif
#ifdef __arm64__
    asm volatile(
                 "mov x0,#26\n"
                 "mov x1,#31\n"
                 "mov x2,#0\n"
                 "mov x3,#0\n"
                 "mov x16,#0\n"
                 "svc #128\n"
                 );
#endif
}

```



#### 其他方法获取进程的调试状态



> * 获取异常端口
>
>   ```
>   void AntiDebug_007(){
>       struct macosx_exception_info{
>           exception_mask_t masks[EXC_TYPES_COUNT];
>           mach_port_t ports[EXC_TYPES_COUNT];
>           exception_behavior_t behaviors[EXC_TYPES_COUNT];
>           thread_state_flavor_t flavors[EXC_TYPES_COUNT];
>           mach_msg_type_number_t cout;
>       };
>       struct macosx_exception_info *info = malloc(sizeof(struct macosx_exception_info));
>       task_get_exception_ports(mach_task_self(),
>                                EXC_MASK_ALL,
>                                info->masks,
>                                &info->cout,
>                                info->ports,
>                                info->behaviors,
>                                info->flavors);
>       for(uint32_t i = 0; i < info->cout; i ++){
>           if(info->ports[i] != 0 || info->flavors[i] == THREAD_STATE_NONE){
>               NSLog(@"debugger detected via exception ports (null port)!\n");
>           }
>       }
>   }
>   
>   ```
>
>   
>
> * isatty
>
>   ```
>   void AntiDebug_008(){
>       if (isatty(1)) {
>           NSLog(@"Being Debugged isatty");
>       }
>   }
>   
>   ```
>
>   
>
> * ioctl
>
>   ```
>   void AntiDebug_009(){
>       if (!ioctl(1, TIOCGWINSZ)) {
>           NSLog(@"Being Debugged ioctl");
>       }
>   }
>   
>   ```
>
>   

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dynamic_protection 反调试、反反调试、反注入、hook检测、完整性校验 -t security
> #原来""的参数，需要自己加上""
```

