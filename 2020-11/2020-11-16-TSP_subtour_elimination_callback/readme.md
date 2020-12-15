# 运小筹(2020-11-16): TSP callback README

@[TOC] 
# TSP中两种不同消除子环路的方法及callback实现（Python调用Gurobi求解）

作者：刘兴禄，清华大学，清华伯克利深圳学院 (博士在读) 

邮箱：hsinglul@163.com


> 原博客链接 [https://blog.csdn.net/HsinglukLiu/article/details/107871295](https://blog.csdn.net/HsinglukLiu/article/details/107871295)

## Introduction

> 本代码主要是实现TSP的两种子环路的消除方法及其代码实现。

代码求解结果可视化效果如图

> 例如：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806202054628.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hzaW5nbHVrTGl1,size_16,color_FFFFFF,t_70#pic_center)
图片来自：[http://algorist.com/problems/Traveling_Salesman_Problem.html](http://algorist.com/problems/Traveling_Salesman_Problem.html)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806202603742.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hzaW5nbHVrTGl1,size_16,color_FFFFFF,t_70#pic_center)
图片来自[http://www.math.uwaterloo.ca/tsp/methods/opt/subtour.htm](http://www.math.uwaterloo.ca/tsp/methods/opt/subtour.htm)



## Model
### 第一种消除子环路约束的方法：subtour-elimination constraints
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215161759867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hzaW5nbHVrTGl1,size_16,color_FFFFFF,t_70)
### 第2种消除子环路约束的方法：MTZ formulation
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215161922624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hzaW5nbHVrTGl1,size_16,color_FFFFFF,t_70)


## Quick start 

 - `readData(path, nodeNum)`:读取.txt文档中的算例数据;
 - `reportMIP(model, Routes)`：获得并打印最优解信息;
 - `getValue(var_dict, nodeNum)`:获得决策变量的值，并存储返回一个`np.array()`数组;
 - `getRoute(x_value)`:根据解`x_value`得到该解对应的路径。

然后定义几个非常关键的`用于添加subtour-elimination`约束的函数：

 - `subtourelim(model, where)`: callback函数，用于为`model`对象动态添加`subtour-elimination`约束；
 - `computeDegree(graph)`: 给定一个graph(二维数组形式)，也就是给定一个`邻接矩阵`，计算出每个结点的`degree`.(degree=每个结点被进入次数+被离开的次数)；
 - `findEdges(graph)`: 给定一个graph(二维数组形式)，也就是给定一个`邻接矩阵`，找到该图中所有的`边`，例如[(1, 2), (2, 4), (2, 5)]；
 - `subtour(graph)`:给定一个graph(二维数组形式)，也就是给定一个`邻接矩阵`，找到该图中包含结点数目最少的`子环路`，例如[2, 3, 5]。

其中，函数`subtourelim(model, where)`中，调用了函数`computeDegree(graph)`、`findEdges(graph)`和`subtour(graph)`。


## Dependencies

> gurobi
> numpy
> math
> re
> matplotlib
> pandas
> copy
> time


## Dataset Format
Solomon VRP benchmark instance
下载地址[https://www.sintef.no/projectweb/top/vrptw/solomon-benchmark/100-customers/](https://www.sintef.no/projectweb/top/vrptw/solomon-benchmark/100-customers/)

```python
C101

VEHICLE
NUMBER     CAPACITY
  25         200

CUSTOMER
CUST NO.  XCOORD.   YCOORD.    DEMAND   READY TIME  DUE DATE   SERVICE   TIME
 
    0      40         50          0          0       1236          0   
    1      45         68         10        912        967         90   
    2      45         70         30        825        870         90   
    3      42         66         10         65        146         90   
    4      42         68         10        727        782         90   
    5      42         65         10         15         67         90   
    6      40         69         20        621        702         90   
    7      40         66         20        170        225         90   
    8      38         68         20        255        324         90   
    9      38         70         10        534        605         90   
   10      35         66         10        357        410         90   
   11      35         69         10        448        505         90   
   12      25         85         20        652        721         90   
   13      22         75         30         30         92         90   
   14      22         85         10        567        620         90   
   15      20         80         40        384        429         90   
   16      20         85         40        475        528         90   
   17      18         75         20         99        148         90   
   18      15         75         20        179        254         90   
   19      15         80         10        278        345         90   
   20      30         50         10         10         73         90   
   21      30         52         20        914        965         90   
   22      28         52         20        812        883         90   
   23      28         55         10        732        777         90   
   24      25         50         10         65        144         90   
   25      25         52         40        169        224         90   
   26      25         55         10        622        701         90   
   27      23         52         10        261        316         90   
   28      23         55         20        546        593         90   
   29      20         50         10        358        405         90   
   30      20         55         10        449        504         90   
   31      10         35         20        200        237         90   
   32      10         40         30         31        100         90   
   33       8         40         40         87        158         90   
   34       8         45         20        751        816         90   
   35       5         35         10        283        344         90   
   36       5         45         10        665        716         90   
   37       2         40         20        383        434         90   
   38       0         40         30        479        522         90   
   39       0         45         20        567        624         90   
   40      35         30         10        264        321         90   
   41      35         32         10        166        235         90   
   42      33         32         20         68        149         90   
   43      33         35         10         16         80         90   
   44      32         30         10        359        412         90   
   45      30         30         10        541        600         90   
   46      30         32         30        448        509         90   
   47      30         35         10       1054       1127         90   
   48      28         30         10        632        693         90   
   49      28         35         10       1001       1066         90   
   50      26         32         10        815        880         90   
   51      25         30         10        725        786         90   
   52      25         35         10        912        969         90   
   53      44          5         20        286        347         90   
   54      42         10         40        186        257         90   
   55      42         15         10         95        158         90   
   56      40          5         30        385        436         90   
   57      40         15         40         35         87         90   
   58      38          5         30        471        534         90   
   59      38         15         10        651        740         90   
   60      35          5         20        562        629         90   
   61      50         30         10        531        610         90   
   62      50         35         20        262        317         90   
   63      50         40         50        171        218         90   
   64      48         30         10        632        693         90   
   65      48         40         10         76        129         90   
   66      47         35         10        826        875         90   
   67      47         40         10         12         77         90   
   68      45         30         10        734        777         90   
   69      45         35         10        916        969         90   
   70      95         30         30        387        456         90   
   71      95         35         20        293        360         90   
   72      53         30         10        450        505         90   
   73      92         30         10        478        551         90   
   74      53         35         50        353        412         90   
   75      45         65         20        997       1068         90   
   76      90         35         10        203        260         90   
   77      88         30         10        574        643         90   
   78      88         35         20        109        170         90   
   79      87         30         10        668        731         90   
   80      85         25         10        769        820         90   
   81      85         35         30         47        124         90   
   82      75         55         20        369        420         90   
   83      72         55         10        265        338         90   
   84      70         58         20        458        523         90   
   85      68         60         30        555        612         90   
   86      66         55         10        173        238         90   
   87      65         55         20         85        144         90   
   88      65         60         30        645        708         90   
   89      63         58         10        737        802         90   
   90      60         55         10         20         84         90   
   91      60         60         10        836        889         90   
   92      67         85         20        368        441         90   
   93      65         85         40        475        518         90   
   94      65         82         10        285        336         90   
   95      62         80         30        196        239         90   
   96      60         80         10         95        156         90   
   97      60         85         30        561        622         90   
   98      58         75         20         30         84         90   
   99      55         80         10        743        820         90   
  100      55         85         20        647        726         90   

```


solomon相关数据集、最优解等详细信息见官网：[solomon benchmark](https://www.sintef.no/projectweb/top/vrptw/solomon-benchmark/)



## Contact
Your Name ：  hsinglul@163.com

My blog:   [https://blog.csdn.net/HsinglukLiu?spm=1010.2135.3001.5113](https://blog.csdn.net/HsinglukLiu?spm=1010.2135.3001.5113)


## About us
运小筹公众号是致力于分享运筹优化(LP、MIP、NLP、随机规划、鲁棒优化)、凸优化、强化学习等研究领域的内容以及涉及到的算法的代码实现。编程语言和工具包括Java、Python、Matlab、CPLEX、Gurobi、SCIP 等。


**关注我们:  运筹小公众号**


![在这里插入图片描述](https://img-blog.csdnimg.cn/20201214000806951.png)






