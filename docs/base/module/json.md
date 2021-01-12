# json模块介绍

Json库是Python提供专门用来处理Json格式的模块，主要包含以下四个方法：

- dumps：将对象序列化为Json格式字符串
- dump：将对象序列化为Json格式字符串并写入文件
- loads：将Json字符串反序列化为对象
- load：从文件中读取Json字符串反序列化为对象

附带所有参数的语法函数：

```python
json.dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, encoding="utf-8", default=None, sort_keys=False, **kw)

json.loads(s[, encoding[, cls[, object_hook[, parse_float[, parse_int[, parse_constant[, object_pairs_hook[, **kw]]]]]]]])
```

# 代码示例

```python
bool_word = True
int_word = 1
float_word = 8.99
str_word = "这是一个字符串!"
None_word = None
tuple_word = (12, "Hello", 88.9)
list_word = [12, "Hello", 88.9]
dict_word = {"key1": "value1", "key2": "测试数据"}
set_word = {12, "Hello", 88.9}
print(json.dumps(bool_word))
print(json.dumps(int_word))
print(type(json.dumps(float_word)))
print(json.dumps(str_word))
print(json.dumps(None_word))
print(type(json.dumps(tuple_word)))
print(type(json.dumps(list_word)))
print(json.dumps(dict_word))
print(json.dumps(set_word))

with open("save.txt", "a+") as f:
    json.dump(dict_word, f)
---------------------------------------
true
1
<class 'str'>
"\u8fd9\u662f\u4e00\u4e2a\u5b57\u7b26\u4e32!"
null
<class 'str'>
<class 'str'>
{"key1": "value1", "key2": "\u6d4b\u8bd5\u6570\u636e"}
TypeError: Object of type set is not JSON serializable
生成save.txt文件
```

> 利用sort_keys可以格式化输出，ensure_ascii输出汉字，不进行ascii编码。

```python
# 格式化输出
jsonStr = json.dumps(dict_word, sort_keys=True, indent=4, separators=(',', ': '), ensure_ascii=False)
print(jsonStr)

{
    "key1": "value1",
    "key2": "测试数据"
}
```

# 类型转换

python 原始类型向 json 类型的转化对照表：

| Python           | JSON     |
| ---------------- | -------- |
| dict             | object   |
| list, tuple      | array    |
| str, unicode     | string   |
| int, long, float | number   |
| True             | true     |
| False            | false    |
| None             | null     |
| set              | 抛出异常 |

json 类型转换到 python 的类型对照表：

| JSON          | Python    |
| ------------- | --------- |
| object        | dict      |
| array         | list      |
| string        | unicode   |
| number (int)  | int, long |
| number (real) | float     |
| true          | True      |
| false         | False     |
| null          | None      |