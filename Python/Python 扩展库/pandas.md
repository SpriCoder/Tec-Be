

# 1. Pandas
1. 基于SciPy和NumPy
2. 高效的Series和DataFrame数据结构
3. 强大的可扩展数据操作与分析的Python库
4. 高效处理大数据集的切片等功能
5. 提供优化库功能读写多种文件格式，如CSV、HDF5

# 2. DataFrame
1. 可以切片访问
2. 一个表格型的数据结构，包含一组类似于index的有序列，每一列的值可以使不同的
3. 使用`head(number)`来访问前几行
4. 使用`tail(number)`来访问后几行
5. 使用`loc[index]`来按照字段进行访问第几段数据

## 2.1. 创建DataFrame
1. 由列表创建
2. 由元组创建
3. 由字典创建
```python
data = {'name':['1','2','3'],'pay':[100,200,300]}
frame = pd.DataFrame(data)
# frame格式如下
#    name    pay
#0      1    100
#1      2    200
#2      3    300
```
4. 由ndarray创建
```python
data = np.array([('1',100),('2',200),('3',300)])
frame = pd.DataFrame(data,index = range(1,4),columns = ['name','pay'])
# frame格式如下
#    name    pay
#1      1    100
#2      2    200
#3      3    300
```
5. 由Series创建
6. 由文件创建

## 2.2. 查看相关属性
1. `.index`查看行索引
2. `.columns`查看列索引
3. `.values`查看值
4. 修改直接赋值:`frame.index = range(2,5)...`

## 2.3. 基本操作
1. iloc(,):第一维表示行，第二维表示列
2. 对对象进行修改:直接赋值`frame['name']='admin'`
3. 删除某一列:`del frame['name']`
4. 一般查找先取列，再说行，然后结果会被转成字符串
5. 插入行:`frame.loc(number)={(,),(,),(,)}`
6. 插入列:`frame[name] = [,,]`
7. 删除行:`frame.drop(number)`
8. 可以直接用列与列之间进行加减乘除默认对应项操作`frame['1'] = frame['2'] + frame['3']`

## 2.4. sort_values()排序
1. 将Dataframe数据集按照某个字段中的数据进行排序
2. 函数声明:`DataFrame.sort_values(by=‘##’,axis=0,ascending=True, inplace=False, na_position=‘last’)`
   1. `by`:指定列名(axis=0或’index’)或索引值(axis=1或’columns’)，表示使用哪一个列进行排序
   2. `axis`:若axis=0或’index’，则按照指定列中数据大小排序；若axis=1或’columns’，则按照指定索引中数据大小排序，默认axis=0
   3. `ascending`:是否按指定列的数组升序排列，默认为True，即升序排列
   4. `inplace`:是否用排序后的数据集替换原来的数据，默认为False，即不替换
   5. `na_position`:{‘first’,‘last’}，设定缺失值的显示位置
3. 示例

```py
import pandas as pd
df=pd.DataFrame({'col1':['A','A','B',np.nan,'D','C'],
                 'col2':[2,1,9,8,7,7],
                 'col3':[0,1,9,4,2,8]
}]
# 依据第一列排序，并将该列空值放在首位
df.sort_values(by=['col1'],na_position='first')
# 依据第一列排序，按降序排列，并替换原数据
df.sort_values(by =0,ascending=False,axis=1)
# 依据第二、三列，数值降序排序
df.sort_values(by=['col2','col3'],ascending=False)
# 依据第一列中数值排序，按降序排列，并替换原数据
df.sort_values(by=['col1'],ascending=False,inplace=True,na_position='first')
```

## 2.5. 条件过滤

### 2.5.1. 布尔运算
```py
# 判断元素是否是3的倍数，得到的新对象格式与data完全一样
data % 3 == 0
#        A      B      C      D
# a   True  False  False   True
# b  False  False   True  False
# c  False   True  False  False
# d   True  False  False   True
# e  False  False   True  False

# data['B']是一个Series，因此运算结果也是一个Series
data['B'] > 5
# a    False
# b    False
# c     True
# d     True
# e     True
# Name: B, dtype: bool

# 使用loc或iloc选取结果也可以进行布尔运算，也可以将多个运算结果再次进行与或运算
(data.loc[:,'B':'D']>5) & (data.loc[:,'B':'D']<10)
#        B      C      D
# a  False  False  False
# b  False   True   True
# c   True  False  False
# d  False  False  False
# e  False  False  False
```

