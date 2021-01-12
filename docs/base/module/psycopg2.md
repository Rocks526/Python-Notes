# psycopg2模块介绍

psycopg2模块可以让Python操作postgresql数据库。

- 安装Python操作pg的包：psycopg2

> pip install psycopg2

# psycopg2代码示例

```python
import psycopg2

# 创建连接对象
conn = psycopg2.connect(database="postgres", user="postgres", password="123456", host="localhost", port="5432")
# 获取游标
cur = conn.cursor()
# 执行SQL
cur.execute("select * from user_info")
# 获取结果 返回元组
res = cur.fetchall()
print(res)
# 提交事务
conn.commit()
# 关闭连接
cur.close()
conn.close()

[(1, 'Jack', '123456', '17629154002'), (2, 'Jack2', '123456', '17629154003'), (3, 'Jack3', '123456', '17629154004'), (4, 'Jack4', '123456', '17629154005'), (5, 'Jack5', '123456', '17629154006'), (6, 'unknow', '123456', '17629154008')
```

