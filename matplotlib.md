# [matplotlib中文文档](https://www.matplotlib.org.cn/) <br>
# [echart](https://www.echartsjs.com/examples/) <br>
# [ploty](https://plot.ly/) <br>

### 什么是matplotlib  

Matplotlib是一个Python 2D绘图库，可以生成各种硬拷贝格式和跨平台交互式环境的出版物质量数据。Matplotlib可用于Python脚本，Python和IPython shell，Jupyter笔记本，Web应用程序服务器和四个图形用户界面工具包。  
实际上在公司里面比较少用到matplotlib，更多的使用其他的js框架，这里学习只是为了更直观的看到数据处理后的结果，重点放在后面的数据处理上😀。

### 折线图demo1 <br>
以折线的上身或下降来表示统计数量增减变化的统计图 <br>
```python
import random

fig = plt.figure(figsize=(20,8),dpi=80)
# 通过实例化一个figure并传递参数 能够使后台自动使用该实例
# dpi -> dots per inch 图像模糊时可以传入dpi

x = range(0, 24, 2)
y = [15, 13, 14.5, 17, 20, 25, 26, 26, 24, 22, 18, 15]
# .randint()随机整数 包括

_xtick_lable = [i/2 for i in range(0, 45)]
plt.xticks(_xtick_lable[::2])
# 设置x的参数 这里回顾一下列表的取值知识和range()函数用法
plt.yticks(range(min(y), max(y)+1))

plt.grid(alpha=0.5)
# 绘制网格 添加透明度
plt.plot(x, y)
# plt.savefig('./kin_01_demo.png')
plt.savefig('./kin_01_demo.svg')
# 保存到本地 .svg 矢量图 放大不会失真
# plt.show()
```
![demo1](.\images\kin_01_demo.svg)
### 折线图demo2
```pyhon
import matplotlib
from matplotlib import pyplot as plt
import random

font = {'family': 'Microsoft YaHei',
        'weight': 'bold'}
matplotlib.rc('font', **font)
# 显示中文

fig = plt.figure(figsize=(20, 8))

y1 = [random.randint(24, 31) for i in range(120)]
y2 = [random.randint(24, 31) for i in range(120)]
x = range(120)
_x = list(x)  # 上面这两句不能合并成一句
_xtick_lable = [f'10点{i}分' for i in range(60)]
_xtick_lable += [f'11点{i}分' for i in range(60)]

plt.xticks(_x[::3], _xtick_lable[::3], rotation=45)
# 上面这两个参数的个数要一样

plt.xlabel('时间')
plt.ylabel('温度')
plt.title('demo')

plt.plot(x, y1,label='y1')
plt.plot(x, y2,label='y2') # 添加图例
plt.legend() # 显示图例
plt.grid() # 显示网格
# plt.show()
plt.savefig('./kin_02_demo.svg')
```
![demo2](.\images\kin_02_demo.svg)
### 散点图demo1
```python
from matplotlib import pyplot as plt
import matplotlib

font = {'family': 'Microsoft YaHei',
        'weight': 'bold'}
matplotlib.rc('font', **font)
fig = plt.figure(figsize=(20, 8))

y_1 = [1,2,4,2,3,1,2,4,3,2,2,3,2,1,3,2,5,1,3,6,3,2,1,8]
y_2 = [9,5,4,1,2,2,5,3,5,3,4,1,1,3,1,5,9,8,4,5,4,1,4,7,10]

x_1 = range(24)
x_2 = range(34,59)

_x = list(x_1)+list(x_2) # 真实长度
_xtick_label = [f'1月{i}日' for i in x_1]
_xtick_label += [f'2月{i}日' for i in x_2]
plt.xticks(_x[::2],_xtick_label[::2],rotation=45)

plt.xlabel('时间')
plt.ylabel('温度')
plt.scatter(x_1,y_1,label='一月') # .scatter()散点图
plt.scatter(x_2,y_2,label='二月')
plt.legend()
plt.show()
```
### 条形图demo1
```python
from matplotlib import pyplot as plt
import matplotlib
font = {'family': 'Microsoft YaHei',
        'weight': 'bold'}
matplotlib.rc('font', **font)
fig = plt.figure(figsize=(20, 8))

x = [1,2,3,4,5,6,7,8,9,10] # 这里本应该是字符串类型 偷个懒
y = [11,12,13,14,15,16,17,18,19,20]

# plt.xticks(range(len(x)),x)
plt.yticks(range(len(x)),x)
# plt.bar(range(len(x)),y,width=0.3,color='SkyBlue', label='text') # 垂直条形图
plt.barh(range(len(x)),y,height=0.8,color='SkyBlue', label='text')
plt.grid(alpha=0.5)
plt.show()
```
### 条形图demo2
```python
from matplotlib import pyplot as plt
import matplotlib
font = {'family': 'Microsoft YaHei',
        'weight': 'bold'}
matplotlib.rc('font', **font)
fig = plt.figure(figsize=(20, 8))

x = ['垃圾电影1', '垃圾电影2','垃圾电影3','垃圾电影4']
y1 = [10,12,13,16]
y2 = [11,15,14,15]
y3 = [9,10,11,15]

x_1 = list(range(len(x)))
x_2 = [i+0.2 for i in x_1]
x_3 = [i+0.2*2 for i in x_1]

plt.bar(x_1,y1, width=0.2, label='第一天')
plt.bar(x_2,y2, width=0.2, label='第二天')
plt.bar(x_3,y3, width=0.2, label='第三天')
plt.legend()
plt.xticks(x_2,x)
plt.show()
```
### 直方图demo1
```python
from matplotlib import pyplot as plt
import matplotlib
font = {'family': 'Microsoft YaHei',
        'weight': 'bold'}
matplotlib.rc('font', **font)
fig = plt.figure(figsize=(20, 8))

y = [19,31,12,12,63,21,32,55,32,41,57,12,65,32,99,21,2,93,88,97,1,64,21,84,7,74,56,62,81,83,49,42,32,16,12,35,10,94,84,72,12,32,45,63,15,63,46,32,87,96,14,32,54,52,63,48,21,26,49,35,28,47,39,84,64,19]

# d = 5 # 组距 相当于步长
# num_bins = (max(y)-min(y))//d #组数
# 如果不能整除 这种方法就不对了

# plt.hist(y,10) # 频数分布直方图
plt.hist(y,10,density=True) # 频率分布直方图
plt.xticks(range(min(y),max(y)+10)[::10])
# plt.grid()
plt.show()
```
## 😥 <br>
学到这里，然后发现并没有有什么用，有更强大实用的画图框架=。=<br>