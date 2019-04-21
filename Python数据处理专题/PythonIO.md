# IO

[TOC]



# 其他

```python
!type f:\test\News\DataAnalyst.csv # 查看文件内容、格式
!type f:\test\demo.json
!dir # 查看目录下，文件名称
list(open('demo.csv')) # 打开查看文件
```





# pd.read_table()

```python
pd.read_table('文件路径/demo.txt',
              sep='\s+', # 指明分隔符为 多个空行
              delimiter=None,
              header='infer',#读取哪一行为columns
              names=None,#字段名
              index_col=0,  # 读取某列为index
              parse_dates=False,#哪个字段读取为时间类型
              date_parser=None,#日期解析器，即format
  
```

***

# pd.read_csv()

## 查看文件布局

```python
list(open('demo.csv'))
```

## 读取

```python
pd.read_csv('f:/test/demo.csv',
            index_col=0, # 读取某列为index
            index_col=['col1','col2'], # 读取多重索引
            encoding='gbk', # 编码格式
            header=None, # 用作列名的行
            names=列表, # 自定义列名
            dtype={'col1':int,'col2':np.float64}, # 设置列的数据类型
            usecols=[1,2,3], # 只读取某几列['col1,'col2']
            skiprows=[0,2], #跳过 1、3行，skiprows=3:跳过前3行
            
            skipfooter=2, # 跳过最后2行,
            engine='python', # 指明skip_footer实现的方式
            
            nrows=5， # 只读5行数据（不包含标题）
            
            na_values=['NULL',999,'np.nan'], # 指定这些值读取为缺失值
            na_values={'col1':['NULL','demo'],'col3':['a','c']}, # 字典指定列中特定值为缺失值 
            
            parse_dates=False, # 尝试解析为日期
            #【True】：解析所有列
            #【列表或元组】
            
            delimiter=',', # 用于分隔字段的单字符字符串，默认为','
            skipinitialspace=False, # 忽略分隔符后面的空白符
            
            
           )
```

### 参数

| 参数              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| parse_dates       | 解析为日期，默认False。<br />True：尝试解析所有列<br />一组列名、列号：解析指定列<br />列表的元素为列表或元组：将多格列组合到一起再进行日期解析 |
| converters        | {'col1':f}：对col1列的所有值应用函数f                        |
| dayfirst          | 解析有歧义日期时，看做国际格式，默认False<br />23/6/2018→June，23，2018 |
| `engine='python'` | 解析方式<br />分析引擎<br />C或python<br />C引擎快<br />python引擎：更能完备 |

### 逐块读取

```python
chunker = pd.read_csv('demo.csv',chunksize=1000)

tot = pd.Series([])
for piece in tot:
    tot = tot.add(piece['key'].value_counts(),fill_value=0)

tot = tot.sort_values(ascending=False)
```

## 输出文件

```python
df.to_csv('f:/test/demo.csv',
          index_label='demo', # 设置保存时index的name
          index = False， # 不保存索引
          header = False, # 不保存列名
          na_rep = 'NULL', # 指定缺失值，用NULL标记
          columns = ['col5','col3','col1'], # 只保存部分列，保存列的顺序与指定列表一致
         )
```

### 指定分隔符输出

```python
df.to_csv('demo',
          sep = '|', # 指定分隔符
         )
```

## 手工处理

### 手工读取

```python
with open('demo.csv') as f:
```



### 手工输出分隔符文件

- 

```python
with open('demo.csv','w') as f:
    writer = csv.writer(f, dialect = my_dialect)
    writer.writerow(('one','two','three'))
    writer.writerow(('1','2','3'))
    writer.writerow(('4','5','6'))
    writer.writerow(('7','8','9'))
```





***

# pd.read_excel()

## 读取表

```python
pd.read_excel('f:/test/News/demo.xlsx',
              sheetname='表名', # 读取哪张表
              
             )
```

## 写入表

```python
df.to_excel('f:/test/demo.xlsx',
            sheet_name='demo1', # 设置保存的表名
            index=False, # 不保存index         
           )
```

```python
writer = pd.ExcelWriter('demo.xlsx') # 创建一个demo.xlsx文件

df1.to_excel(writer,'Sheet1') # df文件写入demo.xlsx表，命名为表1

writer.save()
```



## 写入多张表

* 推荐：

```python
with pd.ExcelWriter('f:/test/demo.xlsx') as writer:
    df1.to_excel(writer,sheet_name='demo1')
    df2.to_excel(writer,sheet_name='demo2')
```

