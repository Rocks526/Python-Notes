- [Flask快速入门](#js)
- [Flask对象配置](#pz)
- [视图](#st)
- [扩展插件](#kz)
  - [Flask-Script](#sc)
  - [Flask-SQLAlchemy](#orm)
  - [Flask-Migrate](#qy)
  - [Flask-Mail](#mail)
- [测试与部署](#cs)
  - [蓝图](#lt)
  - [单元测试](#dy)
  - [部署](#bs)

# <a id="js">Flask快速入门</a>

- Flask介绍

Flask诞生于2010年，是Armin ronacher用Python语言基于Werkzeug路由工具箱编写的轻量级Web开发框架。它主要面向需求简单的小应用。

> 相比Django，Flask非常轻量级，只提供了请求路由和模板引擎两个核心功能，其余的ORM，认证，后台管理，邮件等都依靠扩展包实现。
>
> 请求路由基于路由工具包Werkzeug实现，模板引擎采用Jinja2

- Flask扩展包

  Flask-SQLalchemy：操作数据库；

  Flask-migrate：管理迁移数据库；

  Flask-Mail:邮件；

  Flask-WTF：表单；

  Flask-script：插入脚本；

  Flask-Login：认证用户状态；

  Flask-RESTful：开发REST API的工具；

  Flask-Bootstrap：集成前端Twitter Bootstrap框架；

  Flask-Moment：本地化日期和时间；

- Flask文档

  中文文档： http://docs.jinkan.org/docs/flask/

  英文文档： http://flask.pocoo.org/docs/0.11/

- 快速入门

1.安装Flask环境

> pip install flask

2.创建flask程序

```python
# 快速入门
from flask import Flask

# 接收参数__name__获取一个app对象
app = Flask(__name__)

# 第一个视图函数
@app.route('/')
def index():
    return "Hello Flask!"


# 启动程序
if __name__ == "__main__":
    app.run()
```

# <a id="pz">Flask对象配置</a>

- Flask对象的\__name__参数

传入参数为模块名，可以随意填写，当传入模块没有找到时以当前文件所在目录为根目录。

Flask程序根据此模块所在的目录为根目录，默认这个目录中的static为静态目录，templates为模板目录。

- Flask对象的static_url_path参数

此参数是设置访问静态文件的URL前缀，默认值为static

- Flask对象的static_folder参数

此参数是设置静态文件的存放目录，当为相对路径时，以模块名为根路径；当为绝对路径时，所指向的目录为静态文件的目录

- Flask对象的template_folder参数

和static_folder同理，设置模板文件的目录

```python
# 接收参数__name__获取一个app对象
app = Flask(__name__,
            static_url_path="/python",
            static_folder="static",
            template_folder="templates"
            )
```

-------

> Flask的配置参数支持配置文件、对象、环境变量、Json等多种方式

- 使用文件导入配置的方式

 ```python
app.config.from_pyfile("config/flask.config")

# 文件内容
DEBUG = True
 ```

- 使用对象导入配置的方式

```python
class AppConfig(object):
    DEBUG = True

app.config.from_object(AppConfig)
```

- 将app.config当做字典直接填入配置信息

```python
app.config["DEBUG"] = True
```

> app.config对象除了可以添加Flask程序内置的默认参数外，还可以添加自定义参数，之后程序了可以通过app.config["key"]或者app.config.get()取值
>
> 当某些视图获取不到app对象时，可以从flask导入current_app对象，此对象是app对象的一个代理，操作和app对象完全一致

```python
# 快速入门
from flask import Flask, current_app

app.config["DEBUG"] = True
app.config["Name"] = "Rocks526"

# 第一个视图函数
@app.route('/')
def index():
    name = app.config.get("Name", "None")
    name2 = current_app.config.get("Name", "None")
    return "Hello Flask! I am {}".format(name)
```

------

- app.run方法参数host，port

指定项目启动的ip和端口，默认127.0.0.1和5000

- app.run方法参数debug

指定项目启动是否开启调试模式，和app.config["DEBUG"]一样的效果

```python
    app.run(host="10.91.139.18", port=8888, debug=True)
```

# <a id="st">视图</a>

- Flask通过app.route()设置视图的路由信息

```python
@app.route('/')
def index():
    return "Hello Flask!
```

- 可以通过app.url_map查看所有路由信息

```python
    print(app.url_map)
    -------------------------
    Map([<Rule '/' (GET, HEAD, OPTIONS) -> index>,
 <Rule '/python/<filename>' (GET, HEAD, OPTIONS) -> static>])
```

- 通过methods参数限制访问的方法

```python
@app.route('/demo', methods=["GET", "POST"])
def demo():
    return "Hello Flask! "
------------------------------------
Map([<Rule '/demo' (OPTIONS, GET, POST, HEAD) -> demo>,
```

- 同一个路径可以绑定多个视图函数，url_map列表里会有多个映射，根据顺序从上到下匹配

```python
@app.route('/demo', methods=["GET", "POST"])
def demo():
    return "Hello Flask! "

@app.route('/demo', methods=["GET", "POST"])
def demo2():
    return "Hello Flask!2 "
-------------------------------------
Map([<Rule '/demo' (GET, OPTIONS, POST, HEAD) -> demo>,
 <Rule '/demo' (GET, OPTIONS, POST, HEAD) -> demo2>,
```

- Flask可以一个视图函数绑定多个路由

```python
@app.route('/demo2', methods=["GET", "POST"])
@app.route('/demo', methods=["GET", "POST"])
def demo():
    return "Hello Flask! "
```

- 请求重定向

```python
from flask import Flask, current_app, redirect, url_for
@app.route('/demo', methods=["GET", "POST"])
def demo():
    return redirect(url_for("index"))
    # return redirect("/")
```

> Flask支持url_for方法，通过函数名字反推url路径

- 捕获URL路径里的参数

```python
@app.route("/get/<int:id>")
def demo3(id):
    print(id)
    return "get id :{}".format(id)
```

> Flask支持int、float、path等多种转换器，默认采用str转换器
>
> 当默认选择器不满足需求的时候，可以自定义转换器

- 自定义转换器

```python
from werkzeug.routing import  BaseConverter

# 自定义转换器
class RegexConverter(BaseConverter):
    """正则表达式提取转换器"""
    def __init__(self, url_map, regex):
        # BaseConverter会默认执行self.regex属性里的正则逻辑
        super().__init__(url_map)
        # url_map为路由映射map  regex为自己传的参数
        # 将传的参数保存到对象的regex属性里
        self.regex = regex

    # 请求匹配过程 正则 --> __init__  -->  to_python并将解析后的值传给value --> 视图函数参数
    def to_python(self, value):
        print(value)
        return value

    # 和to_python类似 to_url在url_for时会调用
    def to_url(self, value):
        print(value)
        return value

# 将自定义的转换器注册到flask应用中
app.url_map.converters["re"] = RegexConverter

@app.route("/send/<re(r'1[35789]\d{9}'):mobile>")
def send(mobile):
    return "send sms to {}".format(mobile)

@app.route("/demo2")
def demo4():
    url = url_for("send", mobile="13891082457")
    return redirect(url)
```

- 请求参数提取，从flask导入request对象，从request对象里提取请求参数
  - data：提取请求体参数，转换为字符串格式
  - form：提取请求体参数，如果通过form表单或者www表单传递数据，会将数据从data提取出来转换kv存储到form里
  - args：请求URL中的参数
  - cookies：记录请求中的cookie信息
  - headers：记录请求中的报文头
  - method：记录请求使用的HTTP方法
  - url：记录请求的URL地址
  - files：记录请求上传的文件

```python
# 请求参数提取示例
@app.route("/demo3")
def demo5():
    # URL中的参数
    url_params = request.args["key1"]
    url_params2 = request.args.get("key2", default="None")
    # 表单参数
    form_params = request.form["key1"]
    form_params2 = request.form.get("key2")
    print(url_params + "," + url_params2 + "," + form_params + "," + form_params2)
    return "ok"

@app.route("/demo4")
def demo6():
    # 请求体非表单参数
    print(request.data)
    return "ok"

@app.route("/demo6")
def demo8():
    # files
    file = request.files.get("file1")
    if file == None:
        return "file is not upload!"
    with open("upload.docx", "wb") as f:
        data = file.read()
        f.write(data)
    return "file saved success!"

@app.route("/demo5")
def demo7():
    # cookies
    name = request.cookies.get("name", default="None")
    res = make_response(name)
    if name == "None":
        res.set_cookie("name", "Rocks", max_age=5)
    return res

@app.route("/demo9")
def demo9():
    res = make_response("delete cookie!")
    # 删除cookie
    res.delete_cookie("name")
    return res
```

- abort函数
  - abort函数用来终止视图函数的执行，返回特定的信息，类似于raise关键字。
  - abort函数返回的状态码必须是符合HTTP规范的状态码，否则无法正常解析，会出现500
  - abort函数也可以传入一个Response对象作为参数，指定响应体内容

```python
from flask import abort, Response
@app.route("/d1")
def demo23():
    print("abort test...")
    abort(500)
    return "success..."

@app.route("/d2")
def demo29():
    print("abort test...")
    abort(Response("fail..."))
    return "success..."
```

- 自定义异常状态码处理
  - flask会给异常处理函数传递一个err异常对象

```python
# 自定义异常响应状态码处理
@app.errorhandler(500)
def handler_666_error(err):
    return "Server is error : {}".format(err)
```

- 自定义响应状态码和响应头信息
  - 通过元组返回
  - 通过make_response构造响应信息

```python
# 自定义状态码和响应头
@app.route("/q1")
def demo0():
    # 1. 返回一个元组 第一个是响应体 第二是状态码 第三个是响应头
    # return "success!", 666, {"name": "Rocks", "age": "22"}
    # return "success!", "999 Rocks", [{"name", "Rocks"}, {"age", "22"}]

    # 2. 通过make_response 来构造响应信息
    res = make_response("success!")
    res.status = "666"
    res.headers["name"] = "Rocks"
    res.headers["age"] = 22
    return res
```

- 返回Json数据
  - jsonify函数

```python
from flask import abort, Response, jsonify, g
import json
# 返回Json数据
@app.route("/q2")
def demo00():
    # 1.使用jsonify返回json数据
    data = {
        "name": "Rocks",
        "age": "22"
    }
    return jsonify(data)
    return jsonify(name="Rocks", age=22)
    
    # 2.手动构建json数据返回 并设置响应头 "ContentType":"application/json"
    json_str = json.dumps(data)
    return json_str, 200, {"ContentType": "application/json"}
```

- cookie和session
  - 获取cookie通过request的cookie对象，设置和删除cookie通过flask导入make_response对象操作
  - 获取和添加、删除session通过flask导入session操作
  - flask使用之前，必须设置一个密匙，因为flask是将session数据通过密匙加密后存储在cookie里

```python
from flask import Flask, current_app, redirect, url_for, request, make_response, session
app.config["SECRET_KEY"] = "dasdascecjew998we"

@app.route("/demo10")
def demo10():
    user = session["user"]
    pwd = session.get("pwd", "None")
    return user + "," + pwd

@app.route("/demo11")
def demo11():
    session["user"] = "Rocks"
    session["pwd"] = "Rocks666"
    return "session set success!"

@app.route("/demo12")
def demo12():
    del session["user"]
    del session["pwd"]
    return "session delete success!"
```

- 请求上下文与应用上下文
  - flask中根据线程进行了隔离，称为请求上下文，例如requests对象和session对象，都是和请求绑定
  - 除了请求上下文之外，还有应用上下文，针对应用进行绑定，例如current_app和g对象
  - current_app表示当前运行程序文件的程序实例
  - g对象在处理请求时，用于临时存储的对象，每次请求都会重设这个变量，g对象可以在一次请求内调用其他方法时传递某些值过去，省去调用函数时传递大量参数

```python
from flask import abort, Response, jsonify, g
import json
@app.route("/q3")
def index2():
    print("g对象测试...")
    g.username = "Rocks"
    g.age = 22
    other_func()
    return "success..."

def other_func():
    print(g.username)
    print(g.age)
```

- 请求钩子：在flask对请求进行解析和路由映射的过程中插入的锚点

```python
# 钩子
# 第一次请求之前被执行
@app.before_first_request
def before_first_request():
    print("before_first_request...")

# 每次请求之前被执行
@app.before_request
def before_request():
    print("before_request...")

# 每次请求之后 没有异常则被执行 可以获得之前视图函数返回的响应对象
@app.after_request
def after_request(res):
    print("after_request...")
    return res

# 每次请求之后 无论是否存在异常都会执行  可以获得之前视图函数返回的响应对象/异常对象  DEBUG模式下不会起作用
@app.teardown_request
def teardown_request(e):
    print("teardown_request...")
    print(e)
    return e
```

# <a id="kz">扩展插件</a>

### <a id="sc">Flask-Script</a>

>  扩展脚本，让Flask具备管理能力

- demo.py --help：查看帮助
- demo.py runserver ：启动服务
- demo.py runserver --help：启动服务帮助，查看端口IP设置参数
- demo.py shell：启动shell交互脚本环境，可以直接使用flask环境，flask对象等

```python
from flask_script import Manager

# 接收参数__name__获取一个app对象
app = Flask(__name__,
            static_url_path="/python",
            static_folder="static",
            template_folder="templates"
            )
# 将flask对象交给Manager管理器
manager = Manager(app)
# 启动程序
if __name__ == "__main__":
    manager.run()
```

### <a id="orm">Flask-SQLAlchemy</a>

SQLAlchemy是一个ORM框架，Flask-SQLAlchemy是简化了SQLAlchemy的Flask扩展包，为Flask框架提供ORM功能

1. 安装flask-SQLAlchemy

> pip install flask-sqlalchemy
>
> pip install flask-mysqldb

2. 编写Flask项目基本信息，配置SQLAlchemy对象

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

# 创建flask对象
app = Flask(__name__)

# 设置秘钥
app.config["SECRET_KEY"] = "dasdasd3d34d34d"
# 设置数据库URI
app.config["SQLALCHEMY_DATABASE_URI"] = "mysql://rocks:Qy123456.@47.101.217.179/users"
# 设置每次请求结束后自动提交 (不建议使用 需要手动控制提交还是回滚)
app.config["SQLALCHEMY_COMMIT_ON_TEARDOWN"] = False
# 设置数据库自动追踪数据模型的变化
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = True
# 设置执行SQL时将SQL打印出来
app.config["SQLALCHEMY_ECHO"] = True
# 获取数据库对象
db = SQLAlchemy(app)
```

3. 建立模型类

```python
# 模型类
class User(db.Model):
    """用户表"""
    # 设置表名
    __tablename__ = "user"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    pwd = db.Column(db.String(50))
    # 外键
    role_id = db.Column(db.BigInteger, db.ForeignKey("role.id"))

class Role(db.Model):
    """角色表"""
    __tablename__ = "role"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    # 关联属性 backref属性作用是反推 加上此属性后 User对象里的role_id属性就不是Role的ID 而是Role对象
    users = db.relationship("User", backref="role")
```

4. 创建数据表

```python
if __name__ == "__main__":

    # 清楚数据库之前所有的残留表和数据
    db.drop_all()
    # 根据模型类创建表
    db.create_all()
```

5. 添加数据

```python
    # 创建对象
    role1 = Role(name="admin")
    role2 = Role(name="customer")
    # 添加任务到会话里
    db.session.add(role1)
    db.session.add(role2)
    # 提交任务
    db.session.commit()

    # 创建对象 由于Role的ID是自增的 需要先将Role提交到数据库 数据库将自增的主键返回 Role对象的ID才有值
    user1 = User(name="Rocks", pwd="123456", role_id=role1.id)
    user2 = User(name="Ming", pwd="123456", role_id=role2.id)
    # 批量添加任务对象到会话
    db.session.add_all([user1, user2])
    # 提交session
    db.session.commit()
```

6. 查询数据

- 常用的SQLAlchemy查询过滤器

|   过滤器    |                       说明                       |
| :---------: | :----------------------------------------------: |
|  filter()   |      把过滤器添加到原查询上，返回一个新查询      |
| filter_by() |    把等值过滤器添加到原查询上，返回一个新查询    |
|    limit    |         使用指定的值限定原查询返回的结果         |
|  offset()   |       偏移原查询返回的结果，返回一个新查询       |
| order_by()  | 根据指定条件对原查询结果进行排序，返回一个新查询 |
| group_by()  | 根据指定条件对原查询结果进行分组，返回一个新查询 |

- 常用的SQLAlchemy查询执行器

|      方法      |                     说明                     |
| :------------: | :------------------------------------------: |
|     all()      |         以列表形式返回查询的所有结果         |
|    first()     |  返回查询的第一个结果，如果未查到，返回None  |
| first_or_404() |  返回查询的第一个结果，如果未查到，返回404   |
|     get()      |   返回指定主键对应的行，如不存在，返回None   |
|  get_or_404()  |   返回指定主键对应的行，如不存在，返回404    |
|    count()     |              返回查询结果的数量              |
|   paginate()   | 返回一个Paginate对象，它包含指定范围内的结果 |

- 单表查询

```python
from orm_demo import db, Role, User
from sqlalchemy import or_, func

if __name__ == "__main__":

    # 查询某张表的全部数据 返回list
    roles = Role.query.all()    # flask封装方法
    # roles = db.session.query(Role).all()   # SQLAlchemy原生方法
    for role in roles:
        print("角色ID为{}，角色名称{}，角色对应的用户有{}".format(role.id, role.name, role.users))

    # 查询某张表的第一条记录
    role = Role.query.first()
    print("角色ID为{}，角色名称{}".format(role.id, role.name))

    # 根据主键ID查询
    role2 = Role.query.get(2)
    print("角色ID为{}，角色名称{}".format(role2.id, role2.name))

    # 根据过滤条件筛选 没有则返回None 多条件之间and关系
    users = User.query.filter_by(name="Rocks").first()
    users = User.query.filter_by(name="Rocks", pwd="123456").all()
    for user in users:
        print("用户ID：{}，用户名字：{}，用户密码：{}".format(user.id, user.name, user.pwd))

    # 通用过滤条件筛选 没有则返回None
    等效为users = User.query.filter_by(name="Rocks", pwd="123456").all()
    users = User.query.filter(User.name == "Rocks", User.pwd == "123456").all()
    for user in users:
        print("用户ID：{}，用户名字：{}，用户密码：{}".format(user.id, user.name, user.pwd))
    # or关系 用户名等于Rocks或者密码是123456
    users = User.query.filter(or_(User.name == "Rocks", User.pwd == "123456"))
    for user in users:
        print("用户ID：{}，用户名字：{}，用户密码：{}".format(user.id, user.name, user.pwd))

    # 根据ID升序排 从第二条开始取 取两条
    users = User.query.filter(or_(User.name == "Rocks", User.pwd == "123456")).order_by(User.id.asc()).offset(1).limit(2).all()
    # 根据ID降序排 从第一条开始取 取五条
    users = User.query.filter(or_(User.name == "Rocks", User.pwd == "123456")).order_by(User.id.desc()).offset(0).limit(2).all()
    for user in users:
        print("用户ID：{}，用户名字：{}，用户密码：{}".format(user.id, user.name, user.pwd))

    # GroupBY 分组  db.session方式可查询部分字段 User.query方式职能查询某个实体所有字段
    res = db.session.query(User.role_id, func.count(User.role_id)).group_by(User.role_id).all()
    print(res)
```

- 关联查询

```python
    # 获取Role
    role = Role.query.get(1)
    # 获取关联的Users
    users = role.users
    for user in users:
        print("UserId:{},UserName:{}".format(user.id, user.name))

    # 获取User
    user = User.query.get(1)
    # 获取Role ID
    role_id = user.role_id
    print(role_id)
    # 根据role_id获取role
    role = Role.query.get(role_id)
    print("Role ID:{}, Role Name:{}".format(role.id, role.name))
    # 获取关联Role 需要有backref属性才能获取
    role2 = user.role
    print("Role ID:{}, Role Name:{}".format(role2.id, role2.name))
```

- 修改查询之后获得的对象的打印信息，类似于Java的toString

```python
# 模型类
class User(db.Model):
    """用户表"""
    # 设置表名
    __tablename__ = "user"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    pwd = db.Column(db.String(50))
    # 外键
    role_id = db.Column(db.BigInteger, db.ForeignKey("role.id"))

    # 修改对象打印信息
    def __repr__(self):
        return "用户ID：{}，用户名：{}，角色ID：{}".format(self.id, self.name, self.role)


class Role(db.Model):
    """角色表"""
    __tablename__ = "role"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    # 关联属性 backref属性作用是反推 加上此属性后 User对象里的role_id属性就不是Role的ID 而是Role对象
    users = db.relationship("User", backref="role")

    # 修改对象打印信息
    def __repr__(self):
        return "角色ID：{}，角色名：{}".format(self.id, self.name)
```

7. 更新数据

```python
    # 数据更新
    # 1.先查再更 根据主键ID更新 两条SQL
    user = User.query.get(2)
    user.name = "stuff"
    db.session.add(user)
    db.session.commit()
    # 2.根据过滤条件去选择更新的数据进行更新 一条SQL
    row = User.query.filter_by(id=2).update({
        "name": "Customer"
    })
    print(row)
    # commit之后才能提交更新 row是更新的记录数
    db.session.commit()
```

8. 删除数据

```python
	# 数据删除
    # 先查后删 根据主键删除 两条SQL
    user = User.query.get(1)
    db.session.delete(user)
    db.session.commit()
```

### <a id="qy">Flask-Migrate</a>

> 数据库迁移扩展插件
>
> 在开发过程中，需要修改数据库模型，而且还要在修改之后更新数据库。最直接的方式就是删除旧表，但这样会丢失数据。
>
> 更好的解决办法是使用数据库迁移框架，它可以追踪数据库模式的变化，然后把变动应用到数据库中。
>
> 在Flask中可以使用Flask-Migrate扩展，来实现数据迁移。并且集成到Flask-Script中，所有操作通过命令就能完成。
>
> 为了导出数据库迁移命令，Flask-Migrate提供了一个MigrateCommand类，可以附加到flask-script的manager对象上。

1. 安装flask-migrate

> pip install flask-migrate
>
> pip install flask-script

2. 将Migrate插件配置进Manager管理器

```python
# 数据库迁移扩展插件

from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_script import Shell, Manager
from flask_migrate import Migrate, MigrateCommand

# flask对象
app = Flask(__name__)

# Manager管理器
manager = Manager(app)

# DB配置
app.config["SQLALCHEMY_DATABASE_URI"] = "mysql://rocks:Qy123456.@47.101.217.179/users"
app.config["SQLALCHEMY_COMMIT_ON_TEARDOWN"] = True
app.config["SQLALCHEMY_TRACK_MODIFITIONS"] = True
db = SQLAlchemy(app)

# 将Migrate注入app里
migrate = Migrate(app, db)

# 向manager管理器添加数据库迁移的命令 取名db
manager.add_command("db", MigrateCommand)

# 模型类
class User(db.Model):
    """用户表"""
    # 设置表名
    __tablename__ = "user"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    pwd = db.Column(db.String(50))
    # 外键
    role_id = db.Column(db.BigInteger, db.ForeignKey("role.id"))

    # 修改对象打印信息
    def __repr__(self):
        return "用户ID：{}，用户名：{}，角色ID：{}".format(self.id, self.name, self.role)


class Role(db.Model):
    """角色表"""
    __tablename__ = "role"
    id = db.Column(db.BigInteger, primary_key=True)
    name = db.Column(db.String(50), unique=True)
    # 关联属性 backref属性作用是反推 加上此属性后 User对象里的role_id属性就不是Role的ID 而是Role对象
    users = db.relationship("User", backref="role")

    # 修改对象打印信息
    def __repr__(self):
        return "角色ID：{}，角色名：{}".format(self.id, self.name)


if __name__ == "__main__":
    manager.run()
```

- 操作数据库
  - python flask_migrate.py db init：初始化迁移脚本
  - python flask_migrate.py db migrate：生成迁移文件
  - python flask_migrate.py db migrate -m "xx"：生成迁移文件并添加注释
  - python flask_migrate.py db upgrade：根据迁移文件升级
  - python flask_migrate.py db downgrade 版本号：降级到指定版本
  - python flask_migrate.py db history：查看历史记录

### <a id="mail">Flask-Mail</a>

> Flask发送邮件的扩展插件
>
> 在开发过程中，很多应用程序都需要通过邮件提醒用户，Flask的扩展包Flask-Mail通过包装了Python内置的smtplib包，可以用在Flask程序中发送邮件。
>
> Flask-Mail连接到简单邮件协议（Simple Mail Transfer Protocol,SMTP）服务器，并把邮件交给服务器发送。
>
> 发送邮件首先需要开启邮箱的SMTP服务，之后根据要求获取授权码

- 示例

```python
# Flask-Mail发送邮件

from flask import Flask
from flask_mail import Mail, Message

app = Flask(__name__)
# 配置邮件：服务器／端口／传输层安全协议／邮箱名／密码
app.config.update(
    DEBUG=True,
    MAIL_SERVER='smtp.qq.com',
    MAIL_PORT=465,
    MAIL_USE_SSL=True,
    MAIL_USE_TLS=False,
    MAIL_USERNAME='674099234@qq.com',
    MAIL_PASSWORD="kbpsxivdfgibbeib",
)

mail = Mail(app)


@app.route('/')
def index():
    # sender 发送方，recipients 接收方列表
    msg = Message("This is a test Mail By Lzx", sender='674099234@qq.com', recipients=['2533639692@qq.com', '1804283990@qq.com', 'rocks526@126.com'])
    # 邮件内容
    msg.body = "Flask test mail"
    # 发送邮件
    mail.send(msg)
    return "Send　Succeed"


if __name__ == "__main__":
    app.run()
```

- 常见异常
  - 没有开启POP3/SMTP服务：smtplib.SMTPAuthenticationError: (454, 'Authentication failed, please open smtp flag first!')
  - 授权码异常：smtplib.SMTPAuthenticationError: (535, 'Authentication failed')
  - 没有开启TLS：smtplib.SMTPServerDisconnected: Connection unexpectedly closed

# <a id="cs">测试与部署</a>

### <a id="lt">蓝图</a>

> flask按照功能模块拆分时，多个python文件存在循环引用问题，子模块需要引用主模块的flask对象去注册路由，主模块需要导入子模块的注册路由信息，因此无限循环。
>
> 针对循环引用问题的解决方案：
>
> - 通过将主模块导入子模块的代码放在函数里，延迟加载
> - 不通过app.route()的方式注册路由，改用手动注册或者自定义装饰器注册
> - 蓝图

- 蓝图的定义

蓝图：用于实现单个应用的视图、模板、静态文件的集合。一个蓝图类似于Django里面的一个应用。

- 蓝图实现机制

蓝图是保存了一组将来可以在应用对象上执行的操作。注册路由就是一种操作,当在程序实例上调用route装饰器注册路由时，这个操作将修改对象的url_map路由映射列表。当我们在蓝图对象上调用route装饰器注册路由时，它只是在内部的一个延迟操作记录列表defered_functions中添加了一个项。当执行应用对象的 register_blueprint() 方法时，应用对象从蓝图对象的 defered_functions 列表中取出每一项，即调用应用对象的 add_url_rule() 方法，这将会修改程序实例的路由映射列表。

- 蓝图的使用

  - 创建蓝图对象：admin表示蓝图的名称，name是蓝图所在模块

  ```python
admin = Blueprint('admin',__name__)；
  ```
  
  - 注册蓝图路由：
  
    ```python
  @admin.route('/')
    def admin_index():
        return 'admin_index'
    ```
  
  - 在flask实例中注册蓝图信息：
  
  	```python
    app.register_blueprint(admin,url_prefix='/admin')
    ```

- 示例

```python
# flask主程序
from flask import Flask
from blueprint.user import user
from blueprint.workorder import workorder

app = Flask(__name__)

app.config["DEBUG"] = True

# 注册蓝图并设置蓝图请求前缀
app.register_blueprint(user, url_prefix="/user")
app.register_blueprint(workorder, url_prefix="/workorder")


@app.route("/get")
def get_all():
    return "main get info success!"


if __name__ == "__main__":
    print(app.url_map)
    app.run()
----------------------------------------------------
# user模块

from flask import Blueprint

# 创建蓝图对象
user = Blueprint("user", __name__)


# 注册路由
@user.route("/get")
def get_user():
    return "get User Info success!"
-----------------------------------------------------
# 工单模块

from flask import Blueprint

workorder = Blueprint("workorder", __name__)


@workorder.route("/get")
def get_workorder():
    return "get WorkOrder success!"
----------------------------------------------------
Map([<Rule '/workorder/get' (OPTIONS, HEAD, GET) -> workorder.get_workorder>,
 <Rule '/user/get' (OPTIONS, HEAD, GET) -> user.get_user>,
 <Rule '/get' (OPTIONS, HEAD, GET) -> get_all>,
 <Rule '/static/<filename>' (OPTIONS, HEAD, GET) -> static>])
```

- 注意事项
  - 一般项目中会根据功能模块进行划分，在蓝图目录里创建static，template等目录，类似于Django的结构，在蓝图里，搜索template时会首先寻找主程序下的template目录，之后再查找蓝图下的template目录

### <a id="dy">单元测试</a>

- 断言

> 断言就是判断一个函数或对象的一个方法所产生的结果是否符合你期望的那个结果。 python中assert断言是声明布尔值为真的判定，如果表达式为假会发生异常。单元测试中，一般使用assert来断言结果。

- 示例

```python
# 断言练习
num = 12
assert num >= 0, "num小于0"
sql = "select * from user where id = 8"
assert sql.__contains__("select from"), "sql不包含'select from'语句"
----------------------------------------
第二个断言不满足条件，抛出异常：
AssertionError: sql不包含'select from'语句
```

- Python中的单元测试库：unittest

```python
# 单元测试练习
import unittest

class TestClass(unittest.TestCase):

    # 此方法会在所有单元测试执行之前执行
    def setUp(self) -> None:
        print("------------start----------------")

    # 此方法会在所有单元测试执行完之后执行
    def tearDown(self) -> None:
        print("--------------end-------------------")

    # 测试代码 必须以test_开头
    def test_number(self):
        num = 12
        assert num >= 0, "num小于0"
        sql = "select * from user where id = 8"
        assert sql.__contains__("select from"), "sql不包含'select from'语句"
       
if __name__ == '__main__':
    suite = unittest.TestSuite()
    suite.addTest(TestClass('test_number'))
    runner = unittest.TextTestRunner()
    runner.run(suite)
----------------------------------------------
常用的断言方法：
self.assertEqual     如果两个值相等，则pass
self.assertNotEqual  如果两个值不相等，则pass
self.assertTrue      判断bool值为True，则pass
self.assertFalse     判断bool值为False，则pass
self.assertIsNone    不存在，则pass
self.assertIsNotNone 存在，则pass
```

- flask中的单元测试

```python
# flask的单元测试
import unittest
from flask_test_main import app
import json

class flaskTest(unittest.TestCase):

    def test_flask(self):
        client = app.test_client()
        res = client.get("/index")
        json_data = res.data
        obj = json.loads(json_data)
        self.assertIn("code", obj)
        self.assertEqual(obj["code"], 200)

if __name__ == "__main__":
    # suite = unittest.TestSuite()
    # suite.addTest(flaskTest('test_flask'))
    # runner = unittest.TextTestRunner()
    # runner.run(suite)
    unittest.main()
```

### <a id="bs">部署</a>

> 在程序开发时使用的flask自带的服务器，完成了web服务的启动。在生产环境中，flask自带的服务器，无法满足性能要求，我们这里采用Gunicorn做wsgi容器，来部署flask程序。Gunicorn（绿色独角兽）是一个Python WSGI的HTTP服务器。从Ruby的独角兽（Unicorn ）项目移植。该Gunicorn服务器与各种Web框架兼容，实现非常简单，轻量级的资源消耗。Gunicorn直接用命令启动，不需要编写配置文件，相对uWSGI要容易很多。
>
> **Windows不支持Gunicorn服务器，需要运行于Linux环境。**

- 常见概念
  - **WSGI：**全称是Web Server Gateway Interface（web服务器网关接口），它是一种规范，它是web服务器和web应用程序之间的接口。
  - **uwsgi：**是一种传输协议，用于定义传输信息的类型。
  - **uWSGI：**是实现了uwsgi协议WSGI的web服务器。

- 一般部署采用nginx + gunicorn + flask

- Gunicorn使用

  - 安装 gunicorn 

  >  pip install gunicorn

  - 查看gunicorn常用参数

  >  gunicorn -h

  - 运行程序

  > gunicorn 文件名称:应用程序实例名称
  >
  > 例如：gunicorn flask:app
  >
  > 直接运行默认127.0.0.1:8000

  - 指定端口号

  > -w指定运行进程数  -b指定IP和端口  --access-logfile执行日志存放位置
  >
  > 例如：gunicorn -w 4 -b 127.0.0.1:9000 --access-logfile ./logs/flask.log flask:app

