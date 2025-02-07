```
%% 加载数据

clear;clc

load data.mat

%% 第一步：正向化（极小型、中间型、区间型转极大型）

[n,m] = size(X);

disp(['共有' num2str(n) '个评价对象, ' num2str(m) '个评价指标'])

Judge = input(['这' num2str(m) '个指标是否需要经过正向化处理，需要请输入1 ，不需要输入0： ']);

if Judge == 1

Position = input('请输入需要正向化处理的指标所在的列，例如第2、3、6三列需要处理，那么你需要输入[2,3,6]： '); %[2,3,4]

disp('请输入需要处理的这些列的指标类型（1：极小型， 2：中间型， 3：区间型） ')

Type = input('例如：第2列是极小型，第3列是区间型，第6列是中间型，就输入[1,3,2]： '); %[2,1,3]

for i = 1 : size(Position,2) %这里需要对这些列分别处理，因此我们需要知道一共要处理的次数，即循环的次数

X(:,Position(i)) = Positivization(X(:,Position(i)),Type(i),Position(i));

end

disp('正向化后的矩阵 X = ')

disp(X)

end

%% 第二步：对正向化矩阵标准化

Z = X ./ repmat(sum(X.*X) .^ 0.5, n, 1);

disp('标准化矩阵 Z = ')

disp(Z)

%% 第三步：计算与最大值和最小值的距离，并计算得分

D_P = sum([(Z - repmat(max(Z),n,1)) .^ 2 ],2) .^ 0.5; % D+ 与最大值的距离向量

D_N = sum([(Z - repmat(min(Z),n,1)) .^ 2 ],2) .^ 0.5; % D- 与最小值的距离向量

S = D_N ./ (D_P+D_N); % 未归一化的得分

disp('最后的得分为：')

stand_S = S / sum(S)

[sorted_S,index] = sort(stand_S ,'descend');

disp('标准化后得分为：')

disp(sorted_S)

disp('对应的下标为:')

disp(index)
```


```
function [posit_x] = Positivization(x,type,i)

% 输入变量有三个：

% x：需要正向化处理的指标对应的原始列向量

% type： 指标的类型（1：极小型， 2：中间型， 3：区间型）

% i: 正在处理的是原始矩阵中的哪一列

% 输出变量posit_x表示：正向化后的列向量

if type == 1 %极小型

disp(['第' num2str(i) '列是极小型，正在正向化'] )

posit_x = Min2Max(x); %调用Min2Max函数来正向化

disp(['第' num2str(i) '列极小型正向化处理完成'] )

disp('~~~~~~~~~~~~~~~~~~~~分界线~~~~~~~~~~~~~~~~~~~~')

elseif type == 2 %中间型

disp(['第' num2str(i) '列是中间型'] )

best = input('请输入最佳的那一个值： ');

posit_x = Mid2Max(x,best);

disp(['第' num2str(i) '列中间型正向化处理完成'] )

disp('~~~~~~~~~~~~~~~~~~~~分界线~~~~~~~~~~~~~~~~~~~~')

elseif type == 3 %区间型

disp(['第' num2str(i) '列是区间型'] )

a = input('请输入区间的下界： ');

b = input('请输入区间的上界： ');

posit_x = Inter2Max(x,a,b);

disp(['第' num2str(i) '列区间型正向化处理完成'] )

disp('~~~~~~~~~~~~~~~~~~~~分界线~~~~~~~~~~~~~~~~~~~~')

else

disp('没有这种类型的指标，请检查Type向量中是否有除了1、2、3之外的其他值')

end

end
```

```
function [posit_x] = Inter2Max(x,a,b)

r_x = size(x,1); % row of x

M = max([a-min(x),max(x)-b]);

posit_x = zeros(r_x,1); %zeros函数用法: zeros(3) zeros(3,1) ones(3)

% 初始化posit_x全为0 初始化的目的是节省处理时间

for i = 1: r_x

if x(i) < a

posit_x(i) = 1-(a-x(i))/M;

elseif x(i) > b

posit_x(i) = 1-(x(i)-b)/M;

else

posit_x(i) = 1;

end

end

end
```

```
function [posit_x] = Mid2Max(x,best)

M = max(abs(x-best));

posit_x = 1 - abs(x-best) / M;

end
```

```
function [posit_x] = Min2Max(x)

posit_x = max(x) - x;

% posit_x = 1 / x; 如果x全部都大于0，也可以这样正向化

end
```

```
clear,clc

file_path = 'C:\Users\15855\Desktop\2023c\第三问\topsis\蔬菜数据.xlsx';

data = readmatrix(file_path);

X = data(:,24:28);
```