### 2.5.2. 过滤
1. 将布尔运算结果再次传入DataFrame中，会得到一个过滤后的新对象，对应位置为False的值均置NaN：若对单列进行布尔运算如data['B']>5，结果是Series，传入DataFrame后会对行进行过滤，但若对单行进行布尔运算如data.loc['c']>10，得到的Series是不能进行过滤的

```py
data[data%3==0]
#       A    B     C     D
# a   0.0  NaN   NaN   3.0
# b   NaN  NaN   6.0   NaN
# c   NaN  9.0   NaN   NaN
# d  12.0  NaN   NaN  15.0
# e   NaN  NaN  18.0   NaN

# 对单列进行布尔运算，对行进行过滤
data[data['B']>5]
#     A   B   C   D
# c   8   9  10  11
# d  12  13  14  15
# e  16  17  18  19

data[(data.loc[:,'B':'D']>5) & (data.loc[:,'B':'D']<10)]
#     A    B    C    D
# a NaN  NaN  NaN  NaN
# b NaN  NaN  6.0  7.0
# c NaN  9.0  NaN  NaN
# d NaN  NaN  NaN  NaN
# e NaN  NaN  NaN  NaN

# 提取col1的值为A的所有行
df[df["col1"] == "A"]
# 提取col1的值为A并且col2的值为2的行
df[(df["col1"] == "A") & (df["col2"] == 2)]
```

### 2.5.3. 赋值
1. 进行切片和条件过滤可以赋值，不返回新对象，直接对原对象进行操作

```py
data[(data.loc[:,'B':'D']>5) & (data.loc[:,'B':'D']<10)]='X'
#     A   B   C   D
# a   0   1   2   3
# b   4   5   X   X
# c   8   X  10  11
# d  12  13  14  15
# e  16  17  18  19
```

## 2.6. 索引

### 2.6.1. 修改索引
> 修改索引顺序，对传入的DataFrame重新索引，返回一个新的DataFrame
```py
import pandas as pd
data=pd.DataFrame([[0,1,2],[3,4,5],[6,7,8]],index=['a','c','d'],columns=['A','B','C']

# 传入参数没指定参数名，且只有一列序列，默认对行索引进行修改，若索引值不存在则置NaN
data.reindex(['c','a','b','d'])
#      A    B    C
# c  3.0  4.0  5.0
# a  0.0  1.0  2.0
# b  NaN  NaN  NaN
# d  6.0  7.0  8.0

# 同时对行索引和列索引进行修改
data.reindex(index=['a','b','c','d'],columns=['B','C','D','A'])
#      B    C   D    A
# a  1.0  2.0 NaN  0.0
# b  NaN  NaN NaN  NaN
# c  4.0  5.0 NaN  3.0
# d  7.0  8.0 NaN  6.0
```

### 2.6.2. 修改索引值
1. 只修改索引值，不对DataFrame中的数据的顺序修改，闯入列表必须和索引长度相同
2. 直接修改原DataFrame

```py
data.index = list("xyz")
data.columns = list("xyz")
print(data)
#    X  Y  Z
# x  0  1  2
# y  3  4  5
# z  6  7  8
```

### 2.6.3. 删除索引
1. 删除传入的索引项，返回新的DataFrame
2. 函数声明:`drop(axis=0,index,columns,inplace=False)`
   1. `axis`:指定删除哪个轴上的索引，默认行索引`axis=0`或`axis='index'`，列索引为`axis=1`或`axis='columns'`
   2. `index`，`columns`是用来显式指定删除哪个轴的索引
   3. `inplace`:默认`False`返回新对象，`True`则直接修改原对象，无返回

```py
# 使用axis指定轴
data.drop(['x'],axis=0)
#    X  Y  Z
# y  3  4  5
# z  6  7  8
# 显示指定删除哪个轴的索引
data.drop(columns=['X','Z'])
#    Y
# x  1
# y  4
# z  7
# 直接修改对象，无返回
data.drop(['y','z'],inplace=True)
#    X  Y  Z
# x  0  1  2
```

## 2.7. 切片选取

### 2.7.1. 直接选取
1. 使用一个值或序列进行索引，获取一个或多个列

