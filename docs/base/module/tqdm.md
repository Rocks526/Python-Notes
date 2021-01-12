# tqdm库介绍

Tqdm 是一个快速，可扩展的Python进度条，可以在 Python 长循环中添加一个进度提示信息，用户只需要封装任意的迭代器 tqdm(iterator)。

### 安装

```shell
pip install tqdm
```

# tqdm库常用api

 tqdm(iterable=range(100), desc="Process", mininterval=3, initial=20)

- iterable：整个序列
- desc：描述信息
- mininterval：打印时间间隔
- initial：初始值

 trange等价于tqdm(range(n))

# tqdm代码示例

```python
# tqdm示例 Python进度条

from tqdm import tqdm, trange
import time

# tqdm(list)方法可以传入任意一种list
for i in tqdm(range(100)):
    # 每0.5秒增加1%
    time.sleep(0.5)

for char in tqdm(['a', 'b', 'c', 'd', 'e']):
    # 每0.5秒到下一个元素 展示的进度条是百分数
    print(char)
    time.sleep(0.5)

# tqdm(range(100))  等价于  trange(100)
for i in trange(100):
    pass

# desc描述信息 mininterval是进度条展示间隔 每n秒打印一次 initial是初始值
for i in tqdm(iterable=range(100), desc="Process", mininterval=3, initial=20):
    time.sleep(1)
    print(i)
```

