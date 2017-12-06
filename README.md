
# greyforecasting：欢迎丁松同学参加内测

灰色预测工具包开发
## 工具加载方法
推荐使用工具：
- R语言[https://cloud.r-project.org/](https://cloud.r-project.org/ "R语言")
- Rstudio[https://www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/ "Rstudio")  
先安装R再安装Rstudio，R是计算平台，Rstudio是一个IDE工具，有点像辅助界面之类的工具。在Rstudio的环境中加载工具包  

首先，下载devtools包并在环境中加载
~~~{r}
install.package("devtools")
library("devtools")
~~~
然后，直接从github上在线加载该工具包
~~~{r}
install_github("exoplanetX/greyforecasting")
~~~
之后，以工具包方式正常加载之后便可以使用了
~~~{r}
library(greyforecasting)
~~~
## 灰色预测基本模型

1. GM(1,1)模型的计算方法
~~~{r}
y #案例数据
gmprocess(y)
~~~
gmprocess()函数是GM(1,1)模型计算的集成函数，参数包括三个：
- y:（不可缺省）原始数据，至少4个元素以上的向量；
- term:(可缺省）预测期，默认预测期为1；
- pattern：（可缺省）预测类型，模型为“forecast",即仅返回GM(1,1)预测数值，当设为“model"时返回一个gmodel类型模型列表变量，包含所有参数、拟合值和预测值;
2. AGRM模型  

自己做的一个模型，命名为AGRM(1,1)
~~~{r}
demo.agrm()
~~~
直接缺省方式运行，返回拟合图。其他还没做好。

3. 滚动建模方法  

灰色预测当中的动态建模，即新陈代谢模型，基础是类似GM(1,1)这样的模型。
~~~{r}
rolling(y)
~~~
rolling函数的参数有：
- y:(不可缺省）原始数据，大于4个元素的向量；
- x:（可缺省）数据y的名称，要求与y等长，模型为1：length(y)，拟合图中的横轴刻度即用的它；
- rollterm：（可缺省）默认rollterm=3，滚动预测期数
- piece:(可缺省）默认piece=4，数据切片长度，即数据窗口
- stepsize：（可缺省）默认stepsize=1,滚动步长，即每次以几个元素向前滚动生成数据切片。
## 灰色预测工具函数
工具函数是将典型的灰色预测模型分解为功能部分后编写的各部分功能函数，例如累加生成、背景值、参数估计方法、响应式生成方法等。主要目的是方便建立新模型时候二次开发。
1. 生成模型变量  
作为建模基础，模型变量创建了一个标准化的数据集，其中包含了原始数据、预处理方法、背景值公式、参数估计方法和响应式生成方法。创建方法：
~~~{R}
y #案例数据
gdata<-gmodel(y)
~~~
gmodel函数用于创建一个列表类型的模型变量，参数包括：
- y:(不可缺省）原始数据，大于4个元素的向量；
- seqname:（可缺省）默认使用向量y自带的数据名称，即names(y)，要求与y等长；
- term:（可缺省）：预测期，默认term=1.


2. 进行模型计算  
对建立好的模型变量进行计算，计算功能包括按照设置好的方法进行参数估计、生成时间响应式、生成拟合值、计算出预测值。
~~~{r}
gdata<-gm(gdata) #对模型变量gdata进行计算，计算结果重新存入gdata当中
~~~
gm()函数的参数包括：
- gdata:(不可缺省)设置好的模型变量

3. 建模结果可视化  
gdata模型变量经过计算后，可以使用gm.graph()函数进行可视化作图，函数使用方法：
~~~{r}
picture<-gm.graph(gdata)
~~~
gm.graph的参数有：
- gdata:(不可缺省)经过gm()计算过的模型变量
- pattern:(可缺省)生成的图形类型，默认pattern="fitting"拟合图，当pattern="errors"时生成误差图
经过gm.graph生成的是一个图形函数，以拟合图为例，picture以函数方式调用即可做出图形
~~~{r}
picture()
~~~
其中picture(...)的...可以缺省按默认方式画图，也可以是控制图形元素的参数，具体图形参数请查看
~~~{r}
help(par)
~~~
常用的图形参数包括
- xlab、ylab：设置横轴纵轴标题
- main:设置图中主标题
- col:设置图中元素颜色
- ...
4. 比较拟合精度