```py
data
#     A   B   C   D
# a   0   1   2   3
# b   4   5   6   7
# c   8   9  10  11
# d  12  13  14  15
# e  16  17  18  19
# 使用序列对列索引选择
data[['A','C']]
#     A   C
# a   0   2
# b   4   6
# c   8  10
# d  12  14
# e  16  18
# 使用切片对行索引进行选择，对行索引的b~e(索引位置序号为1~4)进行切片，切片使用索引位置序号，结果不包含末端索引
data[1:4]
data['b':'e']
#     A   B   C   D
# b   4   5   6   7
# c   8   9  10  11
# d  12  13  14  15
# e  16  17  18  19
```

2. 若索引的标签值是整数，在使用标签值进行索引选取的时候会造成混乱，比如要选取标签值为1的项目，使用data[1]，但得到的却是索引位置为1的项目。因此若索引的标签值含有整数，在进行选取时应该总是使用标签。为了更准确，请使用loc(标签)或iloc(整数)。

### 2.7.2. 使用loc和iloc进行选取
1. 上面的方法不能对列使用切片方式存取，而`loc`和`iloc`是支持的
2. `loc(行标签, 列标签)`:使用标签值选取
3. `iloc(行序号，列序号)`:使用索引位置进行选取
   1. 结果若为多行多列:DataFrame
   2. 结果为莫一行，某一列，则得到对应的Series，但是如果一列表形式指定某行或某列，如`["B"]`，则得到的是DataFrame
   3. 如果行和列都指定某一个标签，得到的是指定位置的数据，类型为对应数据类型
   4. `loc`使用标签切片包含末端，`iloc`使用标签切片不包含末端项

```py
# 选取行标签为'a'的行，得到一个name为行标签的Series
data.loc['a']
# A    0
# B    1
# C    2
# D    3
# Name: a, dtype: int32

# 选取多行，得到DataFrame
data.loc[['a','c']]
#    A  B   C   D
# a  0  1   2   3
# c  8  9  10  11

# 选取多行和指定的某一列，得到name为列标签的Series
data.loc[['a','c'],'B']
# a    1
# c    9
# Name: B, dtype: int32

# 使用切片方式同时选取行和列
data.loc['a':'d','B':'D']
#     B   C   D
# a   1   2   3
# b   5   6   7
# c   9  10  11
# d  13  14  15

# 切片方式选择所有行和指定列
data.loc[:,'B':'D']
#     B   C   D
# a   1   2   3
# b   5   6   7
# c   9  10  11
# d  13  14  15
# e  17  18  19

# iloc使用索引序号进行切片，不包含末端项
data.iloc[0:4,1:3]
#     B   C
# a   1   2
# b   5   6
# c   9  10
# d  13  14
```

## 2.8. 按行遍历
`df = pd.DataFrame([{'c1':10, 'c2':100}, {'c1':11,'c2':110}, {'c1':12,'c2':120}])`
### 2.8.1. 使用iterrows和itertuples
1. 使用iterrows():不应该修改正在迭代的数据，迭代器返回的是副本

```py
for index, row in df.iterrows():
  print row["c1"], row["c2"]
```

2. 使用itertuples()：列名称将被重命名为位置名称，如果它们是无效的Python标识符，重复或以下划线开头。对于大量的列(> 255)，返回常规元组。

```py
for row in df.itertuples(index=True, name='Pandas'):
  print getattr(row, "c1"), getattr(row, "c2")
```

### 2.8.2. apply
1. 使用apply来便利行并访问函数的多个列

```py
def valuation_formula(x, y):
    return x * y * 0.5
 
df['price'] = df.apply(lambda row: valuation_formula(row['x'], row['y']), axis=1)
```

### 2.8.3. 使用iloc方法
```py
for i in range(0, len(df)):
    print df.iloc[i]['c1'], df.iloc[i]['c2']
```

## 2.9. DataFrame和其他形式的转换

### 2.9.1. DataFrame & CSV
```py
DataFrame.to_csv(path_or_buf=None, sep=', ', na_rep='', float_format=None, columns=None, header=True, index=True,
                 index_label=None, mode='w', encoding=None, compression=None, quoting=None, quotechar='"',
                 line_terminator='\n',tupleize_cols=None, date_format=None, doublequote=True,
                 escapechar=None, decimal='.')
```

- 参数：
  - `path_or_buf`:文件路径，如果没有指定则将会直接返回字符串的 json
  - `sep`:输出文件的字段分隔符，默认为 ","
  - `na_rep`:用于替换空数据的字符串，默认为''
  - `float_format`:设置浮点数的格式(几位小数点)
  - `columns`:要写的列
  - `header`:是否保存列名，默认为 True ，保存
  - `index`:是否保存索引，默认为 True ，保存
  - `index_label`:索引的列标签名

