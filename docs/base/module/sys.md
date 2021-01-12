# sys模块介绍

sys模块提供了一系列有关Python运行环境的变量和函数。

# sys模块主要api

- sys.argv: 实现从程序外部向程序传递参数，返回元组，第一个是程序名
- sys.exit([arg]): 程序退出，arg=0为正常退出
- sys.version：返回python版本号
- sys.modules:`sys.modules`是一个全局字典，该字典是python启动后就加载在内存中。每当程序员导入新的模块，`sys.modules`将自动记录该模块。当第二次再导入该模块时，python会直接到字典中查找，从而加快了程序运行的速度。它拥有字典所拥有的一切方法。
- sys.getdefaultencoding(): 获取系统当前编码，一般默认为ascii。
- sys.setdefaultencoding(): 设置系统默认编码，执行dir（sys）时不会看到这个方法，在解释器中执行不通过，可以先执行reload(sys)，在执行 setdefaultencoding('utf8')，此时将系统默认编码设置为utf8。
- sys.getfilesystemencoding(): 获取文件系统使用编码方式，Windows下返回'mbcs'，mac下返回'utf-8'.
- sys.path: 获取指定模块搜索路径的字符串集合，可以将写好的模块放在得到的某个路径下，就可以在程序中import时正确找到。
- sys.platform: 获取当前系统平台。
- sys.stdout.write('please:'):标准输出
- sys.getrecursionlimit() :获取最大递归层数
- sys.setrecursionlimit(1200):设置最大递归层数
- sys.stdin,sys.stdout,sys.stderr: stdin , stdout , 以及stderr 变量包含与标准I/O 流对应的流对象. 如果需要更好地控制输出,而print 不能满足你的要求, 它们就是你所需要的. 你也可以替换它们, 这时候你就可以重定向输出和输入到其它设备( device ), 或者以非标准的方式处理它们

# sys代码示例

```python
# sys模块示例

import sys

# 实现从程序外部向程序传递参数，返回元组，第一个是程序名
print(sys.argv)

# 返回python版本号
print(sys.version)

# `sys.modules`是一个全局字典，该字典是python启动后就加载在内存中
print(sys.modules)

# 获取系统当前编码，一般默认为ascii。
print(sys.getdefaultencoding())

# 设置系统默认编码 windows下报错
if sys.getdefaultencoding() != 'utf-8':
    reload(sys)
    sys.setdefaultencoding('utf-8')
print(sys.getdefaultencoding())

# 获取文件系统使用编码方式
print(sys.getfilesystemencoding())

# 获取指定模块搜索路径的字符串集合
print(sys.path)

# 获取当前系统平台。
print(sys.platform)

# 标准输出  类似于print
sys.stdout.write('demo')

# 程序退出，arg=0为正常退出
sys.exit(-2)
```

