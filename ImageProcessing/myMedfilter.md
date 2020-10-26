# 直方图均衡化(未对边界处理)

## 题目描述
```
请写出使用大小为3×3的模板对图像I进行中值滤波，生成图像J的方法。
```

## 算法思路
``` 
    对图像中的每个像素点，对它和它的八邻域的九个点进行排序，将排序后的中值赋值给找个点
```

## 代码实现
Matlab:
```
function [] = myMedfilter(I)
I = rgb2gray(I);
subplot(1,3,1);imshow(I);title('原灰度图');
[m,n] = size(I); %m表示行数(高度)，n表示列数(宽度)
In = imnoise(I,'salt&pepper',0.02); %表示加入了椒盐噪声，噪声密度为0.02
subplot(1,3,2);imshow(In);title('椒盐噪声图');
Id = double(In);
Id1 = Id;
%--------未对边界进行处理----------%
for i = 1:m-1
    for j =1:n-1
        mb = Id((i-1):(i+1),(j-1):(j+1));
        mb = mb(:);   %矩阵中的元素以一列输出
        mm = median(mb); %取向量的中值
        Id1(i,j) = mm;  %对中心元素赋值
    end
end

I1 = uint8(Id1);
subplot(1,3,3);imshow(Id1);title('中值滤波后图');
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/medfilter.png)