```py
# 读取csv文件
df = pd.read_csv("csvpath")
# 保存为csv文件
df.to_csv("csvpath")
```

### 2.9.2. DataFrame和JSON
```py
DataFrame.to_json(path_or_buf=None, orient=None, date_format=None, double_precision=10, force_ascii=True,
                  date_unit='ms', default_handler=None, lines=False, compression=None, index=True)
```

- 参数：
  - path_or_buf : 文件路径，如果没有指定则将会直接返回字符串的 json。

```py
# 读取json文件
df = pd.read_json("jsonpath")
# 保存为json文件
df.to_json("jsonpath")
```

### 2.9.3. DataFrame和HTML
```py
DataFrame.to_html(buf=None, columns=None, col_space=None, header=True, index=True, na_rep='NaN', formatters=None,
                  float_format=None, sparsify=None, index_names=True, justify=None, bold_rows=True, classes=None,
                  escape=True, max_rows=None, max_cols=None, show_dimensions=False, notebook=False, decimal='.',
                  border=None, table_id=None)
# 读取HTML文件
read_html = pd.read_html("html_path")
# 保存html文件
with open("test.html", 'w') as f:
    f.write(HEADER)
    f.write(df.to_html(classes='df'))
    f.write(FOOTER)
```

### 2.9.4. DataFrame和EXCEL文件
```py
DataFrame.to_excel(excel_writer, sheet_name='Sheet1', na_rep='', float_format=None, columns=None, 
                   header=True, index=True, index_label=None, startrow=0, startcol=0, engine=None,
                   merge_cells=True, encoding=None, inf_rep='inf', verbose=True, freeze_panes=None)
```

## 2.10. map方法
1. map可以对某一列的数据进行修改
2. `data["col1"] = data["col1"].map(lambda x : x ** 2)`

## 2.11. apply方法

### 2.11.1. Series的apply
1. 自动遍历真个Series，对每一个元素运行指定的函数，数据如下图所示

| Name | Nationality | Score |
| ---- | ----------- | ----- |
| 张   | 汉          | 400   |
| 李   | 回          | 450   |
| 王   | 汉          | 460   |

2. 如果民族不是汉族，我们为其总分加5

```py
import pandas as pd

df = pd.read_csv("studuent-score.csv")
df['ExtraScore'] = df['Nationality'].apply(lambda x : 5 if x != '汉' else 0)
df['TotalScore'] = df['Score'] + df['ExtraScore']
```

3. apply()函数也可以执行python内置的函数:`df['NameLength'] = df['Name'].apply(len)`

### 2.11.2. DataFrame的apply()
1. DataFrame.apply() 函数则会遍历每一个元素，对元素运行指定的 function。比如下面的示例：

```py
import pandas as pd
import numpy as np

matrix = [
    [1,2,3],
    [4,5,6],
    [7,8,9]
]
# 修改行列索引值
df = pd.DataFrame(matrix, columns=list('xyz'), index=list('abc'))
# 对每一个元素计算平方
df.apply(np.square)
#     x   y   z
# a   1   4   9
# b  16  25  36
# c  49  64  81

# 对x行计算平方
df.apply(lambda x : np.square(x) if x.name=='x' else x)
#     x  y  z
# a   1  2  3
# b  16  5  6
# c  49  8  9

# 对x和y行的计算平方
df.apply(lambda x : np.square(x) if x.name in ['x', 'y'] else x)
#     x   y  z
# a   1   4  3
# b  16  25  6
# c  49  64  9

# 第一行进行平方运算
df.apply(lambda x : np.square(x) if x.name == 'a' else x, axis=1)
#     x  y  z
# a   1  4  9
# b   4  5  6
# c   7  8  9
```

### 2.11.3. apply计算时间差
```py
import pandas as pd
import datetime as dt

def get_interval_days(arrLike, start, end):   
    start_date = dt.datetime.strptime(arrLike[start], '%Y-%m-%d')
    end_date = dt.datetime.strptime(arrLike[end], '%Y-%m-%d') 

    return (end_date - start_date).days


wbs = {
    "wbs": ["job1", "job2", "job3", "job4"],
    "date_from": ["2019-04-01", "2019-04-07", "2019-05-16","2019-05-20"],
    "date_to": ["2019-05-01", "2019-05-17", "2019-05-31", "2019-06-11"]
}

df = pd.DataFrame(wbs)
df['elapsed'] = df.apply(
    get_interval_days, axis=1, args=('date_from', 'date_to'))
```

