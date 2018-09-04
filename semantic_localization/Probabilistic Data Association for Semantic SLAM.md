本篇为ICRA2017 Best Paper，来自美国宾夕法尼亚大学的Sean L. Bowman和George J. Pappas。论文的核心就是提出了一种将尺度信息和语义信息融合的理论框架,而前端的的选择则可以基于实际情况挑选.
Traditional approaches to SLAM have some weakness:

- unable to assign semantic labels to landmarks
- Vulnerable to viewepoint and illumination and subject to failure in loop closure detection

slam问题的一个完整的表述是给定观测,包含传感器轨迹X,路标集合L和数据关联$\mathcal{D}$的最大似然估计:
![image.png-5.6kB][1]
$\mathcal{D}$代表了我手里的这个测量$\mathcal{z}_k$是在哪里看的哪个路标。
一般的slam过程中，姿态都是估计出来的，对应的地图中的路标也是估计出来的。求解这个问题,最常见的方法是分成两个估计问题:
一是给定先验估计$\chi^0$和$\mathcal{L}^0$,计算$\hat{ \mathcal{D}}$
![image.png-4.4kB][2]
$ \hat{ \mathcal{D} } = \mathop{\arg\max}_\mathcal{D} p( \mathcal{D}|\chi^0,\mathcal{L}^0,\mathcal{Z})$
二给定估计的数据关联$\hat{ \mathcal{D}}$,估计路标位置和传感器姿态
![image.png-4.9kB][3]
$\hat{\chi},\hat{\mathcal{L}} = \mathop{\arg\max}_{\chi,\mathcal{L}}\log p(\mathcal{Z}|\chi , \mathcal{L},\hat{\mathcal{D}})$
上面的第一步对第二步有影响,去掉模糊的测量避免不正确的数据关联,故存在坐标系下降法 迭代求解这两步
![image.png-10.2kB][4]
以上解决了一旦状态估计提升之后可以重复关联决策,但没有解决模糊观测因为数据关联的硬决策依然需要(?)

III.
语义信息是怎么辅助求解路标位置和传感器姿态的,
文章使用三个数据源:惯性信息,几何特征信息以及物体语义信息,
A.惯性信息
B.几何信息
C.语义信息

- 物体检测置信度
- 矩形框bounding box,即物体在图像中的位置
- 物体分类结果

物体检测用的CPU上也能跑的DPM(deformable part model),
当语义测量s_k的数据关联是已知时（即我们知道现在的观测结果是在A点看的梨子的时候），测量的似然可以分解成下列几个部分：
![image.png-7.6kB][5]
b 矩形框估计: 物体方框的似然用正态分布表示，其均值等于物体到图像平面内的投影中心，而协方差与被检测框的规模成正比。也就是说认为中心就是准的，而知道肯定有误差，这个误差用最显然的大小来凑就好了。
c 类别估计和s 置信度计算：与物体检测的confusion matrix有关。实际上，如果用神经网络来识别物体，可以代表神经网络中对图像矩阵的计算过程。也就是说这个统一的大模型甚至将物体检测部分也整合到了一起，可以连续运算。（其实就是将物体检测部分的概率融合到了计算中而已，实际还是分开的，只是表述起来浑然一体）
最后给出来语义slam的数学定义:
![image.png-41.8kB][6]
IV.EM法求解


  [1]: http://static.zybuluo.com/MichaelCZhou/cf14l9d8aylti1gmyjfpk1sm/image.png
  [2]: http://static.zybuluo.com/MichaelCZhou/caxtg8my4u36f1wqg0j3dnfh/image.png
  [3]: http://static.zybuluo.com/MichaelCZhou/aphla12txziidpdsa199saiq/image.png
  [4]: http://static.zybuluo.com/MichaelCZhou/jm4vky7f9w03n60k481ps44u/image.png
  [5]: http://static.zybuluo.com/MichaelCZhou/jllne2rbfaweyaun0h2y1tp5/image.png
  [6]: http://static.zybuluo.com/MichaelCZhou/gv7ppbfshw3y7r9ymesef9rt/image.png
