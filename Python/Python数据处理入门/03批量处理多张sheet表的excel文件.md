# 第三章: 批量处理多张sheet表的excel文件

@[TOC](Python数据处理入门)



## 良好习惯(十分重要)

在正式处理数据之前, 请保证你数据和文件名的规范性, **否则报错是你活该**, 至少满足以下要求:

+ 不要在最初的数据文件中添加你的处理结果

  > 保留原始数据文件作为副本, 你的处理结果大多数情况下只会给专业的人添加麻烦

+ 不要使用合并单元格

  > 你做的是数据处理工作, 不是数据展示工作, 此外合并单元格也并没有让表格显得高级

+ 不要把两张不相关的表放到一起, 空表也算

  > 一个程序专注执行一件事情, 如果你打算写一个能够处理任意情况的程序的话, 那你也不会在看这篇垃圾文章了

+ 存放数据的目录下不要存放其它不相关的文件

  > 理由同上

+ 存放数据的目录和文件名尽可能不要使用中文字符, 特别是那些乱七八糟的标点符号, 尽可能保持全英文, 数字或下划线



## 代码处理

```python
# 导入需要的库
import pandas as pd
import os

#创建一个空的DataFrame对象
result = pd.DataFrame()
#设置文件路径
directory_path = input("请输入数据所在目录的文件路径:")
#获取到该目录下的所有文件
filnames = os.listdir(directory_path)
for filename in filenames:
    #拼接成文件的路径
    filepath = directory_path + os.sep + filename
    #获取所有的sheet构成的字典对象
    dfs = pd.read_excel(filepath, sheet_name='None')
    #遍历每一个sheet生成的DataFrame对象,即df
    for index, df in dfs.item():
        #对于每一个sheet都新建一个字典,用来记录要保存的数据
        tmp = {}
        
        #具体的数据处理内容
        #......
        #tmp['A'] = A
        #tmp['B'] = B
        #......
        
        #这里需要重新赋值给result, 否则不生效
        result = result.append(tmp,ignore_index=True)

#保存处理结果
result.to_csv('result.csv')
```

