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