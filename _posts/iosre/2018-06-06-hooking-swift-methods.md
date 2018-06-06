---
layout: post
title: hooking-swift-methods
date: 2018-06-06
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 利用theos、MSHookFunction、MSFindSymbol进行实现
---



# Using `nm`, we can dump the Swift symbols

>* `nm HookExampleApp`

>*  the symbols look unmangled: 

```sh 
 nm /Users/devzkn/decrypted/NoName/Payload/Name.app/Name | xcrun swift-demangle
```


# swift 进行swiftOCclass-dump 之后的分析

>* _Ttappname19NNTabViewController 对应 hopper 中的`appname.NNTabViewController`


# I 、 hooking-swift-methods
```
%hook AnyRandomNameHere
- (void)isjailbroken {
return nil;
}
%end
%ctor {
%init(AnyRandomNameHere = objc_getClass("mobile.AppDelegate"));
}
```

# II、 Calling a Swift method

#### 2.1 Adding a test Swift method
```
func randomFunction() {
    print("randomFunction called")
}
```

```
nm <AppName>
..
T __T014HookExampleApp14ViewControllerC14randomFunctionyyF
..
nm <AppName> | xcrun swift-demangle
..
T _HookExampleApp.ViewController.randomFunction() -> ()
..
```

#### 2.2 Using [MSFindSymbol](http://www.cydiasubstrate.com/api/c/MSFindSymbol/) we can find the function pointer to the Swift method, and call it.
>* MSFindSymbol
```
void *MSFindSymbol(MSImageRef image, const char *name);
```
>* Calling a Swift method

```
- (void)viewDidLoad {
    %orig;
    NSLog(@"VIEW DID LOAD");
    void *symbol = MSFindSymbol(NULL, "__T014HookExampleApp14ViewControllerC14randomFunctionyyF");
   ((void (*)(void)) symbol)();
}
```


# III、 Hooking a Swift method

After finding the function pointer to a Swift method;use [MSHookFunction](http://www.cydiasubstrate.com/api/c/MSHookFunction/) to hook it

>* MSHookFunction
```
void MSHookFunction(void *symbol, void *hook, void **old);
```
>* Hooking a Swift method

```
static void (*orig_ViewController_randomFunction)(void) = NULL;

void hook_ViewController_randomFunction() {
   orig_ViewController_randomFunction();
   NSLog(@"Hooked random function");
}

%ctor {
    %init(ViewController = objc_getClass("HookExampleApp.ViewController"));
    MSHookFunction(MSFindSymbol(NULL, "__T014HookExampleApp14ViewControllerC14randomFunctionyyF"),
                   (void*)hook_ViewController_randomFunction,
                   (void**)&orig_ViewController_randomFunction);
}
```

# other 

#### knhook

```
__attribute__((constructor)) static void before1(){
  [KNHook hookClass:@"appName.AppDelegate"];
}
```

# See Also 
>* [www.mbo42.com](https://www.mbo42.com/2018/04/01/hooking-swift-methods/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost hooking-swift-methods 利用theos、MSHookFunction、MSFindSymbol进行实现 -t iosre
> #原来""的参数，需要自己加上""
```

