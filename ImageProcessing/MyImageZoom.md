# 图像放大缩小

## 题目描述
```
设一幅大小为M×N的灰度图像I中，现要变成（放大或缩小）为 P×Q的图像J，请写出J的生成算法（要求使用双线性插值）。
```

## 算法思路
```
⚪线性插值：
        已知坐标(x0,y0)与(x1,y1)，要得到(x0,x1)区间内某一位置x在直线上的值。
        由已知值x和(y-y0)/(x-x0)=(y1-y0)/(x1-x0),得y=(x1-x)*y0/(x1-x0)+(x-x0)*y1/(x1-x0)
⚪双线性插值：
        有两个变量的插值函数的线性插值扩展，其核心思想是在两个方向分别进行一次线性插值。

```
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/interpolate.png)

```
在x方向上：
    f(R1) = (x2-x)*f(Q11)/(x2-x1) + (x-x1)*f(Q21)/(X2-x1),R1=(x,y1)
    f(R2) = (x2-x)*f(Q12)/(x2-x1) + (x-x1)*f(Q22)/(X2-x1),R2=(x,y2)
在y方向上：
    f(P) = (y2-y)*f(R1)/(y2-y1) + (y-y1)*f(R2)/(y2-y1)
    f(x,y) = f(Q11)*(x2-x)*(y2-y)/(x2-x1)(y2-y1) + f(Q21)*(x-x1)*(y2-y)/(x2-x1)(y2-y1) + f(Q12)*(x2-x)*(y-y1)/(x2-x1)(y2-y1)+f(Q22)*(x-x1)*(y-y1)/(x2-x1)(y2-y1)

在图像中双线性插值会用到周围四个点，并且分母为1，简化得到
f(i+u,j+v) = (1-u)*(1-v)*f(i,j) + (1-u)*v*f(i,j+1) + u*(1-v)*f(i+1,j) + u*v*f(i+1,j+1)

思路：
        1、根据放缩大小确定最终图像大小并创建矩阵；
        2、由最终图的点映射到原图像中；
        3、利用双线性插值，确定最终图像中像素点的像素值。
```