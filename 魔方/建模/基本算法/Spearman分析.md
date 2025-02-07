斯皮尔曼相关分析是一种常用的非参数统计方法，用于衡量两个变量之间的相关性。它不依赖于数据的分布情况，适用于各种类型的数据。
spearman系数实际上就是我们常用的相关性系数r,其具体公式如下：
![](file:///C:/Users/15855/AppData/Local/Temp/msohtmlclip1/01/clip_image002.gif)

在matlab中，我们可以通过使用’***corr***‘函数来实现斯皮尔曼相关分析。我们需要准备两个变量的数据。假设我们有两个变量X和Y，它们的数据分别存储在两个向量x和y中。我们可以使用以下代码来生成这些数据：
```
clear,clc

%%数据导入

file_path = 'D:\Date\建模\建模练习\2023年c题\数据预处理\日销售数据.xlsx';

data = readmatrix(file_path);

%%数据处理

missing_indices = isnan(data);%查找缺失值 isnan函数可以便捷的找出数组数据中的缺失值

data_mean_imputed = data;%保留正常数据

data_mean_imputed(missing_indices) = 0;%定值插值

%%SPEARMAN相关性分析

SPE = [];

x1 = data_mean_imputed(:,2);%提取函数

x2 = data_mean_imputed(:,3);

x3 = data_mean_imputed(:,4);

x4 = data_mean_imputed(:,5);

x5 = data_mean_imputed(:,6);

x6 = data_mean_imputed(:,7);

for i = 1:6

for j = 1:6

tem_va = [x1,x2,x3,x4,x5,x6];

rho = corr(tem_va(:,i),tem_va(:,j),"type","Spearman");%计算spearman系数

SPE(i,j) = rho;

end

end

%%绘制热力图

xvalues = {'花叶类','花菜类','水生根茎类','茄类','辣椒类','食用菌'};

yvalues = {'花叶类','花菜类','水生根茎类','茄类','辣椒类','食用菌'};

h = heatmap(xvalues,yvalues,SPE,'Colormap',copper);
```
这里设置'***for***'循环是为了绘制热力图，我将两两相关的spearman数据储存在SPE矩阵中，进而通过其·绘制热力图。

可以通过修改'***colormap***'函数来调节热力图的颜色，***注意*** 色彩矩阵的最下端是表示1的意思，依次向上递减。

```
CM=[

0.3320 0.5320 0.7438

0.4549 0.6784 0.8196

0.5987 0.7935 0.8824

0.7399 0.8850 0.9333

0.8784 0.9529 0.9725

0.9595 0.9843 0.8235

0.9987 0.9595 0.6876

0.9961 0.8784 0.5647

0.9935 0.7477 0.4418

0.9804 0.5974 0.3412

0.9569 0.4275 0.2627

0.8810 0.2680 0.1895

0.7778 0.1255 0.1516

0.6471 0 0.1490];

h = heatmap(xvalues,yvalues,SPE,Colormap=CM);
```