***

# pd.read_json()

* JSON(JavaScript Object Notation)

* **json字符串**

  ```python
  obj = """
  {"name": "Wes",
   "places_lived": ["United States", "Spain", "Germany"],
   "pet": null,
   "siblings": [{"name": "Scott", "age": 30, "pets": ["Zeus", "Zuko"]},
                {"name": "Katie", "age": 38,
                 "pets": ["Sixes", "Stache", "Cisco"]}]
  }
  """
  ```

## JSON字符串转换成Python形式

```python
import json

result = json.loads(obj) # obj为json字符串
```

## Python对象（不一定是DataFrame、Series）转换成JSON格式

```python
asjson = json.dumps(result)
```

## JSON格式转换成DataFrame

* 向DataFrame构造器传入一个字典的列表（就是原先的JSON对象），并选取数据字段的子集

```python
df = pd.DataFrame(result['siblings'], # siblings JSON对象
                  columns=['name','age']) # ['name','age]选取字段的子集
```

## 特定格式的JSON数据集

* 转换成Series或DataFrame

```python
pd.read_json('demo.json') # JSON数组中的每个对象是表格中的一行
```



## pands输出json

```python
df.to_json()
```



***

# sqlite

* 写入数据库
* sqlitebrowser.org查看数据库

```python
import sqlite3
connection = sqlite3.connect('f:/test/demo.sqlite')
df.to_sql('demo_DATA', #自己命名数据库名，会写入demo.sqlite
          connection,
          if_existe='replace'
          # 如果数据库存在，则替换（if_exists='append,存在则添加在后）
          )
connection.commit()
connection.close()
```

* 读取数据库

```python
connection = sqlite3.connect('f:/test/demo.sqlite')
dfs = pd.io.sql.read_sql('select * from demo_DATA',
                         connection, #读取这个数据库
                         index_col='index'
                        )
connection.close()
dfs.head()
```

## read_sql

```python
import sqlalchemy as sqla

db = sqla.create_engine('sqlite:///mydata.sqlite') # 用SQLAlchemy连接SQLite数据库

pd.read_sql('select * from table1',db)
```



***

# pandas解析hdf5

* HDF5是一种存储大规模科学数组数据的非常好的文件格式
  * 可以被作为C库，带有许多语言的接口，如Java、Python和MATLAB等。
  * HDF5中的HDF指的是层次型数据格式（hierarchical data format）。
  * 每个HDF5文件都含有一个文件系统式的节点结构，它使你能够存储多个数据集并支持元数据。
  * 与其他简单格式相比，HDF5支持多种压缩器的即时压缩，还能更高效地存储重复模式数据。
  * 对于那些非常大的无法直接放入内存的数据集，HDF5就是不错的选择，因为它可以高效地分块读写。
* 保存为hdf5

```python
df = pd.DataFrame({'a':np.random.randn(10,3)},
 		         index=pd.date_range('20181111',periods=10),
                  columns=['a','c','b'])
hdf =  pd.HDFStore('f:/test/demo.h5')
hdf['obj1'] = df
hdf['obj1_col'] = df['a']
```

* 读取

```python
read_hdf = pd.HDFStore('f:/test/demo.h5')
df = read_hdf['df']
df
```

```python
pd.HDFStore('f:/test/hdf.h5')['df']
```

## HDFStore存储模式

* table存储模式

  * 通常会更慢
  * 支持使用特殊语法进行查询操作

  ```python
  hdf.put('obj2',df,format)
  hdf.select('obj2',where = ['index == 20181111'])
  ```

* ```python
  df.to_hdf('demo.h5','obj3',format = 'table')
  pd.read_hdf('demo.h5','obj3',where = ['index = 20181111'])
  ```

* 

***

# web

## 读取远程文件

```python
url = 'http://archive.ics.uci.edu/m1/machine-learning-databases/pima-indians-diabetes/pima-indians-diabetes.data'
names = ['preg','plas','pres','skin','test','mass','pedi','age','class']
data = pd.read_csv(url, names = names)
data.head()
```

## pandas读取HTML

```python
url = 'http://www.worldcup.2014.163.com/index.html'
df = pd.read_html(url)
len(df)
df[0].head()


table = []
for i in range(1,7):
    table.append(pd.read_html('https://nba.hupu.com/stats/players/pts/%d' %i)[0])

players = pd.concat(table)
columns=['排名','球员','球队','得分','命中-出手','命中率','命中-三分','三分命中率','命中-罚球','罚球命中率','场次','上场时间']
players.columns=columns
```

