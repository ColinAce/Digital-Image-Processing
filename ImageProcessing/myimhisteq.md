# 直方图均衡化

## 题目描述
```
设一幅大小为M×N的灰度图像I中，灰度为g的像素数为h(g)， 。 请写出对图像I进行直方图均衡化，得到图像J的计算方法。
```
## 算法思路
``` 
    1、计算图像矩阵中各个灰度值的概率直方图，并求出累积概率；
    2、根据累计概率直方图，得到均衡化后的映射；
    3、根据映射进行将原图中每个像素点进行映射。
```

## 代码实现
Matlab:
```
function [I2] = myimhisteq(I)
[h,w,d] = size(I);
if(d>1)
    I = rgb2gray(I); %如果颜色通道数C>1，则将彩色图像转化为灰度图rgb2gray
end

%统计每个像素出现的次数
count = zeros(1,256);
for i = 1:h
    for j = 1:w
        count(1,I(i,j)+1) = count(1,I(i,j) + 1)+1;
    end
end

%编写T函数
T = zeros(1,256);
T = double(T);
count = double(count);

%统计每个像素值出现的概率，得到概率直方图
for i = 1:256
    T(1,i) = count(1,i)/(h*w);
end

%求累计概率，得到累计直方图
for i = 2:256
    T(1,i) = T(1,i-1) + T(1,i);
end

%根据累计直方图，计算每个像素点变换后对应的像素点
for i = 1:256
    T(1,i) = T(1,i)*255;
end

%将原灰度图中每个点进行映射
I2 = double(I);
for i = 1:h
    for j = 1:w
        I2(i,j) = T(1,I2(i,j)+1);
    end
end

%改回数据类型
I2 = uint8(I2);
I3 = histeq(I);
subplot(1,3,1);imshow(I);title('原灰度图');
subplot(1,3,2);histeq(I3);title('内置方法');
subplot(1,3,3);imshow(I2);title('手写方法');
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/histeq.png)