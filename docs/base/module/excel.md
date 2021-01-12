# excel模块介绍

Python操作Excel的库众多，常用有以下几种：

- xlrd库：从excel中读取数据，支持xls、xlsx
- xlwt库：向excel中写入数据
- xlutils库：修改excel数据
- openpyxl：主要针对xlsx格式的excel进行读取和编辑

# 代码示例

### xlwt

```python
import xlwt

# 创建Excel对象
excel = xlwt.Workbook()
# 添加页面
sheet = excel.add_sheet('user_info')
sheet.write(0, 0, '用户ID')
sheet.write(0, 1, '用户名')
sheet.write(0, 2, '用户密码')

users_info = [
    [1, 'Jack', '123456'],
    [2, 'Mary', '984264'],
    [3, 'Merry', '200481'],
    [4, 'Lucy', '89100']
]

# 添加用户数据
line = 1
for user in users_info:
    col = 0
    for field in user:
        sheet.write(line, col, field)
        col += 1
    line += 1

# 保存Excel
excel.save('user_infos.xls')
```

### xlrd

```python
import xlrd

# 打开excel
excel = xlrd.open_workbook('user_infos.xls')
# 获取sheet
sheet = excel.sheet_by_index(0)
# sheet = excel.sheet_by_name('user_info')
# 输出多少行 多少列
print('一共有{}行，{}列'.format(sheet.nrows, sheet.ncols))
# 获取某个位置的值
print('第二行第二列的值是{}'.format(sheet.cell(1, 1).value))
# 获取整行整列的值
print('第一行的所有值为：{}'.format(sheet.row_values(0)))
print('第一列的所有值为：{}'.format(sheet.col_values(0)))
# 输出所有值
for i in range(sheet.nrows):
    print(sheet.row_values(i))

一共有5行，3列
第二行第二列的值是Jack
第一行的所有值为：['用户ID', '用户名', '用户密码']
第一列的所有值为：['用户ID', 1.0, 2.0, 3.0, 4.0]
['用户ID', '用户名', '用户密码']
[1.0, 'Jack', '123456']
[2.0, 'Mary', '984264']
[3.0, 'Merry', '200481']
[4.0, 'Lucy', '89100']
```

### xlutils

```python
import xlrd
from xlutils import copy

# xlrd读取一个excel
excel = xlrd.open_workbook('user_infos.xls')
# xlutils复制一份之前的excel
new_excel = copy.copy(excel)
# 获取sheet
sheet = new_excel.get_sheet(0)
# 修改数据
sheet.write(1, 1, 'Rocks')
# 保存
new_excel.save('new_user_infos.xls')
```

### openpyxl

```python
from openpyxl import Workbook
import datetime

# 实例化
wb = Workbook()

# 激活 worksheet
ws = wb.active

# 创建表
# 方式一：插入到最后(default)
ws1 = wb.create_sheet("Sheet1")
# 方式二：插入到第二个
ws2 = wb.create_sheet("Sheet2", 1)

# 插入数据
# 方式一：数据可以直接分配到单元格中(可以输入公式)
ws['A1'] = 42
# 方式二：可以附加行，从第一列开始附加(从最下方空白处，最左开始)(可以输入多行)
ws.append([1, 2, 3])
# 方式三：Python 类型会被自动转换
ws['A3'] = datetime.datetime.now().strftime("%Y-%m-%d")

# 访问单元格
# 方法一
c = ws['A4']
# 方法二：row 行；column 列
d = ws.cell(row=4, column=2, value=10)
# 方法三：只要访问就创建
for i in range(1, 101):
    for j in range(1, 101):
        ws.cell(row=i, column=j)

# 选择表
ws3 = wb["Sheet2"]
ws4 = wb.get_sheet_by_name("Sheet1")

# 查看表名
print(wb.sheetnames)

# 获得最大列和最大行
print(ws.max_row)
print(ws.max_column)

# 保存数据
wb.save('demo.xlsx')
```

