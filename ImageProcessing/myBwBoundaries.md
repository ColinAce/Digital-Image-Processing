# 边界跟踪

## 题目描述
```
设一幅二值图像中，只有一个白色区域，试给出求该区域外围轮廓线的方法（要求按顺时针的顺序给出各点的坐标，即行/列号）。
```
## 算法思路
``` 
    1、找到二值图像中第一个不为0的像素点的位置；
    2、以这个点为初始点，并设置一个初始扫描方向。
    3、若扫描方向值为偶数，则dir = (dir+7)%8;
    若扫描方向值为基数，则dir = (dir+6)%8;
    4、从新方向位置开始，按顺时针方向进行扫描，遇到的第一个像素值不为0的像素点加入标记数组，并以这个点为出发点进行下一步扫描；
    5、重复3、4步骤，直到得到的像素点与初始像素点位置相同，结束扫描。
```

## 代码实现
Matlab:
```
function[re] = myBwBoundaries(I)
%----------------------边界跟踪---------------------------
%题目描述：设一幅二值图像中，只有一个白色区域，试给出求该区域外围轮廓线的方法
%（要求按顺时针的顺序给出各点的坐标，即行/列号）。
% Input：
%       B：二值图像
% Output：
%       P:白色区域的外围轮廓线
% Usage:
%       re = myBwBoundaries(I);
%       plot(re(:,2),re(:,1),'r'); 
%       以顺时针方向给出边界点的位置
%----------------------------------------------------------
I = im2bw(I);
move = [0,-1;-1,-1;-1,0;-1,1;0,1;1,1;1,0;1,-1;0,0];   %按顺时针搜索
re = [];
[h,w] = size(I);
[i,j] = find(I,1,'first');    %find函数返回第一个不为0的像素值的行/列
jbegin = j;    %记录边界搜索的起始位置
ibegin = i;
IsEnd = 0;  %判断是否搜索完毕
dir = 7;

while i <= h-1 && j <= w-1 && IsEnd == 0
    if dir/2 == 0
        dir = mod((dir+7),8);   %mod函数mod(a,b),返回a%b
    else
        dir = mod((dir+6),8);
    end
    %从dir方向开始搜索该像素的邻域，找到第一个不为0的位置，则为下一个像素
    next = [I(i,j-1),I(i-1,j-1),I(i-1,j),I(i-1,j+1),I(i,j+1),I(i+1,j+1),I(i+1,j),I(i+1,j-1),I(i,j)];
    for k = 1:8
        while next(1,dir+1) ~= 1   %dir为0-7，而next为1-8
            dir = mod(dir+1,8);
        end
        if next(1,dir+1) == 1
            re = [re;i,j];
            i = i + move(dir+1,1);
            j = j + move(dir+1,2);
            if ibegin == i && jbegin == j
                IsEnd = 1;
                break
            end
            break;
        end
    end
end
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/bwBoundaries_origin.png)
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/bwBoundaries.png)