---
title: "vol内存取证"
date: 2025-09-28
draft: false
showComments: true
tags: ["取证", "Volatility", "内存取证"]
---

# vol内存取证

## 参数

```bash
-f FILENAME
```

## 查看镜像

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' imageinfo 
```

![image-20241222192814235](http://139.196.222.108:40061/i/2024/12/22/vvyp5e-0.png)

> Suggested Profile：表示这个镜像的版本号，只有这个版本号才能进行后面的步骤，如果镜像不对换后面的
>
> Image data and time：这个内存镜像的制作时间，默认是UTC+0的时间
>
> Image Local data and time：是自己的UTC+8的时间

## 进程分析

### pslist

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 pslist
```

- 该命令为列出内存中的进程，包括进程PID、PPID（父进程）以及启动时间，并可以清楚的看到父子进程的关系。

![image-20241222193616525](http://139.196.222.108:40061/i/2024/12/22/w0qpmr-0.png)

### psscan

查看隐藏的进程

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 psscan
```

![image-20241222193938785](http://139.196.222.108:40061/i/2024/12/22/w2ns30-0.png)

### pstree

可以查看伪装的进程以树的形式展示

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 pstree
```

![image-20241222194055788](http://139.196.222.108:40061/i/2024/12/22/w3cx98-0.png)

有些恶意进程会伪装成同名的进程，通过进程树，看他是否归属于正常的进程

例：很多恶意进程会伪装成svchost.exe，但svchost.exe的真正父进程是services.exe的。

### 

## 动态链接库

### DLL文件是什么

> DLL文件为在动态链接库文件，又称“应用程序扩展”，是软件文件类型。在Windows中，许多应用程序并不是一个完整的可执行文件，它们被分割成一些相对独立的动态链接库，既DLL文件，放置于系统中。当我们执行某一个程序时，相应的DLL文件就会被调用。一个应用程序可使用多个DLL文件，一个DLL文件也可能被不同的应用程序使用，这样的DLL被称为共享DLL文件。

### dlllist

该命令能看到每个进程（程序)运行所需要的所有动态链接库

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 dlllist
```

![image-20241222200339152](http://139.196.222.108:40061/i/2024/12/22/x4rjy2-0.png)

## 网络连接

### netscan

该命令可查看所有的连接协议、本地地址、链接地址、状态、监听端口以及PID（进程号）

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 netscan   
```

![image-20241222200753091](http://139.196.222.108:40061/i/2024/12/22/x787nl-0.png)

## 内核驱动

### modules

查看内核驱动

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 modules
```

![image-20241222201119051](http://139.196.222.108:40061/i/2024/12/22/x9equv-0.png)

### modscan

查看隐藏的内核驱动

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 modscan
```

![image-20241222201342039](http://139.196.222.108:40061/i/2024/12/22/xaqfvy-0.png)

### driverscan

查看隐藏的内核驱动

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 driverscan
```

![image-20241222201436802](http://139.196.222.108:40061/i/2024/12/22/xbavcx-0.png)

## 导出进程程序

### procdump

> -D 导出的路径
>
> -p 进程号PID

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 procdump -D  /root/Desktop -p 3820
```

![image-20241222202430754](http://139.196.222.108:40061/i/2024/12/22/xh7xm9-0.png)

## 内存文件

### filescan

扫描内存中的文件

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 filescan
```

![image-20241222202602901](http://139.196.222.108:40061/i/2024/12/22/xi8qx7-0.png)

### dumpfiles导出内存中缓存的文件

该命令能导出内存中缓存的文件

> -Q 内存中的位置
>
> -D 导出的路径

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 dumpfiles -Q 0x000000007fe67730 -D /root/Desktop
```

![image-20241222202854208](http://139.196.222.108:40061/i/2024/12/22/xjqhbj-0.png)

## 注册表

### hivelist

列举在内存中的注册表

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 hivelist 
```

![image-20241222203722916](http://139.196.222.108:40061/i/2024/12/22/xowsv4-0.png)

### printkey

查看和解析注册表中的键值

```bash
python2 vol.py  -f '/root/Desktop/OtterCTF.vmem' --profile=Win7SP1x64 printkey -o 0xfffff8a000024010 -K "ControlSet001\Control\ComputerName\ComputerName" 
```

![image-20241223085413708](http://139.196.222.108:40061/i/2024/12/23/e4j4kp-0.png)

## **二、解答过程**

### **任务一、**           提交该符合该内存文件的配置文件名称, 提交格式：flag{*******}；

![image-20241223090720437](http://139.196.222.108:40061/i/2024/12/23/f04ib4-0.png)

### 任务二、提交操作系统当前时间, 提交格式：flag{*******}；

![image-20241223090729962](http://139.196.222.108:40061/i/2024/12/23/f06q9f-0.png)

### 任务三、最可疑的进程是什么, 提交进程名。提交格式：flag{*******}；

![image-20241223090739251](http://139.196.222.108:40061/i/2024/12/23/f08kj3-0.png)

### 任务四、哪个进程被注入的可能性最高? 提交进程名。提交格式：flag{***}；

![image-20241223090747477](http://139.196.222.108:40061/i/2024/12/23/f0ammb-0.png)

### 任务五、进程被注入的dll文件名称是什么, 提交格式：flag{*******}；

![image-20241223090756518](http://139.196.222.108:40061/i/2024/12/23/f0ccro-0.png)

### 任务六           **、**           注入的dll基地址是什么, 提交格式：flag{*******}；

![image-20241223090805802](http://139.196.222.108:40061/i/2024/12/23/f0mpvy-0.png)

### 任务七、注入的过程中引用了可疑文件,提供该文件的完整路径？提交格式：flag{*******}；

![image-20241223090816917](http://139.196.222.108:40061/i/2024/12/23/f0pfq2-0.png)

---
title: "vol内存取证"
date: 2025-09-28
slug: "vol-memory-forensics"
draft: false
showComments: true
---


