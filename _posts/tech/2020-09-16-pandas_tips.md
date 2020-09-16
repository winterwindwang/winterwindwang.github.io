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

4. 