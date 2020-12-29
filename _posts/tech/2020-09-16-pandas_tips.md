---

layout: post

title: pandas 使用技巧

category: 技术

tag: pandas技巧

keywork:React

---

## Pandas 使用小技巧

1. 时间处理，将`2017/12/18 13:42:13.000.`转换成python中datetime格式

   ```
   import datetime
   date1 = datetime.datetime.strptime('2017/12/18 13:42:13.000'.replace('.000.',''),'%Y/%m/%d %H:%M:%S')
   date2 = datetime.datetime.strptime('2017/12/18 13:42:14.000'.replace('.000.',''),'%Y/%m/%d %H:%M:%S')
   # 求二者的时间差使用 (秒)
   ss = (date2-date1).seconds
   ```

2. iat函数的使用

   ```
   >>> df = pd.Dataframe([[0, 2, 3], [0, 4, 1], [10, 20, 30]],
           ...                   columns=['A', 'B', 'C'])
   >>> df
               A   B   C
           0   0   2   3
           1   0   4   1
           2  10  20  30
   >>> df.iat[1, 2]
           1
   >>> df.iat[1, 2] = 10
   >>> df.iat[1, 2]
           10
   Get value within a series
   >>> df.loc[0].iat[1]
           2
   ```

3. loc函数：通过索引“Index”中的具体值来取行数据（如取"Index"为"A"的行）

   iloc函数：通过行号来取行数据（如取第二行的数据）

   + 提取行数据

   ```
   data=pd.DataFrame(np.arange(16).reshape(4,4),index=list('abcd'),columns=list('ABCD'))
    
   In[1]: data
   Out[1]: 
       A   B   C   D
   a   0   1   2   3
   b   4   5   6   7
   c   8   9  10  11
   d  12  13  14  15
    
   #取索引为'a'的行
   In[2]: data.loc['a']
   Out[2]:
   A    0
   B    1
   C    2
   D    3
    
   #取第一行数据，索引为'a'的行就是第一行，所以结果相同
   In[3]: data.iloc[0]
   Out[3]:
   A    0
   B    1
   C    2
   D    3
   ```

   + 提取列数据

   ```
   In[4]:data.loc[:,['A']] #取'A'列所有行，多取几列格式为 data.loc[:,['A','B']]
   Out[4]: 
       A
   a   0
   b   4
   c   8
   d  12
    
   In[5]:data.iloc[:,[0]] #取第0列所有行，多取几列格式为 data.iloc[:,[0,1]]
   Out[5]: 
       A
   a   0
   b   4
   c   8
   d  12
   ```

   + 提取指定行、指定列数据

     ```
     In[6]:data.loc[['a','b'],['A','B']] #提取index为'a','b',列名为'A','B'中的数据
     Out[6]: 
        A  B
     a  0  1
     b  4  5
      
     In[7]:data.iloc[[0,1],[0,1]] #提取第0、1行，第0、1列中的数据
     Out[7]: 
        A  B
     a  0  1
     b  4  5
     ```

4. 以上述的例子为例，如果要对某列的值统一进行某个操作，使用lambda函数可以实现：

   ```
   // 对每列的值加1
   add_fun = lambda x:x+1
   df['A'] = df[['A']].apply(add_fun)
   ```

5. pandas画图特别方便，比如说画折线图

   ```
   df['A'].plot()
   // 或者多个特征一起画
   df[['A','B','c']].plot(subplots=True, figsize=(6,4))
   // 或者只想画这些特征的某一部分数据
   df[['A','B','c']][:150].plot(subplots=True, figsize=(6,4))
   ```

6. 删除某一列

   ```
   // 删除列为B的那列
   df = df.drop(column='B')
   ```

7. 用存表格时不保存行名

   ```
   数据如下：
   0   A   B   C   D
   1   0   1   2   3
   2   4   5   6   7
   3   8   9  10  11
   4  12  13  14  15
   df.to_excel("file.xlsx", index=False)
   加了index=False后，数据在xlsx中就没有第一列0-4（不会占用cell）,尽管excel本身会有这样的一个index。
   ```

8. 读表时应该注意的一些点

   ```
   文件如下
   files.xlsx
   0   A   B   C   D
   1   0   1   2   3
   2   4   5   6   7
   3   8   9  10  11
   4  12  13  14  15
   // 直接读取
   df = pd.read_excel("files.xlsx")
   读取后,df与上面的数据一样，即df会把第一行的A,B,C,D当作是列名，数据行从 第二行开始
   // 加参数header
   df = pd.read_excel("files.xlsx"，sheet_name=sheet_name,header=None)
   当文件中原本没有列名的情况下，不适用header=None参数，那么pd会把一行数据当成列名
   所以建议使用如下方法，读取时指定列名
   df = pd.read_excel("files.xlsx"，sheet_name=sheet_name,names=['A','B','C',D],header=None)
   ```

