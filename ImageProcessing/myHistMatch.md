# 直方图规定化

## 题目描述
```
设一幅大小为M×N的灰度图像I中，灰度为g的像素数为h(g)， 。另给定一个直方图t(g)，。 请写出对图像I进行变换的方法，使得变换后的新图像的直方图与t相同（近似相等）。
```
## 算法思路
``` 
    1、计算原图像和规定图像的累计直方图；
    2、利用映射关系，求出原图中每个像素值的累积概率对应在规定图中最相近的累积概率所对应的像素值并赋值。
```

## 代码实现
Matlab:
```
function [matchI] = myHistMatch(I1_gray,I2_gray)
%----------------------直方图规定化---------------------------
% Input：
%       I1_gray：原图像
%       I2_gray：规定标准图
% Output：
%       matchI：直方图规定化后得到的图像
% Usage:
%       I1_gray=imread('cameraman.tif');
%       I2_gray=imread('rice.png');
%       myHistMatch(I1_gray,I2_gray)
%          
%----------------------------------------------------------
%I1_gray = rgb2gray(I1);    %若为彩色图像，则进行转换
[h1,w1] = size(I1_gray);
%I2_gray = rgb2gray(I2);
[h2,w2] = size(I2_gray);

%计算I1/I2每个像素点的个数
count1 = zeros(1,256);
for i = 1:h1
    for j = 1:w1
        count1(1,I1_gray (i,j)+1) = count1(1,I1_gray (i,j)+1)+1;
    end
end

count2 = zeros(1,256);
for i = 1:h2
    for j = 1:w2
        count2(1,I2_gray(i,j)+1) = count2(1,I2_gray(i,j)+1) + 1;
    end
end

%计算每个点出现的概率，得到概率直方图
origin = zeros(1,256);origin = double(origin);count1 = double(count1);
standard = zeros(1,256);standard = double(standard);count2 = double(count2);

for i = 1:256
    origin(1,i) = count1(1,i)/(h1*w1);  %原始图
    standard(1,i) = count2(1,i)/(h2*w2);%规定图
end

%求累计概率，得到累计直方图
for i = 2:256
    origin(1,i) = origin(1,i-1) + origin(1,i);
    standard(1,i) = standard(1,i-1) + standard(1,i);
end

%进行直方图匹配，求出origin中每个累计概率对应在standard中的概率(差值最小)
%[x y] = [...]得到x为最小值，y为最小值所在下标
value = zeros(256,256);
for i = 1:256
    for j = 1:256
        value(i,j) = abs(standard(1,j) - origin(i));
        [x index(i)] = min(value(i,:));  %x为第i行最小值，index(i)即表示原始图中
    end
end

%进行匹配
matchI = zeros(h1,w1);
for i = 1:h1
    for j = 1:w1
        matchI(i,j) = index(I1_gray(i,j)+1)-1;
    end
end

matchI = uint8(matchI);

subplot(2,3,1);imshow(I1_gray);title('原图');
subplot(2,3,2);imshow(I2_gray);title('标准图');
subplot(2,3,3);imshow(matchI);title('匹配图');
subplot(2,3,4);imhist(I1_gray);title('原图');
subplot(2,3,5);imhist(I2_gray);title('标准图');
subplot(2,3,6);imhist(matchI);title('匹配图');
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/histMatch.png)