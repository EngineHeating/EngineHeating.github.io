---
title: QCustomPlot库的使用——绘制曲线
date: 2024-03-20 17:44:12
tags:
  - C++
  - Qt
  - QCustomPlot
---

# 1. 配置库
## 1.1 下载与预配置
- 下载压缩包，然后吧.cpp和.h文件放入工程目录中
- 在mainwindow.h中引用下载的.h文件
``` C++
#include "qcustomplot.h"
```
- Q4.0以上版本需要在.pro文件中加入：
```
QT += printsupport
```
- 在UI设计界面，拖入**Widget**,然后右键->提升为...然后在弹出的对话框中，在**提升为类名**那里输入QCustomPlot，然后头文件那里会自动填充为qcustomplot.h。单击添加按钮将QCustomPlot加入提升类列表中，最后单击提升就可以了。
  
 ![20230405212452](https://raw.githubusercontent.com/EngineHeating/MyPicGo/main/20230405212452.png) 

 可见，类中已经显示为QCustomPlot。预配置完成，**这个可以用来作为坐标轴画图。**

## 1.2 第一个demo：画一个简单抛物线
代码如下：
```C++
    // 生成数据，画出的是抛物线
    QVector<double> x(101), y(101); //初始化向量x和y
    for (int i=0; i<101; ++i)
    {
      x[i] = i/50.0 - 1; // x范围[-1,1]
      y[i] = x[i]*x[i]; // y=x*x
    }
    ui->customPlot->addGraph();//添加数据曲线（一个图像可以有多个数据曲线）

    // graph(0);可以获取某个数据曲线（按添加先后排序）
    // setData();为数据曲线关联数据
    ui->customPlot->graph(0)->setData(x, y);
    ui->customPlot->graph(0)->setName("第一个示例");// 设置图例名称
    // 为坐标轴添加标签
    ui->customPlot->xAxis->setLabel("x");
    ui->customPlot->yAxis->setLabel("y");
    // 设置坐标轴的范围，以看到所有数据
    ui->customPlot->xAxis->setRange(-1, 1);
    ui->customPlot->yAxis->setRange(0, 1);
    ui->customPlot->legend->setVisible(true); // 显示图例
    // 重画图像,类似于plot函数
    ui->customPlot->replot();
``` 
1

  