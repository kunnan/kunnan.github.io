---
layout: post
title: The_use_of_Frida
date: 2018-08-16
tag: frida
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 通过Frida提高的python接口、注入js调用native的函数、利用Frida提供的各种动态函数拦截功能，实现对class的替换和监控（ 这个高级功能是由js的API提供）
---



# Frida 的初级使用：通过Frida提高的python接口获取一些信息 

#### [`main.py`,编写代码增加入口函数](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-7/7.3%20Frida%E5%AE%9E%E6%88%98%E5%BA%94%E7%94%A8/Frida/main.py)



> * `if __name__ == '__main__':`
>
>   ```
>   	try:
>   		main()
>   	except KeyboardInterrupt:
>   		if session:
>   			session.detach()
>   		sys.exit()
>   	else:
>   		pass
>   	finally:
>   		pass
>   
>   ```
>
>   



#### 获取USB设备信息

> * 获取USB设备
>
>   ```
>   	device = get_usb_iphone()
>   
>   ```
>
>   * get_usb_iphone
>
>     ```
>     #获取第一个USB连接的设备
>     def get_usb_iphone():
>     	dManager = frida.get_device_manager();   #获取设备管理器
>     	changed = threading.Event()
>     	def on_changed():
>     		changed.set()
>     	dManager.on('changed',on_changed)        #监听添加设备的事件
>     
>     	device = None
>     	while device is None:
>     		devices = [dev for dev in dManager.enumerate_devices() if dev.type =='tether']  #类型为tether为USB连接的设备
>     		if len(devices) == 0:
>     			print 'Waiting for usb device...'
>     			changed.wait()
>     		else:
>     			device = devices[0]				 #获取第一个设备
>     
>     	dManager.off('changed',on_changed)    
>     
>     	return device
>     
>     ```
>
>     

#### 枚举运行进程信息

> * listRunningProcess
>
>   ```
>   def listRunningProcess():
>   	device = get_usb_iphone();// 先获取设备信息
>   	processes = device.enumerate_processes();//获取当前的运行进程信息
>   	processes.sort(key = lambda item : item.pid)
>   	outWrite('%-10s\t%s' % ('pid', 'name'))
>   	for process in processes:
>   		outWrite('%-10s\t%s' % (str(process.pid),process.name))
>   
>   ```
>
>   

#### 枚举安装应用程序信息



正常获取安装app的信息，可以通过LSApplicationWorkspace 的私有API来实现。

> * frida 获取app信息的步骤
>
>   * 注入进程（sb），获取session
>
>     ```
>     	# session = device.attach(u'SpringBoard')
>     
>     ```
>
>     
>
>   * 在进程里通过注入js代码去执行native函数，调用私有API获取信息，由js返回，在和python通信，从而将信息输出到控制台
>
>     *  注入js文件，设置回调
>
>       * 加载js文件
>
>         ```
>         #加载JS文件脚本
>         def loadJsFile(session, filename):
>         	source = ''
>         	with codecs.open(filename, 'r', 'utf-8') as f:
>         		source = source + f.read()
>         	script = session.create_script(source)
>         	script.on('message', on_message)#设置js返回数据的python回调
>         	script.load()# 加载js文件
>         	return script
>         
>         ```
>
>         

```
	# session = device.attach(u'SpringBoard')
	# script = loadJsFile(session, APP_JS)
	# script.post({'cmd' : 'installed'})
	# finished.wait()


```

> * js
>
>   ```
>   APP_JS = './js/app.js'
>   UI_JS = './js/ui.js'
>   HOOK_JS = './js/hook.js'
>   
>   ```
>
>   * [app.js](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-7/7.3%20Frida%E5%AE%9E%E6%88%98%E5%BA%94%E7%94%A8/Frida/js/app.js)
>
>     * getDataDocument
>
>       ```
>       function getDataDocument(appid){
>       	const dataUrl = LSApplicationProxy.applicationProxyForIdentifier_(appid).dataContainerURL();
>       	if(dataUrl){
>       		return dataUrl.toString() + '/Documents';
>       	}else{
>       		return "null";
>       	}
>       }
>       
>       ```
>
>       
>
>     ```
>     function installed(){
>     	const workspace = LSApplicationWorkspace.defaultWorkspace();
>     	const apps = workspace.allApplications();
>     	var result;
>     	for(var index = 0; index < apps.count(); index++){
>     		var proxy = apps.objectAtIndex_(index);
>     		result = result + proxy.localizedName().toString() + '\t' + proxy.bundleIdentifier().toString()+'\t'+getDataDocument(proxy.bundleIdentifier().toString())+'\n';
>     	}
>     	send({app : result}); # 将ret 返回python
>     };
>     ```
>
>     





# See Also 

>* [py](https://kunnan.github.io/tags/#py)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost The_use_of_Frida 通过Frida提高的python接口、注入js调用native的函数、利用Frida提供的各种动态函数拦截功能，实现对class的替换和监控（ 这个高级功能是由js的API提供） -t frida
> #原来""的参数，需要自己加上""
```

