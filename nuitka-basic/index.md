# Windows上使用Nuitka将Python文件打包成exe文件

Nuitka是用于将Python文件打包成可执行文件的工具，类似于PyInstaller，但是相较而言Nuitka是直接将python编译成C++代码, 性能和安全性更好

## 安装Python
最好手动安装Python，接在Python官网下载Windows的安装包。选择正确的安装包（要选择64位的安装包)  
安装过程中需要注意的是要勾选选项"将Python安装路径添加到Path"
## 安装Nuitka
``` shell
python -m pip install nuitka
```  
安装好之后我们可以测试一下Nuitka是否可以编译python文件
比如笔者在C:\code\test_code目录下可以创建一个main.py文件, 文件如下：  
```python
#!/usr/bin/env python
# coding: utf-8

def job():
    print("hello")
    

if __name__ == "__main__":
    while True:
        job()
        sleep(60)
```
我们尝试编译这个文件  
``` shell
Error, cannot locate suitable C compiler. You have the following options:

a) If a suitable Visual Studio version is installed, it will not be located automatically, unless you install pywin32 for the Python installation below "C:\Program Files (x86)\Microsoft Visual Studio\Shared\Python36_86".

b) To make it find Visual Studio without registry execute from Start Menu the 'Visual Studio Command Prompt' or "vcvarsall.bat". That will add Visual Studio to the "PATH". And it then will be detected.

c) Install MinGW64 to "C:\MinGW64" or "\MinGW", where then it is automatically detected or add it to PATH before executing Nuitka. But be sure to pick the proper variant (32/64 bits, your Python arch is 'x86'), or else cryptic errors will be shown.

Normal MinGW will not work! MinGW64 does not mean 64 bits, just better Windows compatibility. Cygwin based gcc will not work. MSYS2 based gcc will not work. clang-cl will only work if MSVC already worked.
```  
这是由于我们并没有安装C编译器的缘故，因此我们还需要安装C编译器
## 安装MinGW64
要想使用Nuitka，我们就需要C编译器，而这通常使用MinGW64(Minimal GNU for Windows)会比较好, MinGW64是一套适用于Windows的开发环境，这个开发环境有一组开源的工具集，其中就有用于C/C++语言编译的gcc，而Nuitka就需要一个C/C++编译器，所以我们需要先装这个。

首先要下载安装包，下载链接[winlibs](https://winlibs.com/)  
下载之后，解压到你的安装目录（解压后就算安装了，不需要执行安装exe），比如C:\MinGW64  
然后将安装目录下的bin目录的路径加入到环境变量PATH中, 比如本例就是C:\MinGW64\mingw64\bin，然后打开CMD验证是否安装成功。
```shell
>gcc --version
gcc (x86_64-posix-sjlj-rev0, Built by MinGW-W64 project) 8.1.0
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
当看到如上所示的文字说明我们已经安装成功。
## 使用Nuitka进行编译
```shell
> nuitka --mingw64 main.py
```
可以看到这次成功了，在test_code目录下新生成了一个exe可执行文件。  
{{< ypblogads >}}
