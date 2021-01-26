# protobuf介绍

​	Protocol buffers是用于序列化结构化数据的灵活、高效、自动化的机制——比如XML，但是更小、更快、更简单。您只需定义一下希望如何对数据进行结构化，然后就可以使用特殊生成的源代码来轻松地编写和读取到各种数据流中的结构化数据，并使用各种语言。你甚至可以更新你的数据结构，而不必破坏那些被编译为“旧”格式的程序。  
​	Google protobuf是一个灵活的、高效的用于序列化数据的协议。相比较XML和JSON格式，protobuf更小、更快、更便捷。protobuf也叫protocol buffer是google 的一种数据交换的格式，它独立于语言，独立于平台。Google protobuf是跨语言的，并且自带了一个编译器(protoc)，只需要用它进行编译，可以编译成Java、python、C++、C#、Go等代码，然后就可以直接使用，不需要再写其他代码，自带有解析的代码。
   Google 提供了多种语言的实现：Java、c#、c++、Go 和 Python，每一种实现都包含了相应语言的编译器以及库文件。由于它是一种二进制的格式，比使用 xml 、json进行数据交换快许多。可以把它用于分布式应用之间的数据通信或者异构环境下的数据交换。作为一种效率和兼容性都很优秀的二进制数据传输格式，可以用于诸如网络传输、配置文件、数据存储等诸多领域。

# protobuf安装

> https://github.com/protocolbuffers/protobuf/releases

### Linux安装protobuf

- 下载源码包并解压

```shell
wget https://github.com/protocolbuffers/protobuf/releases/download/v3.14.0/protobuf-all-3.14.0.tar.gz
tar -zxvf protobuf-all-3.14.0.tar.gz
```

- 编译安装

```shell
cd protobuf-3.14.0/
./configure && make && make check && make install 
```

### Windows安装protobuf

- github下载win64的安装包并解压
- 配置环境变量

![image-20210118161532158](http://rocks526.top/lzx/image-20210118161532158.png)

![image-20210118161455537](http://rocks526.top/lzx/image-20210118161455537.png)

### python安装protobuf

- pip安装

```shell
pip install protobuf
```

# 使用示例

- 项目下新建protobuf目录，里面创建user.proto文件，文件里定义实体信息

![image-20210119133908702](http://rocks526.top/lzx/image-20210119133908702.png)

```protobuf
syntax = "proto3";
option java_package = "com.rocks";
option java_outer_classname = "UserModel";

message User {
     int32 id = 1;
     string name = 2;
     string email = 3;
}
```

- 编译协议文件，生成py工具类

```shell
E:\git-repository\python-module\protobuf>protoc --python_out=./ user.proto
```

- 编写测试代码

```python
from protobuf.proto import user_pb2


def write_user_to_disk():
    user = user_pb2.User()
    user.id = 2
    user.name = 'Rocks526'
    user.email = "Rocks@126.com"
    with open('./protobuf/data/user.txt', 'wb') as f:
        f.write(user.SerializeToString())


def read_user_from_disk():
    # 注意要以二进制读取
    with open('./protobuf/data/user.txt', 'rb') as f:
        user_str = f.read()
        user = user_pb2.User()
        print(user)
        user.ParseFromString(user_str)
        print(user)


if __name__ == '__main__':
    write_user_to_disk()
    read_user_from_disk()
```