### 2.11.4. apply完成标准化
```py
import pandas as pd
import numpy as np
df = pd.DataFrame(data={
    'A':[1,1,2,2,2],
    'B':[2,3,4,6,5],
    'C':[3,8,5,12,6]
})
# 标准化
df.apply(lambda x : (x-np.min(x))/(np.max(x)-np.min(x)))
# 按A分组后标准化
df.groupby('A').apply(lambda x: (x - np.min(x)) / (np.max(x) - np.min(x)))
# 分组标准化后第一列都是NaN，如果想要加入第一类，可以用concat函数
pd.concat([df['A'],(df.groupby('A').apply(lambda x: (x - np.min(x)) / (np.max(x) - np.min(x))))[['B','C']]],axis=1)
```

# 3. Series结构
1. 可边长的字典，相当于定长有序字典。
2. 由数据和索引组成，类似一维数组的对象。
3. 具有数据对齐功能

## 3.1. 创建的方式
1. `pd.Series([2,4,5,6])`:直接从python中的list来提取
2. `pd.Series(1,index = [1,2,3])`:标量值，index表示了Series类型的尺寸
3. `pd.Series({'a':1,'b':3})`:键是索引，如果定义了index，但是没有在原字典中，则返回NaN
4. `pd.Series(np.arange(6))`:可以通过ndarray类型创建
5. `pd.Series(range(3))`:可以通过range()函数等

## 3.2. 基本操作
```py
a = pd.Series({'a':1,'b':5})
```

1. index:`a.index`:`Index(['a', 'b'], dtype='object')`
2. values:`a.values`:`array([1, 5], dtype=int64)`
3. 自定义索引:`a['a']`
4. 自动索引:`a[0]`
5. 自动索引和自定义索引不可以混合使用
6. 加法:直接会自动对齐不同索引的数据
7. Series可以随时修改并即刻生效

# 4. 计算相关性关系
1. `D.corr()`可以来计算相应数据间的相关性关系。
    + 并且提取出截距、斜率等拟合数据

# 5. pandas处理大型的文件
1. 在训练机器学习模型的过程中，源数据常常不符合我们的要求，所以我们需要对数据进行清洗。

## 5.1. 读取限定列
1. 我们可以在读取csv文件的过程中，我们可以只提取限定列
```py
file = pd.read_csv('demo.csv',usecols = ['column1','column2'])
```

## 5.2. 读取限定行
```py
file = pd.read_csv('demo.csv',nrows=1000,usecols=['column1', 'column2', 'column3'])
```

## 5.3. 分块读取
```py
reader = pd.read_csv('demo.csv',nrows=10000,
                     usecols=['column1','column2','column3'], 
                     chunksize=1000,iterator=True)
reader
```

## 5.4. 获取头和尾部
```py
file = pd.read_csv('demo.csv')
df = pd.DataFrame(file)
df.head() # 默认前10条数据
df.tail() # 默认后10条数据
```

## 5.5. 使用pandas分块处理大文件
1. 读取文件的参数中有两个参数:chunksize,iterator
2. 原理是不一次性把文件数据读入内存，而是分多次

## 5.6. 参考
1. <a href = "https://blog.csdn.net/wld914674505/article/details/81431128">亿级数据清洗</a>
2. <a href = "https://blog.csdn.net/MsSpark/article/details/83154128">Pandas-排序函数sort_values()</a>
3. <a href = "https://blog.csdn.net/jewely/article/details/87869893">DataFrame索引操作、切片选取、条件过滤、赋值</a>
4. <a href = "https://blog.csdn.net/ls13552912394/article/details/79349809">在pandas中遍历DataFrame行</a>
5. <a href = "https://blog.csdn.net/tz_zs/article/details/81137998">【pandas】 之 DataFrame 保存为文件 (df.to_csv、df.to_json、df.to_html、df.to_excel)</a>
6. <a href = "https://blog.csdn.net/li_0891/article/details/81020657">对DataFrame中某一列数据进行修改的方法</a>
7. <a href = "https://blog.csdn.net/Asher117/article/details/86530816">【Python】DataFrame的列标准化</a>
8. <a href = "https://blog.csdn.net/stone0823/article/details/100008619">pandas apply() 函数用法</a>
9. <a href = "https://blog.csdn.net/brucewong0516/article/details/79196902">【python】pandas库Series类型与基本操作详解</a>