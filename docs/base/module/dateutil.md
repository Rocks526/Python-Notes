# datautil介绍

dateutil是一个时间提取模块，用于从大量文本字符串中提取出可能存在的时间字段，分为严格模式和非严格模式。

- 严格模式：必须包含完整的日期和时间信息
- 非严格模式：只包含其中一部分即可

dateutil同时支持获取提取出的日期字段的下标。

# dateutil代码示例

```python
from dateutil import parser
import datefinder

log = "<30>Jan 03 15:15:29 localhost alert: {id=706222} {time=2020-01-03 15:15:23} {lev; +http://www.google.com/bot.html)”。本 {dheader=HTTP/1.1 301 Moved Permanently"
log2 = "<129> 2020-08-14 14:20:46 System_ID=HOST Logcategory=Webfilterft Malware=Phone Home Action=blocked "
log3 = """
<141>Apr 17 19:56:25 科技园 IPS: SerialNum=100JM-0S3B8-27009-J3723-GGH63 GenTimeentType=命令执行 ProtocolType=HTTP Action=drop
"""

matches = datefinder.find_dates(text=log3, strict=False, index=True, source=True)

print(matches)

for match in matches:
    print(match)
```

