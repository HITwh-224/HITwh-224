’***ga***'函数是MATLAB中设置的调用遗传算法的函数，其通过构建自定义函数带入ga函数中进行求解，可以通过‘***options***’函数进行参数设置。
以下为示例：
```
clear,clc

%%文件读取

file_path = 'C:\Users\15855\Desktop\2023c\第二问\第二小问\六大类预测数据.xlsx';

data = readmatrix(file_path);

%%数据处理

D = [];

X = [];

for i = 1:100

tem_var01 = zeros(1,6);%创建零矩阵，用于01变量设置

pre_val = data(1:7,2:7);%七天预测蔬菜销售量

err_var = 0.3 * pre_val;%偏差函数

nor_val = pre_val + normrnd(0,0.5,[7,6]) .* err_var;%需求不确定集（偏差函数使用方差为0.5，均值为0的正态函数表示概率）

cst = [2.53 5.84 7.64 3.90 3.08 2.71];%蔬菜平均成本

gra1 = [0.09 0.09 0.03 0.06 0.04 0.05];%销量与售价的梯度关系

%%构建01变量

tem_var01 (1,nor_val(1,:) >= pre_val(1,:)) = 1;%寻找实际销量大于预计销量的值，赋值为1

tem_var_sm = pre_val(1,:) - nor_val(1,:);

lim = -tem_var_sm .* gra1 + cst;%计算蔬菜全部卖光需要的价格，设置为变量下限

%%构建决策变量，对于降价销售蔬菜，设置上限为成本的160%，对于加价蔬菜，设置上限为全部卖光所需价格

UB = lim.*tem_var01 + cst*1.6.*(1-tem_var01);%决策变量

options = optimoptions('ga', 'Display', 'iter'); % 遗传算法选项

[x,y] = ga(@(x)obj(x,nor_val),6,[],[],[],[],lim,UB,[],options);%调用ga函数，设置遗传算法求解

y = -y

D(1,i) = y

X(i,:) = x

end
%%绘图
Z = mean(D);

MX = mean(X,1);

M = ones(1,100)*Z;

xl = 1:100;

plot(xl,D,'-b',xl,M,'-r');%设置y轴

legend('最优利润');

xlabel('次数');

ylabel('利润');

disp('Optimized variables:');

disp(MX);

disp('Objective function value:');

disp(Z);

  

  

  

%%预设函数

function y = obj(x,m)

%需要的随机量，即不确定集得出的实际销量

cost = [2.53 5.84 7.64 3.90 3.08 2.71];

pre_sell = [190 28 29 32 117 71];

gra = [0.09 0.09 0.03 0.06 0.04 0.05];

y = (((2.53 - x(1))/0.09) + m(1,1))*x(1) + (((5.84- x(2))/0.09) + m(1,2))*x(2)+...

(((7.64 - x(3))/0.03) + m(1,3))*x(3) + (((3.90 - x(4))/0.06) + m(1,4))*x(4) +...

(((3.08 - x(5))/0.04) + m(1,5))*x(5) + (((2.71 - x(6))/0.05) + m(1,1))*x(6)-...

cost*pre_sell';

y = -y;

end
```