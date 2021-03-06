# 总结

|                    | 概率模型        | 非概率（可视化） |
| ------------------ | --------------- | ---------------- |
| 数据分布于“子空间” | Factor Analysis | PCA              |
| 数据分布于“聚簇”   | EM（混合高斯）  | k-means          |



# k-means 聚类

1. 选择k个样本，取均值作为中心μ1；选择另外k个点，取均值作为中心μ2；...
2. 重复至收敛：
   1. 对每个样本，令它的类别为最靠近（L2范数）的中心μ的类别
   2. 对每个类别的样本，重新计算一个中心μ



##### 一定会收敛

cost function：
$$
J(c,\mu) = \sum_{i=1}^n ||x^{(i)} - \mu_{c^{(i)}}||^2
$$
每次迭代  J 都一定会下降，且迭代到一定程度后不会再下降：

- 先保持类心μ不动，而样本改变类别，从而它与类心的距离一定是减少的；
- 之后类心取均值，则此类别中样本的loss也会下降。

（优化是非凸的，因此不是全局最优解；可以使用不同的质心初始化算法，进行100次，选择 J 最小的结果）



##### 如何选择k？

手动选。。可以参考下游用途



# EM算法

类似GDA，但是只知道输入x，不知道x的分类z。

应用于混合高斯模型时：

1. 大概知道数据是服从两个高斯分布

2. E-step：利用贝叶斯写出“输入x属于zj个高斯”的条件概率wj=P(z=j|x)
3. M-step：利用MLE算出wj表达式里的参数Φ、μ、Σ
4. 然后用类似 k-means 的方法，根据M-step的结果，将x的设为最有可能属于的哪个高斯函数，求x的均值（这是MLE求偏导得到的算法），迭代更新

最后就可以拟合出一个数据分布符合多个高斯分布的模型；一方面成功的将多个混在一起的高斯给分离了；另一方面也成功的描述了一个非线性的分布，可以用于判断新输入是否在这个奇怪的形状里（吗？）。



##### jessen不等式

f 为凸函数（f'' > 0），X为随机变量，则有
$$
E[f(X)] \ge f(E[X])
$$
几何证明：连接凸函数上任意两个点，E[f(X)] 是线段中点的高度，线段中点的x就是这两个点的E[X]，而由于凸函数，f(E[X]) 一定在 E[f(X)] 之下。

推论1：如果凸函数有 $E[f(X)] = f(E[X])$ ，则X是常数。

推论2：凹函数，则把不等号反转



##### 收敛性

几何解释：

1. 目标是MLE，似然估计L是一个凹的曲线
2. E-step其实是在 L 上取一个点θ1，画出一条过该点、且始终小于等于 L 的另一条凹的曲线 J（利用了jessen不等式）；
3. M-step最优化为求曲线 J 上的最大值θ2
4. 再用θ2去 L 曲线上进行下一次迭代

可以求得局部最优解（似然估计可能不是一直是凹的）



# Factor Analysis

数据维度很大，而样本量很少，怎么拟合？



问题1：样本很少会导致高斯拟合时，协方差矩阵是奇异矩阵（模为0、无法求逆），导致数值上无法计算



1. 约束协方差矩阵Σ
   1. 令 Σ 是对角矩阵，即x的各个特征是相互独立的
   2. 令 Σ = σ^2 * I，即对角线上的元素都是 σ^2，正圆形的高斯模型轮廓



原理：假设隐变量z具有较低的维度，将z线性映射到一个高维空间 λz +μ，则此时 λz 处在一个超平面上，再为 λz 添加高维的高斯噪声 ε，对其采样即可得到高维的x = λz + μ + ε（学习过程就是对这个高维的x建立模型）。



使用EM迭代计算



# 主成分分析（PCA）

相比于 factor analysis 用概率模型，PCA直接求特征向量，从而实现数据降维



1. 预处理，标准化：$x_i = \frac{x_i - \mu}{\sigma}$
2. 找一个单位向量 u，使得所有样本 x 到 u 的投影到原点的距离之和最大化（该方向上数据分布的方差最大）

$$
\max_u \ \frac{1}{m}\sum_{i=1}^m ({x^{(i)}}^Tu)^2 
= u^T(\frac{1}{m}\sum_{i=1}^m (x^{(i)}{x^{(i)}}^T))u
$$

3. 根据rhs可知 u 其实是矩阵 $\sum x^{(i)} {x^{(i)}}^T$ 中特征值最大的特征向量；要降到k维，就求出所有特征值 λ，就选择前k大的特征向量

4. 降维：把x分别投影到这k个特征向量 $y_i = {u_i}^T x$
5. 还原：$x \approx y_1u_i + y_2u_2 + \dots + y_ku_k$



k如何选取？

一种度量：前k个特征值 λ 之和占所有特征值之和的0.9



用处：

1. 降维，在低维度上可视化，压缩数据
2. 监督学习的数据预处理，降维后，提高学习效率（不建议，如果要避免overfitting，不要用PCA，建议用regularization）
3. 降噪



缺点：

特征向量不太稳定。训练样本稍微的改变可能就导致特征向量完全变了，例如数据均匀分布在一个3d空间中的平面圆上，则特征向量可以平面圆上任意两个垂直的向量



# Independent Components Analysis（ICA）

用于解决“鸡尾酒派对”问题：

假设有n个人发出声音，n个麦克风都多多少少收音到了所有音源，于是得到了n个混音的输出x：
$$
x = As
$$
其中s是n个独立的音源，A是表示将n个独立音源进行**线性组合**的混合方阵。目标是找到一个 $W = A^{-1}$，即可还原 $s = Wx$。 



还原出隐变量s的歧义问题：

- 还原后s'的排列顺序可能改变（没关系）
- 还原后s'可能有缩放倍数（没关系）
- 如果s的来源相互独立，且是**高斯分布**的（旋转对称），则无法恢复：我们无法得知某个平面上的两个垂直的s1和s2原始的方向是什么（因为可以旋转）
- 麦克风多于实际的音源数？没关系，只是学习额外得到了几个静音的麦克风。反之不行，因为这样的A无法求逆。



##### x的概率密度

由于连续变量x是s经过A**线性变换**得到，因此x的概率密度：
$$
p_x(x) = p_s(s) · |W|
$$
（线性变换A导致体积元缩放|A|倍，密度缩放为1/|A| = |W|）

（有点反直觉，但是考虑连续变量的积分CFD为1，所以密度也乘上一个系数好像也很合理？）



##### 假设

- s内的每个音源都是独立的：$p_s(s) = \prod_{i=1}^n p_s(s_i) = \prod_{j=1}^n p_s(w_j^Tx)$
- 均值E[x] = 0
- s的CFD是sigmoid：$p_s(s) = g'(s)$（也可以选择其他的CFD，如Laplace，相比于高斯PDF的密度在远离μ时迅速的降为0，sigmoid的PDF在较远处也会较平缓的下降，适合人声等自然规律）



学习：

联立上面三个式子即可写出似然估计函数：$log(\prod p_x(x^{(i)}))$

通过对参数W（？？）求偏导来MLE，可得随机梯度下降的学习规则。

这里的训练样本是将一段音频按时间切分，随机梯度下降时，每次选择一小段进行训练，最后全部还原后再拼接起来播放。为了满足独立性的假设，训练时尽量不要连续选择靠近时间内的样本呢。



应用：

1. 清理脑电波数据（多个电极同时采样出现重叠的电脉冲）
