假设

1. 数据服从某个分布D（训练集和测试集）
2. 数据的采样过程是独立的



### 泛化误差

1. bias：欠拟合，没有认识到数据的真实结构，模型过于简化，导致一部分数据很好的拟合，另一部分数据偏离（如用线性模型你和非线性模型）
2. variance：过拟合，模型过于复杂，导致分离边界非常扭曲

解释：

1. 类似均值和方差
2. 同一个算法、相同的样本训练多次得到的参数，bias表示参数相对于真实参数都往一个方向偏移，而variance表示每次训练出的参数的分散程度。
3. 随着训练样本的增大，variance会变小，而bias是不变的，因此无论输入多少数据，bias的存在会导致训练结果总是无法收敛到真实的参数。



误差：

假设

1. 某个模型的真实模型是g
2. 使用某一类算法可以得到的最接近g的模型是h*
3. 使用上述算法、特定训练集得到的模型h

存在误差：

1. ε(g)：Bayes Error，本身存在的不可避免误差
2. ε(h\*)-ε(g)：Approximation Error
3. ε(h)-ε(h\*)：Estimation Error

所以：

- 误差ε(h) = Estimation + Approximation + Bayes Error

- variance：来自于 Estimation Error
- bias：来自于 Estimation 、Approximation Error



##### 降低variance

1. 增大训练数据量
2. regularization，正则化（可能会增大bias）



##### 降低bias

1. 增加可选的假设模型、算法（会增大variance）



regularization

样本少、参数多的时候容易过拟合，避免过拟合的方法：在最大化、最小化目标里减去、加上一个λ|θ|（λ过大也会导致欠拟合）



Empirical Risk Minimization

然后讲了下用训练集得出的模型h和真实模型h*之间的五误差关系



### 模型选择

简单保留交叉验证（CV）

将训练集划分：60%训练、20%开发（用于选择模型）、20%测试



k-fold CV

用于样本非常少的时候。

将训练集划分为k个（通常为10）

1. 

对每个模型: {

​	for i = 1, ..., k: {

​		取第i块作为测试集，其他作为训练集;

​	}

​	求k个测试误差的平均值;

}

2. 选出最合适的模型，然后用100%的数据进行训练





### 特征选择

1. 特征集合 F 初始化为空集

2. 选择一个特征，尝试加入到F；看加入哪个特征对效果提升最大，将其加入到F；
3. 重复2

缺点是计算代价很大。

也可以反过来依次减去特征。