9. 滤波器的使用

   ```
   \\ 低通滤波器
   def low_pass(df_data, cut_hz=4.0, order=3):
       Nyqvist_freq = 250 * 0.5
       b, a = signal.butter(8, cut_hz / Nyqvist_freq, "lowpass")
       lowpass = lambda x: signal.filtfilt(b,a,x)
       for flag in flags:
           df_data[flag]=df_data[[flag]].apply(lowpass)
       return df_data
   \\ 高通滤波器
   def band_pass(df_data, fs=250, low_cut_hz=0.1, high_cut_hz=20, order=3):
       nyq_freq = 0.5 * fs
       low = low_cut_hz / nyq_freq
       high = high_cut_hz / nyq_freq
       b, a = signal.butter(4, [low, high], btype="bandpass")
       bandpass = lambda x: signal.filtfilt(b, a, x)
       for flag in flags:
           df_data[flag]=df_data[[flag]].apply(bandpass)
       return df_data
   ```

10. 5种读取CSV文件的方法；数据集stocks.csv如下：

    | Symbol | Price | Date      | Time   |
    | ------ | ----- | --------- | ------ |
    | A      | 39.48 | 6/11/2007 | 9:36am |
    | AIG    | 71.38 | 6/11/2007 | 9:36am |
    | AXP    | 62.68 | 6/11/2007 | 9:36am |
    | BA     | 98.31 | 6/11/2007 | 9:36am |
    | c      | 53.08 | 6/11/2007 | 9:36am |
    + **简单的读取**

      ```
      import csv
      with open("stocks.csv") as f:
      	f_csv = csv.reader(f)
      	# 获取headers
      	headers = next(f_csv)
      	# 循环获取每一行的内容
      	for row in f_csv:
      		print(row)
      ```

      + 首先读取csv文件，然后用csv.reader生成一个csv迭代器f_csv
      + 然后利用迭代器的特性，next(f_csv)获取csv文件的头，也就是表格数据的头
      + 接着利用for循环，一行一行打印row的内容，也就是表格数据体

    + **用nametuple**

      ```
      from colleections import namedtuple
      
      with open("stocks.csv") as f:
      	f_csv = csv.reader(f)
      	# create nametuple row
      	Row = namedtuple("Row", next(f_csv))
      	
      	# init the row from each row
      	for each_row in f_csv:
      		row_info = Row(*each_row)
      		print(row_info)
      ```

      + nametuple其实是一个非常有用的类，这个类属于collections模块，而这个模块简直就是一个百宝箱里面有非常多的牛逼的库；
      + 这里我们用next(f_csv)其实就是获取表格的头部来初始化这个Row;
      + 然后循环来构造这个Row的数据，**把我们表格里面的每一行的数据都喂成nametuple格式的row_info**；
      + 这样做的好处就是你可以随心所欲的访问这个row_info里面的数，就像访问类数据一样，比如row_info.price

    + **用tuple类型转换**

      ```
      col_types = [str, flaot, str, str, float, int]
      with open("stocks.csv") as f:
      	f_csv = csv.reader(f)
      
      	# get header
      	header = next(f_csv)
      	
      	# convert value
      	for row in f_csv:
      		row = tuple(convert(value) for convert, value in zip(col_types, headers))
      		print(row)
      ```

      操作的步骤其实跟上面差不多，就是对数据结果的清洗处理稍微不一样。**这里非常巧妙的zip来构造一个嵌套的数据列表**，然后用convert(data)把csv文件里面每一行的数据进行转换。

    + **用DictReader**

      ```
      with open("stocks.csv") as f:
      	f_csv = csv.DictReader(f)
      	for row in f_csv:
      		print(row)
      		
      		# 打印row某一个内容
      		print("Price:", row['price'])
      ```

      csv模块直接内置了DictReader(), 按照字典的方法进行读取，然后生成一个有序的字典。在内部的`__next__`其实也是用OrderedDict(zip(self.fieldnames, row))来生成的。

    + **用字典转换**
      如果我们需要对这个csv里面的数据进行清晰，因为读出来的时候都是字符串，我们需要更新为特定的数据类型，

      ```
      field_types = [('Price',float), ('Valume', int)]
      
      with open("stocks.csv") as f:
      	for row in csv.DictReader(f):
      		row.update((key, conversion(row[key])) for key, conversion in field_types)
      ```

      原来的数据价格Price和成交量，读成一个浮点型数据和整型的数据，用一个字典来巧妙的更新key即可。

      + 首先声明一个自定义的类型转换器field_types;
      + 然后循环生成一个可迭代的对象(key, conversion(row[key]));
      + 最后更新一下字典里面相同的key， 比如row['price']的内容就会被更新了。
        P.S 摘抄自[这里](https://zhuanlan.zhihu.com/p/252299357)