# pandas_datareader财经数据

* 搜索pandas_datareader，查看官网

```python
import pandas_datareader.data as web
import datetime
start = datetime.datetime(2018,11,1)
end = datetime.datetime(2018,11,11)
apple = web.DataReader('AAPL','yahoo',start,end)
apple.head()
```

## 解析XML

* XML(Extensible Markup Language)
  * 一种常见的支持分层、嵌套数据以及元数据的结构化数据格式。

```python

```



***

# Web APIs交互

# 读取GitHub的pandas主题api

```python
import requests
url = 'https://api.github.com/repos/pandas-dev/pandas/issues'
resp = requests.get(url) # 会返回一个包含被解析过的JSON字典

data = resp.json() # data中的每个元素都是一个包含所有GitHub主题页数据（不包含评论）的字典。

```

### 返回DataFrame对象

```python
issues = pd.DataFrame(data,columns=['number','title','labels','state'])

```



***

# 股票数据

## tushare.org

```python
import tushare  as ts
print(tushare.__version__) # 查看包的版本
ts.get_h_data('002241',start='2018-11-01',end='2018-11-11')
```

* 读取 新股

```python
ts.new_stocks()
```

***

# Pandas链接mysql

* 先链接，读取

```mysql
import pandas as pd
from sqlalchemy import create_engine
```

```mysql
def reader(query,db = 'learn'):
    sql = query
    engine = create_engine('mysql+pymysql://root:admin888@127.0.0.1:3306/{0}?charset=utf8'.format(db))
    df = pd.read_sql(sql,engine,)
    return df
```

```mysql
df_company = reader('select * from company_sql')
```

```mysql
reader('show tables')

写入mysql

​```python
df.to_sql('new_table_name',con=engine,index=True)
​```

- 优点
  - 新表不用创建字段
```

***

# pymysql链接MySQL数据库

- 安装pymysql

```打开cmd，pip install pymysql```

1. 链接MySQL

```mysql
import pymysql
# 加载pymysql
```

1. 创建链接conn

```mysql
conn = pymysql.connect(
	host = '127.0.0.1',
    user = 'root',
    password = 'admin888',
    db = 'learn',
    port = 3306,
    charset = 'utf8'
)
# 定义变量链接conn
# 链接数据库
```

1. 创建游标

```mysql
cur = conn.cursor()
# 创建游标
```

1. 获得结果

```mysql
cur.execute(“select * from company_sql”)
# sql语句，获得结果，此时结果还放在内存中
```

1. 调出赋值

```mysql
data = cur.fetchall()
# 调取结果赋值给变量data
```

1. 循环

```mysql
for d in data:
	print (d[0],d[1],d[2])
# for循环，切片
```

```mysql
conn.commit()
# 提交修改，一般只是读取的话，用不到该语句
```

```mysql
cur.close()# 关闭游标
conn.close()# 关闭链接
```



***

- `conda install pandas_datareader`

- `import pandas_datareader as pdr`

- 获取雅虎股票信息

  ```python
  start=datetime.datetime(2015,9,20)
  alibaba = pdr.get_data_yahoo('BABA',start = start)
  amazon = pdr.get_data_yahoo('AMZN',start = start)
  ```

  - 股价查询编号

    ```python
    'AAPL' # 苹果
    'GOOG' # 谷歌
    'MSFT' # 微软
    'AMZN' # 亚马逊
    'FB' # 脸书
    'BABA' # 阿里巴巴
    'VIPS' # 唯品会
    ```

# API

- 豆瓣API

  - 返回值为json格式
  - `https://api.douban.com/v2/book/1220562`，获取ID为1220562的书的信息
  - `https://api.douban.com/v2/movie/subject/26387639`获取ID为26387639（摔跤吧爸爸）电影的信息

- 爬取

  ```python
  import urllib.request as urlrequest
  import json
  
  # 抓取api的json文件
  id_list = []
  for id in id_list:
      url_visit = 'https://api.douba.com/v2/movie/subject/{}'.format(id)
      crawl_content = urlrequest.urlopen(url_visit).read()
      # 加载爬取的文件
      json_content = json.loads(crawl_content.decode('utf8'))
      rank = json_content['rating']['average']
      # 将爬取的内容写入文件
      with open('movie_score.txt','a')as output:
          output.write('{},{}\n'.format(id,rank))
  ```

