---

layout: post

title: pandas 使用技巧

category: pandas技巧

tag: React

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

   

