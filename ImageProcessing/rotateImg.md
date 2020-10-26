# 图像旋转

## 题目描述
```
设一幅大小为M×N的灰度图像I中，现要将其逆时针旋转 A度，得到图像J，请写出J的生成算法（要求使用近邻插值）。
```

## 算法思路
```
    1、通过旋转角度θ计算图像四个顶点旋转后的位置，可以得到图像旋转后在两个坐标方向上的偏移量(即负值)dh和dw，并通过这四个位置得到旋转后的图像大小，并初始化新矩阵；
    2、旋转后的点P1与旋转前的点P0的对应关系为:
        x0 = x1*cosθ - y1*sinθ (x对应宽度)
        y0 = x1*sinθ + y1*cosθ (y对应高度)
        并对对应的坐标进行取整。
    3、扫描旋转后的图像矩阵，每个点根据2步骤进行对应。若能对应到原图像中，则将对应点的像素值赋值给这个点；若不能对应，则置为0。
```

## 代码实现

Matlab:
```
function [im] = rotateImg(I,angle)
%----------------------图像旋转---------------------------
% Input：
%       I：原图像
%       ang:旋转的角度
% Output：
%       im:旋转后得到的图像
% Usage:
%       I=imread('cameraman.tif');
%       angle = pi/3;  %规定旋转角度，逆时针旋转
%       rotateImg(I,angle);
%          
%----------------------------------------------------------

[h,w,d] = size(I);
%angle = pi/3;  %规定旋转角度，逆时针旋转
a=sin(angle);b=cos(angle);
w_min = min(min(min(h*a+w*b,h*a),w*b),0);   %计算x方向的偏移量
h_min = min(min(min(min(h*b-w*a),h*b),-w*a),0);   %计算y方向的偏移量
w_max = max(max(max(h*a+w*b,h*a),w*b),0);
h_max = max(max(max(max(h*b-w*a),h*b),-w*a),0);
dw=abs(w_min); dh=abs(h_min);   %偏移量取绝对值
w_rotate = w_max-w_min;   %旋转后的图像大小
h_rotate = h_max-h_min;
I_rotate = zeros(round(h_rotate),round(w_rotate),d);

for i = 1:h_rotate
    for j = 1:w_rotate
        ii = round((j-dw)*a+(i-dh)*b);   %利用旋转后的坐标对应到原图坐标
        jj = round((j-dw)*b-(i-dh)*a);
        if(ii>=1&&ii<=h&&jj>1&&jj<=w)
            I_rotate(i,j,:) = I(ii,jj,:);
        end
    end
end

im = uint8(I_rotate);
figure,imshow(im);
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/rotate1.png)
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/rotate2.png)