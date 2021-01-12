# OS模块介绍

OS模块提供了多数操作系统的功能接口函数。当os模块被导入后，它会自适应于不同的操作系统平台，根据不同的平台进行相应的操作，在python编程时，经常和文件、目录打交道，这时就离不了os模块。

# 常用OS模块命令

- os.name：判断目前正在使用的平台，并给出操作系统的名字，类似的有sys.platform，建议采用后者
- os.getcwd()：全称应该是'get current work directory'，获取当前工作的目录
- os.getpid()：获取进程id
- os.listdir(path)：列出path目录下所有的文件和目录名
- os.remove(path)：删除path指定的文件
- os.rmdir(path)：删除path指定的目录
- os.mkdir(path)：创建path指定的目录
- os.makedirs(path)：递归建立path指定的目录
- os.path.isfile(path)：判断指定对象是否为文件
- os.path.isdir(path)：判断指定对象是否为目录
- os.path.exists(path)：检验指定的对象是否存在
- os.path.split(path)：返回路径的目录和文件名，即将目录和文件名分开，而不是一个整体
- os.system(cmd)：执行shell命令。返回值是脚本的退出状态码，0代表成功，1代表不成功
- os.chdir(path)：'change dir'改变目录到指定目录
- os.path.getsize()：获得文件的大小，如果为目录，返回0
- os.path.abspath()：获得绝对路径
- os.path.join(path, name)：连接目录和文件名
- os.path.basename(path)：返回文件名
- os.path.dirname(path)：返回文件路径
- os.path.getmtime(path)：文件或文件夹的最后修改时间，从新纪元到访问时的秒数
- os.path.getatime(path)：文件或文件夹的最后访问时间，从新纪元到访问时的秒数
-  os.path.getctime(path)：文件或文件夹的创建时间，从新纪元到访问时的秒数
- os.chmod(file)：修改文件权限
- os.rename(src,dst)：重命名
- os.exit()：终止当前进程  

# 代码示例

```python
# os模块示例

import os
import sys

# 判断目前正在使用的平台，并给出操作系统的名字
print(os.name)
print(sys.platform)

# 获取当前工作的目录
print(os.getcwd())

# 获取进程id
print(os.getpid())

# 列出path目录下所有的文件和目录名
print(os.listdir("."))

# 创建目录 默认目录存在会报错  exist_ok设置为True 不会报错
os.makedirs("./demo1/demo2/demo3", exist_ok=True)

# 创建文件
with open("./demo1/demo2/demo3/demo.txt", 'w+') as f:
    f.write("Hello World")

# 判断指定对象是否为文件
print(os.path.isfile("./demo1/demo2/demo3/demo.txt"))
print(os.path.isdir("./demo1"))

# 返回路径的目录和文件名，即将目录和文件名分开，而不是一个整体
print(os.path.split("./demo1/demo2/demo3/demo.txt"))

# 获得文件的大小，如果为目录，返回0
print(os.path.getsize("./demo1"))
print(os.path.getsize("./demo1/demo2/demo3/demo.txt"))

# 删除path指定的文件
print(os.remove("./demo1/demo2/demo3/demo.txt"))

# 删除目录
print(os.removedirs("./demo1/demo2/demo3"))

# 检验指定的对象是否存在
print(os.path.exists("./demo1"))

# 执行shell命令。返回值是脚本的退出状态码，0代表成功，1代表不成功
print(os.system("cd d:"))

# 改变目录到指定目录
print(os.getcwd())
print(os.chdir("../"))
print(os.getcwd())

# 连接目录和文件名
print(os.path.join("d:", "demo"))

# 终止当前进程
os._exit(-1)
```

> 参考：https://blog.csdn.net/weixin_38507813/article/details/87797839