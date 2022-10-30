+++
title = "pandas经验总结"
date = 2022-01-05 14:43:14
slug = "202201051443"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++

<!-- more -->

# pandas显示设置

## 控制台打印完整数据

```python
#显示更多行
pd.set_option('display.max_columns', 100) #显示100行
#显示更多行
pd.set_option('display.max_rows', 100) #显示100列
#改变列宽,设置显示长度为100，默认为50
pd.set_option('display.max_colwidth',100)
# 设置换行，True可以换行显示。False不允许换行
pd.set_option('display.expand_frame_repr', False) 
# 重置
pd.reset_option('display.max_rows')
# 全部重置
pd.reset_option('all')
```

### Pycharm打开（打印）大数据文件显示不全的解决方法

Pycharm IDEA对能关联的文件大小做了限制，默认值为2500kb

#### 修改方法：

Pycharm菜单找到`Edit Custom Properties`

追加字段：

```
idea.max.intellisense.filesize = 20000
idea.max.content.load.filesize = 20000
idea.cycle.buffer.size = 20000
```

其中20000代表限制大小为20000kb

重启Pycharm：

Pycharm菜单File——>Invalidate caches / ...

## 数字格式化显示

#### 设置float列的精度

`pandas`默认情况下float只显示小数点后6位

```python
pd.set_option( 'display.precision',4) # 设置显示小数点后4位
```

这个设置不影响底层数据，它只影响float列的显示。

#### 数字格式化显示

`pandas`中有一个选项`display.float_formatoption`可以用来格式化任何浮点列。

这个设置项仅适用于浮点列，对于其他数据类型，必须将它们转换为浮点数才可以。

**设置数字精度**

```python
pd.set_option('display.float_format',  '{:,.2f}'.format) # 精确到小数点后2位
```

**百分号格式化**

```python
pd.set_option('display.float_format', '{:.2f}%'.format) # 显示带百分比的列
```

**逗号格式化大值数字**

```python
pd.set_option('display.float_format','{:,}'.format) #例如100,000
```



## 更改绘图方法

`pandas`默认使用`matplotlib`作为绘图后端。

**激活backend**

例如使用plotly：

```python
pd.options.plotting.backend = 'plotly'
```

或

```python
pd.set_option('plotting.backend', 'plotly')
```

## 配置info()的输出

设置要分析的最大列数，默认为100。

```python
pd.set_option('display.max_info_columns', 200) 
```

设置计数null时的阈值，默认为1690785。设置一个较小的值可以避免在测试的时候计算所有`null`导致速度很慢

```python
pd.set_option('display.max_info_rows', 5) # 只在行数不超过5时才计数null
```

## 显示设置和重置所有设置

```python
pd.describe_option()  #展示所有设置和描述
pd.reset_option('all')  #重置所有设置选项
```

# 报错处理

## SettingWithCopyWarning:  A value is trying to be set on a copy of a slice from a DataFrame

在向DataFrame中插入新行时，Pandas报出了这个警告。虽然不影响程序执行，但还是不想看到警告（X

事故现场：

```python
add = {'ts_code': stock_code, 'trade_date': now_date, 'close': now_price}
df.loc[len(df)] = add
```

解决方法：

直接对副本修改就会出现这个警告，需要在修改前应该先对副本复制一下

```python
add = {'ts_code': stock_code, 'trade_date': now_date, 'close': now_price}
df = df.copy()
df.loc[len(df)] = add
```

