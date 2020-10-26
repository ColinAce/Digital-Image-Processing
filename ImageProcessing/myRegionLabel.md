# 区域标记

## 题目描述
```
设有一幅二值图像（元素取值为0或1），请生成该图像的标记图像。（即第一个连通区域中的每一个白色像素的值都置为1，第二个连通区域中的每一个白色像素的值都置为2，依此类推。区域编号可不考虑顺序）
```
## 算法思路
``` 
    种子法：
        1、建立一个队列，开辟另一个像素值为零的标记矩阵；
        2、扫描像素矩阵，遇到像素值为1，并且在标记矩阵中为0(即未被标记)的点，将其入队并进行标记；
        3、取队头元素为种子，扫描其八邻域。遇到像素值为1，并且在标记矩阵中为0(即未被标记)的点，将其入队进行标记；
        4、当队列为空时，重复2、3步操作进行下一个区域的标记。

    二次扫描法：
        1、开辟另一个像素值为零的标记矩阵，创建一个维度为1*255的set矩阵记录相连通的标记值；
        2、第一次扫描。逐行扫描，找到像素值为1且未被标记过的点，如果它的左上四个邻域内有已经标记的点，并且这些标记的点的label一致，则将这个label赋值给这个点在标记矩阵上对应的点；如果这些label不一致，则将较大值赋值给这个点在标记ta'de矩阵上对应的点；如果它的左上四个邻域没有已经标记的点，则给它一个新的标签；
        3、第二次扫描，根据set矩阵，将连通的区域都赋上最小值。
```

## 代码实现
Matlab:
```
1、种子法
function[ILabel,n] = myRegionLabel(I)
%--------------------区域标记(种子法)-----------------------
% Input：
%       B：原图像
% Output：
%       ILabel:返回该图像的标记图像
%       n:返回标记区域的个数
% Usage:
%       I = imread('rice.png');
%       [ILabel,n] = myRegionLabel(I);
%       figure,imshow(mat2gray(ILabel));    
%       mat2gray函数实现图像矩阵归一化，归一化后矩阵中每个元素的值都在0到1范围内
%----------------------------------------------------------
move = [-1,-1;0,-1;1,-1;0,1;1,1;0,1;-1,1;-1,0];
Ibw = im2bw(I);  %对图像进行二值化处理
Ibw = medfilt2(Ibw); %对图像进行中值滤波(针对米粒图像)
[h,w] = size(Ibw);
ILabel = zeros(h,w);   %另开空间标记数组
label = 1;
queue_head = 1;   %队头
queue_tail = 1;   %队尾

for i = 2:h-1     %每一行进行搜索
    for j = 2:w-1
        if Ibw(i,j) == 1 && ILabel(i,j) == 0 %当前结点为1且未被标记过
            ILabel(i,j) = label;
            Q{queue_tail} = [i j];
            queue_tail = queue_tail+1;
            
            while queue_head ~= queue_tail   %当前队列不为空时
                temp = Q{queue_head};
                for k = 1:8  %遍历当前结点的八邻域
                    move_x = temp(1) + move(k,1);
                    move_y = temp(2) + move(k,2);
                    if move_x >= 2 && move_x <= h-1 && move_y >=2 && move_y <= w-1   %限定边界，避免数组越界
                        if Ibw(move_x,move_y) ==1 && ILabel(move_x,move_y) ==0
                            ILabel(move_x,move_y) = label;
                            Q{queue_tail} = [move_x,move_y];
                            queue_tail = queue_tail+1;
                        end
                    end
                end
                queue_head = queue_head + 1;   %队头元素出队
            end
            clear Q;
            label = label + 1;
            queue_head = 1;   %初始化队头
            queue_tail = 1;   %初始化队尾
        end
    end
end
n = label - 1; 
```

```
2、二次扫描法
function[ILabel,n] = myRegionLabel2(I)
%-------------------区域标记(二次扫描)------------------------
% Input：
%       B：原图像
% Output：
%       ILabel:返回该图像的标记图像
%       n:返回标记区域的个数
% Usage:
%       [ILabel,n] = myRegionLabel2(I);
%       figure,imshow(mat2gray(ILabel));     
%       mat2gray函数实现图像矩阵归一化，归一化后矩阵中每个元素的值都在0到1范围内
%----------------------------------------------------------
move = [-1,0;-1,-1;0,-1;1,-1];
Ibw = im2bw(I);  %对图像进行二值化处理
Ibw = medfilt2(Ibw); %对图像进行中值滤波(针对米粒图像)
[h,w] = size(Ibw);
ILabel = zeros(h,w);   %另开空间标记数组
label = 2;  %初始化标签

set = zeros(1,256);
%第一次扫描，按行搜索，若该元素的邻域有不同的像素值，则计入set中
for i = 2:h-1
    for j = 2:w-1
        if Ibw(i,j) == 1
            pix1 = 0;  %初始化pix1
            for k = 1:4		%搜索该像素点的左/左上/上/右上四个邻域
                move_x = i + move(k,1);
                move_y = j + move(k,2);
                pix2 = ILabel(move_x,move_y);
                if pix1 ~= 0 && pix2 ~= 0 && pix1 ~= pix2 	%邻域像素值均不为0且像素值不相等，说明这两个区域是连通的，记录在set表中
                    set(1,max(pix1,pix2)) = min(pix1,pix2);  %对应关系为 最大值:较小值
                end
                pix1 = max(pix1,pix2);   %将最大值赋值给pix1
            end
            if pix1 ~= 0
                ILabel(i,j) = pix1;
            else 
                ILabel(i,j) = label;
                label = label + 1;
            end
        end  
    end
end

%第二次扫描，按行搜索，根据set中的元素，将最小值赋给该元素
for i = 2:h-1
    for j = 2:w-1
        pix = ILabel(i,j);
        if pix ~= 0
            while set(1,pix) ~= 0  %可能有几个区域连通，所以需要多次遍历set表
                pix = set(1,pix);
                label = label - 1;
            end
            ILabel(i,j) = pix;		%改为最小连通像素值
        end
    end
end
        
n = label-1;
```

## 效果显示
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/regionLabel.png)
![](https://github.com/ColinAce/Digital-Image-Processing/blob/main/pic/label.png)
