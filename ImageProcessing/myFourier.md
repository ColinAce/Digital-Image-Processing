# 傅里叶变换-理想低通滤波

## 题目描述
```
写出基于频域的低通滤波的步骤。
```
## 算法思路
``` 
    1、对图像进行傅里叶变换，由空域转换为频域，并进行居中变换；
    2、设置低频滤波的截止频率；
    3、由图像中心点计算各点到中心点的距离，并根据截止频率进行过滤；
    4、对去除了高频信号的频谱进行傅里叶反变换，由频域转为空域。
```

## 代码实现
Matlab:
```
function[re] = myFourier()
%----------------------理想低通滤波---------------------------
% Input：
%       I：二值图像
% Output：
%       re:低通滤波后的图像
% Usage:
%       re = myBwBoundaries(I);
%       plot(re(:,2),re(:,1),'r'); 
%       以顺时针方向给出边界点的位置
%----------------------------------------------------------
I = imread('cameraman.tif');
%I = rgb2gray(I);
d0 = 50 ; %设置阈值
I_noise = imnoise(I,'gaussian',0,0.02);  %添加高斯噪声
I_f = fftshift(fft2(double(I_noise)));   %通过傅里叶变换得到频谱，并进行居中操作
[h,w] = size(I);
h_mid = floor(h/2);
w_mid = floor(w/2);   %计算中心点

re = zeros(h,w);

for i = 1:h
    for j = 1:w
        d = sqrt((i - h_mid)^2 + (j - w_mid)^2);   %计算每个点距离中心点的距离，越远频率越高
        re(i,j) = (d <= d0)*I_f(i,j);   %根据阈值过滤掉高频信号
    end
end

re = ifftshift(re);  %傅里叶反变换
re = uint8(real(ifft2(re)));  %取实数部分

figure;
subplot(1,3,1),imshow(I,[]);title('原图像');
subplot(1,3,2),imshow(I_noise,[]);title('高斯噪声图像');
subplot(1,3,3),imshow(re,[]);title('理想低通滤波图像');
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/fourier.png)