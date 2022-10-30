+++
title = "python绘制candlestick"
date = 2022-01-15 16:42:14
slug = "202201151642"

[taxonomies]
tags = ["Quant", "Python" ]
categories = ["Quant"]

+++

<!-- more -->

## 数据获取

### tushare获取行情数据

tushare获取历史行情数据的函数主要有三个：

get_hist_data()： 获取近3年的日线数据

get_h_data()：获取一年的日线数据

get_k_data()：返回每一只股票从上市开始到当前交易日的所有日线数据

### tushare获取交易日历

trade_cal()：获取各大交易所交易日历数据,默认提取的是上交所

### TA-Lib计算均线

10日均线

```python
sma_10 = talib.SMA(np.array(data['close']), 10)
```

同理计算30日均线



## Matplotlib画图

官方文档：[finance — Matplotlib 2.1.2 documentation](https://matplotlib.org/2.1.2/api/finance_api.html)

### matplotlib.finance踩坑

#### AttributeError: module 'mplfinance' has no attribute 'candlestick_ochl'

原因：从matplotlib 2.2.0开始，matplotlib已经不包含finance模块；而mpl_finance模块从2020年开始弃用。

解决：

```
conda install -c conda-forge mplfinance
```

或者

```
pip install --upgrade mplfinance
```



#### ImportError: cannot import name 'candlestick_ochl' from 'mplfinance'

原因：在新的mplfinance包下调用旧的API

解决：将

```
from mpl_finance import
```

改为：

```
from mplfinance.original_flavor import candlestick_ohlc
```



### candlestick函数绘制蜡烛图

matplotlib.finance共有四个画蜡烛图的函数

```python
matplotlib.finance.candlestick_ochl(ax, quotes, width=0.2, colorup='k', colordown='r', alpha=1.0)  
matplotlib.finance.candlestick_ohlc(ax, quotes, width=0.2, colorup='k', colordown='r', alpha=1.0)
matplotlib.finance.candlestick2_ochl(ax, opens, closes, highs, lows, width=4, colorup='k', colordown='r', alpha=0.75)  
matplotlib.finance.candlestick2_ohlc(ax, opens, highs, lows, closes, width=4, colorup='k', colordown='r', alpha=0.75) 
```



### 处理日线图的时间间隔

直接绘图会出现非交易日无法跳过导致图像断裂的情况，一般的方法都是建议重新调整横坐标，被动地过滤掉非交易时间段。例如每 10 个交易日标记一次。

```python
ax.set_xticks(range(0, len(data['date']), 10))
ax.set_xticklabels(data['date'][::10])
```

函数说明：

#### matplotlib.axes.Axes.set_xticks()

用于使用刻度列表设置 x 刻度。

[Matplotlib.axes.Axes.set_xticks() in Python - GeeksforGeeks](https://www.geeksforgeeks.org/matplotlib-axes-axes-set_xticks-in-python/#:~:text=The Axes.set_xticks () function in axes module of,set major ticks or to set minor ticks.)

**Syntax:** Axes.set_xticks(self, ticks, minor=False)

**Parameters:** This method accepts the following parameters.

- **ticks :** This parameter is the list of x-axis tick locations.
- **minor :** This parameter is used whether set major ticks or to set minor ticks

**Return value:** This method does not returns any value.

#### matplotlib.axes.Axes.set_xticklabels()

用于使用字符串标签列表设置 x-tick 标签，可以赋值给之前已经设置过的set_xtick。

[Matplotlib.axes.Axes.set_xticklabels() in Python - GeeksforGeeks](https://www.geeksforgeeks.org/matplotlib-axes-axes-set_xticklabels-in-python/)

**Syntax:** Axes.set_xticklabels(self, labels, fontdict=None, minor=False, **kwargs)

**Parameters:** 

- **labels :** This parameter is the list of of string labels.
- **fontdict :** This parameter is the dictionary controlling the appearance of the ticklabels.
- **minor :** This parameter is used whether set major ticks or to set minor ticks

**Return value:** This method returns a list of Text instances.



### volume_overlay函数绘制成交量

```python
matplotlib.finance.volume_overlay(ax, opens, closes, volumes, colorup='k', colordown='r', width=4, alpha=1.0)
```



### 两张图对轴

调整坐标轴，使两张图的间距一致

```python
ax = fig.add_axes([0,0.2,1,0.5])
ax2 = fig.add_axes([0,0,1,0.2])
```



### 代码

```python
import talib
import numpy as np
import tushare as ts
import matplotlib.pyplot as plt
from mplfinance.original_flavor import candlestick2_ochl, volume_overlay


if __name__ == '__main__':
    data = ts.get_k_data('399300', index=True, start='2021-01-01', end='2021-06-31')
    sma_10 = talib.SMA(np.array(data['close']), 10)
    sma_30 = talib.SMA(np.array(data['close']), 30)

    fig = plt.figure(figsize=(17, 10))
    ax = fig.add_axes([0, 0.2, 1, 0.5])
    ax2 = fig.add_axes([0, 0, 1, 0.2])

    candlestick2_ochl(ax, data['open'], data['close'], data['high'], data['low'],
                          width=0.5, colorup='r', colordown='g', alpha=0.6)
    ax.set_xticks(range(0, len(data['date']), 10))
    ax.plot(sma_10, label='10 日均线')
    ax.plot(sma_30, label='30 日均线')
    ax.legend(loc='upper left')
    ax.grid(True)

    volume_overlay(ax2, data['open'], data['close'], data['volume'], colorup='r', colordown='g', width=0.5,
                       alpha=0.8)
    ax2.set_xticks(range(0, len(data['date']), 10))
    ax2.set_xticklabels(data['date'][::10], rotation=30)
    ax2.grid(True)

    plt.show()



```



## plotly画图

官方文档：[plotly.graph_objects.Figure — 5.5.0 documentation](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html)

### Candlestick绘制蜡烛图

```python
go.Candlestick(x=df['date'], open=df['open'], high=df['high'], low=df['low'],
close=df['close'])
```

### 添加自定义文本和批注

```python
fig.update_layout(
    title='The Great Recession',
    yaxis_title='AAPL Stock',
    shapes = [dict(
        x0='2021-01-09', x1='2021-01-09', y0=0, y1=1, xref='x', yref='paper',
        line_width=2)],
    annotations=[dict(
        x='2021-01-09', y=0.05, xref='x', yref='paper',
        showarrow=False, xanchor='left', text='Increase Period Begins')]
)
```

### 代码

```python
import plotly.graph_objects as go
import tushare as ts

df = ts.get_k_data('399300', index=True, start='2021-01-01', end='2021-06-31')
fig = go.Figure(data=[go.Candlestick(x=df['date'],
                                     open=df['open'],
                                     high=df['high'],
                                     low=df['low'],
                                     close=df['close'])])

# Candlestick without Rangeslider
# fig.update_layout(xaxis_rangeslider_visible=False)

# Adding Customized Text and Annotations
fig.update_layout(
    title='The Great Recession',
    yaxis_title='AAPL Stock',
    shapes = [dict(
        x0='2021-01-09', x1='2021-01-09', y0=0, y1=1, xref='x', yref='paper',
        line_width=2)],
    annotations=[dict(
        x='2021-01-09', y=0.05, xref='x', yref='paper',
        showarrow=False, xanchor='left', text='Increase Period Begins')]
)
fig.show()
```

## plotly嵌入PyQt5

plotly的绘图对象是浏览器，可以用pyqt的QtWebEngineWidgets控件作为ployly的绘图对象，实现将plotly嵌入pyqt中。

```python
import os
import sys
import tempfile

from PyQt5.QtWidgets import QPushButton, QLineEdit, QLabel
from plotly.io import to_html
import plotly.graph_objs as go
from PyQt5 import QtCore, QtGui, QtWidgets, QtWebEngineWidgets
import plotly.graph_objects as go
import tushare as ts


class PlotlyViewer(QtWebEngineWidgets.QWebEngineView):
    now_Id = '399300'
    now_start_time = '2021-01-01'
    now_end_time = '2021-06-01'

    def __init__(self, fig=None):
        super().__init__()
        self.page().profile().downloadRequested.connect(self.on_downloadRequested)
        self.settings().setAttribute(self.settings().ShowScrollBars, False)
        self.settings().setAttribute(QtWebEngineWidgets.QWebEngineSettings.WebGLEnabled, True)
        self.temp_file = tempfile.NamedTemporaryFile(mode="w", suffix=".html", delete=False)
        self.resize(1000, 600)
        self.setWindowTitle("candlestick")
        self.set_figure(self.getFig(self.now_Id, self.now_start_time, self.now_end_time))

        self.lb1 = QLabel('股票代码：', self)
        self.lb1.move(40, 25)
        self.lb1.setStyleSheet('background-color: rgb(255,255,255)')
        self.lineEdit_Id = QLineEdit(self)
        self.lineEdit_Id.move(140, 25)

        self.lb2 = QLabel('起始日期：', self)
        self.lb2.move(250, 25)
        self.lb2.setStyleSheet('background-color: rgb(255,255,255)')
        self.lineEdit_start_time = QLineEdit(self)
        self.lineEdit_start_time.move(350, 25)

        self.lb3 = QLabel('结束日期：', self)
        self.lb3.move(460, 25)
        self.lb3.setStyleSheet('background-color: rgb(255,255,255)')
        self.lineEdit_end_time = QLineEdit(self)
        self.lineEdit_end_time.move(560, 25)

        self.lineEdit_Id.setText('399300')
        self.lineEdit_start_time.setText('2021-01-01')
        self.lineEdit_end_time.setText('2021-06-01')

        self.btn1 = QPushButton("查看K线", self)
        self.btn1.move(740, 25)
        self.btn1.setCheckable(True)



    def set_figure(self, fig=None):
        self.temp_file.seek(0)
        if fig is None:
            fig = go.Figure()
        fig.update_xaxes(showspikes=True)
        fig.update_yaxes(showspikes=True)
        html = to_html(fig, config={"responsive": True, 'scrollZoom': True})
        html += "\n<style>body{margin: 0;}" \
                "\n.plot-container,.main-svg,.svg-container{width:100% !important; height:100% !important;}</style>"

        self.temp_file.write(html)
        self.temp_file.truncate()
        self.temp_file.seek(0)
        self.load(QtCore.QUrl.fromLocalFile(self.temp_file.name))

    def closeEvent(self, event: QtGui.QCloseEvent) -> None:
        self.temp_file.close()
        os.unlink(self.temp_file.name)
        super().closeEvent(event)

    def sizeHint(self) -> QtCore.QSize:
        return QtCore.QSize(400, 400)

    def on_downloadRequested(self, download):
        dialog = QtWidgets.QFileDialog()
        dialog.setDefaultSuffix(".png")
        path, _ = dialog.getSaveFileName(self, "Save File", os.path.join(os.getcwd(), "newplot.png"), "*.png")
        if path:
            download.setPath(path)
            download.accept()

    def getFig(self, id, start, end):
        df = ts.get_k_data(id, index=True, start=start, end=end)
        fig = go.Figure(data=[go.Candlestick(x=df['date'],
                                             open=df['open'],
                                             high=df['high'],
                                             low=df['low'],
                                             close=df['close'])])
        fig.update_layout(
            title='The Great Recession',
            yaxis_title='AAPL Stock',
            shapes=[dict(
                x0='2021-01-09', x1='2021-01-09', y0=0, y1=1, xref='x', yref='paper',
                line_width=2)],
            annotations=[dict(
                x='2021-01-09', y=0.05, xref='x', yref='paper',
                showarrow=False, xanchor='left', text='Increase Period Begins')]
        )
        return fig


if __name__ == "__main__":
    app = QtWidgets.QApplication(sys.argv)
    pv = PlotlyViewer()
    pv.show()
    app.exec_()

```

