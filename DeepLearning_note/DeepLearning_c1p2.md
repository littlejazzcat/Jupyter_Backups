## 《动手学深度学习》第一章第二节

<i>---------------数据预处理</i>


- 路径问题

在python中'\'表示转义字符，在使用路径时需要先使用转义或者在路径前加'r'（特别注意，
直接复制下来的文件路径一般都是用'\'的），也可以将'\'替换成'/'。例：

<code>
'C:\Users' #直接复制下来的路径
#---------------------------
'C:\\Users' 
'C:/Users'
r'C:\Users'

</code>

- 文件读取与写入

使用到的库：<code>import os #python自带库</code>

- 常用方法

```python

os.getcwd()  #获取当前文件所在路径，注意这里获取到的路径也是用'\'做分隔符的

os.chdir(path)  #更改当前路径，之后默认路径为更改后的路径，再使用os.getcwd()
                #获取到的也是更改后的路径
os.path.join(path,*paths) #拼接路径
    #示例
    import os
    p1 = r'D:\DeepLearning\test\pandas'
    p2 = r'join'
    p3 = r'test\text.txt'
    print(os.path.join(p1,p2,p3))
    #ans：D:\DeepLearning\test\pandas\join\test\text.txt

os.path.exists(path)  #判断路径是否存在，返回结果为True/False

os.mkdir(path,mode=511,*,dir_fd=None) #创建文件夹，windows下mode参数忽略
                                      #注意只能创建一层
    #示例：假设要创建文件夹的路径为'D:\DeepLearning\test',文件夹名称为'test_mkdir'
    os.makedir(os.path.join(r'D:\DeepLearning\test', 'test_mkdir'))
    #也可以不使用os.path.join()方法而直接使用完整的路径：
    os.makedir(r'D:\DeepLearning\test\test_mkdir')

os.mkdirs(path, mode=511, exist_ok=True) #创建路径，可以创建多层
                                         #exit_ok参数赋值True时，在创建路径时可以自
                                         #动判断当文件夹已经存在就不创建
                                         #使用方式同os.mkdir()

```

- 处理数据缺失值，使用到的库：os,pandas

```python

import os

os.makedirs(os.path.join('..', 'data'), exist_ok=True)
data_file = os.path.join('..', 'data', 'house_tiny.csv')
with open(data_file, 'w') as f:
    f.write('NumRooms,Alley,Price\n')  # 列名
    f.write('NA,Pave,127500\n')  # 每行表示一个数据样本
    f.write('2,NA,106000\n')
    f.write('4,NA,178100\n')
    f.write('NA,NA,140000\n')

#使用pandas库的read_csv()方法读取创建的csv文件内的数据集
import pandas as pd

data = pd.read_csv(data_file)#这里data不是python原生数据类型
                             #而是pandas.core.frame.DataFrame
                             #注意切片或引用等操作要使用pandas库中的方法iloc[i,j]

#处理缺失值
inputs, outputs = data.iloc[:, 0:2], data.iloc[:, 2]
inputs = inputs.fillna(inputs.mean()) # fillna()方法用于填充数据集中的NAN项
                                      # mean()方法为求均值

inputs = pd.get_dummies(inputs, dummy_na=True)#将空缺值转换成单独的状态
print(inputs)

#ans：
   NumRooms  Alley_Pave  Alley_nan
0       3.0           1          0
1       2.0           0          1
2       4.0           0          1
3       3.0           0          1

```
- pandas库中的<code>get_dummies()</code>方法参数说明:
  
|参数名|参数值|作用|  
|:---:|:---:|:---:|
|data | array-like, Series, or DataFrame | 输入数据 |
|prefix | string, list of strings, or dict of strings, default None | 给输出的列添加前缀，如prefix=“A”,输出的列会显示类似 |
|prefix_sep | str, default ‘_’ | 设置前缀跟分类的分隔符sepration，默认是下划线"_" |
|dummy_na | bool, default False | 增加一列表示空缺值，如果False就忽略空缺值 |
|columns | list-like, default None | 指定需要实现类别转换的列名 |
|sparse | bool, default False | 哑编码列是否应该支持SparseArray (True)或常规NumPy数组(False)。|
|drop_first | bool, default False | 获得k中的k-1个类别值，去除第一个 |
|dtype | dtype, default np.uint8 | 新列的数据类型，只允许一种类型的dtype |

- 将数据转换成张量

```python
import torch

X = torch.tensor(inputs.to_numpy(dtype=float))
y = torch.tensor(outputs.to_numpy(dtype=float))
X, y

#ans
(tensor([[3., 1., 0.],
         [2., 0., 1.],
         [4., 0., 1.],
         [3., 0., 1.]], dtype=torch.float64),
 tensor([127500., 106000., 178100., 140000.], dtype=torch.float64))
```