+++
title = "python数据可视化"
date = 2021-08-15 14:43:14
slug = "202108151443"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++

常用可视化图表速查

<!-- more -->

## 基础图形

https://seaborn.pydata.org/

seaborn中提供了与matplotlib类似的接口，其他高级作图函数都以这些底层作图函数为基础，进行封装，通常作为kind参数。各种基本图形既可以直接传入数组形式的变量数据，也可以传入DataFrame列名并传入data参数。

```python
import seaborn as sns
# 折线图
sns.lineplot()
# 条形图
sns.barplot()
# 计数条形图
sns.countplot()
# 散点图
sns.scatterplot()
# 分类散点图
sns.stripplot()
# 分簇散点图
sns.swarmplot() # 与stipplot()的区别就是点不重叠
# 箱型图
sns.boxplot()
# 增强箱型图
sns.boxenplot() # 适合大数据集，显示更多分位数
# 小提琴图
sns.violinplot()
# 点图
sns.pointplot() # 纵轴是均值，置信区间用标准差表示
# 核密度估计图
sns.kdeplot(x,bw=2.0, shade=True) #bw为带宽
# 地毯图
sns.rugplot(x) # 直接将数据标记在坐标轴上
# 回归线图
sns.regplot() # 散点图附加回归线
# 热图
sns.heatmap(annot=True) # annot表示显示数值
# 另外补充几种常用图
# 饼图
#设置字体样式
mpl.rcParams['font.family']='sans-serif'
mpl.rcParams['font.sans-serif']=[u'SimHei']
title = '饼图'
fig = plt.figure(title, figsize=(8, 7))
fig.tight_layout()
ax = fig.add_subplot(111)
data = [3,4,5,7,3]
labels = ['a','b','c','d','e']
explodes =[0 for x in data]
explodes[0] =0.015
ax.pie(data, labels= labels, radius=0.8, #data 是数据，labels 是标签，radius 是饼图半径
       explode=explodes, #explodes 为0 代表不偏离圆心， 不为零则代表偏离圆心的距离
       autopct='%1.1f%%', #显示所占比例，百分数
       pctdistance = 0.5,
       labeldistance=0.7,  # a,b,c,d 到圆心的距离
       textprops={'fontsize': 20, 'color': 'black'}) # 标签和比例的格式
plt.axis('equal') # 正圆
plt.legend( loc = 'upper right',bbox_to_anchor=(1.1, 1.05), fontsize=14, borderaxespad=0.3)
# loc =  'upper right' 位于右上角
# bbox_to_anchor=[0.5, 0.5] # 外边距 上边 右边
# ncol=2 分两列
# borderaxespad = 0.3图例的内边距
plt.suptitle(title+'pie', fontsize=20)
#plt.savefig(filepath+'\name.png',dpi=120,bbox_inches='tight') #可通过这个方法保存可视化的图片
# plt.show()
# plt.close()
plt.savefig('bingtu.png',dpi=120,bbox_inches='tight')
# 极坐标图
ax = plt.subplot(111,projection='polar') # projection指投影到极坐标
ax.plot(x,y) # x为角度(弧度制),y为径长
# 平行坐标图
from pandas.plotting import parallel_coordinates
parallel_coordinates(data, 'key')
```



## 高级函数

针对数据可视化的不同目的，seaborn提供了relplot()，catplot()，displot()，lmplot()四大主要高级函数。
seaborn与pandas的DataFrame的结合非常好，因此传参是可以直接传入列名。
返回的是FacetGrid(平面网格图)对象。
这些高级函数的主要参数如下：
x，y：输入变量
data：输入数据df
hue：分组变量
style：如传入，则hue分组后每组数据作图风格可不一致
col，row：决定平面网格图布局的变量
kind：底层作图类型名称，如"line",“bar”,"box"等
注：由于返回的平面网格图子图间距可能不合理，可调用plt.tight_layout()自动调整。

### 可视化变量关系(relationship)

relplot()方便观察变量间的关系。

sns.relplot() # 默认是散点图

### 分类数据的分布图(categorical)

catplot()可以很好观察分类数据的分布情况。

sns.catplot()

### 数据集分布图(distribution)

特征工程前，需要对数据集的有一个整体的了解，seabon提供的高级函数displot()可以作各种分布图。

#### 单变量分布

displot是hist([直方图](https://so.csdn.net/so/search?q=直方图&spm=1001.2101.3001.7020))、rugplot(地毯图)、kdeplot(核密度估计图)的高级封装。

```python
from scipy import stats
sns.displot(x, kde=False,rug=False, fit=stats.gamma) # 直方图,若传入fit参数，则表示拟合相应的分布
```

#### 双变量分布

```python
sns.jointplot(kind='scatter')
# scatter表示散点图，hex表示HexBin图，kde表示核密度估计图，reg表示添加回归线的散点图，resid表示残差图
# 定制图
g = sns.JointGrid()
g.plot_joint(plt.scatter)
g.plot_marginal(sns.displot)
g.annotate(stats.pearsonr)
```

#### 成对变量分布

```python
sns.pairplot(data)
# 定制图
g = sns.PairGrid(data)
g.map_diag(sns.kdeplot)
g.map_offdiag(sns.kdeplot)
```

### 回归线图(linear model)

regplot()以及lmplot()都可以作回归线图，二者的区别是lmplot的参数data是必传的，而regplot不是。因此可以将regplot()视为底层函数。

```python
sns.lmplot('total_bill','tip',data=tips)
sns.residplot() # 残差图
"""
lmplot重要参数解释：
ci:置信度
order: 拟合阶数
x_jitter: 施加噪声
robust: 鲁棒性
logistic: 逻辑斯谛回归
hue：分组回归
scatter_kws={"s":80}: 散点图参数(例如点的大小)
"""
```
