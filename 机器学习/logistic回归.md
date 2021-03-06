# Logistic回归

当我们使用线性回归的时候，我们有$y = w_1x_1 + w_2x_2 + \cdots + w_dx_d$，但是我们可以把它应用在衍生模型中，即我们假设有$g(x)$使得$y = g(w_1x_1 + w_2x_2 + \cdots + w_dx_d)$ 。

特 别的，当我们在处理二分分类问题的时候，我们需要变量$x_i$产生输出为0和1的预测值，即我们需要
$$
g(z)=
\begin{cases}
0& z < 0  \\
0.5 & z = 0\\
1& z  > 0
\end{cases}
$$
其中，$z=w_1x_1 + w_2x_2 + \cdots + w_dx_d$即，我们需要这样的**阶跃函数**来产生二分预测值。

但是这样的阶跃函数在程序上很难实现，于是我们使用了如下**Sigmoid**函数：
$$
y = \frac{1}{1 + e^{-x}}
$$
此函数在$x = 0$的时候变化很陡，能近似的替代上述阶跃函数。其中$z=w_1x_1 + w_2x_2 + \cdots + w_dx_d$。

为使得尽量多的点满足上述表达式，我们使用**最大似然估计**来估计w和b。

上述y是分布在$[0, 1]$区间，设$p(y=1|x) = \pi(x) =  \frac{1}{1 + e^{-wx-b}}$ ，则$p(y=0|x) = 1 - \pi(x) = \frac{1}{1 + e^{wx + b}}$，即我们可以把y看作$y=1$的概率，于是对于任意$x_i, y_i$有

$$
p(y_i|x_i) = p(y=1|x_i) ^ {y_i} p(y=0|x_i) ^ { (1-y_i)}
$$
对于所有样本的似然函数有：
$$
\Pi(w, b) = \prod_{i=1}^{N}p(y=1|x_i)^{y_i}p(y=0|x_i)^{1 - y_i}
$$


对于所有样本的概率我们可以写成
$$
l(W) = \sum_{i=1}^{m} p(y|i|x_i)
$$
由于$p(y=1|x)$包含求幂操作，因此我们可以取其对数似然来作为它的近似概率值，我们令$\pi(x) = p(y=1|x)$，于是我们的**对数似然**可以表示为：
$$
lnL(W) = \sum_{i=1}^{m}y_iln[\pi(x_i)] + (1-y_i)ln[1-\pi(x_i)]
$$
对上式对W进行求导，可以得到
$$
\frac{\Phi lnL(W)}{\Phi W} =\sum_{i=1}^{m}\{\frac{y_i}{\pi(x_i)}[\pi(x_i)]' - (1 - y_i)\frac{[\pi(x_i)]'}{1 - \pi(x_i)}\} \\
= \sum_{i=1}^{m}[\frac{y_i}{\pi(x_i)} - \frac{1-y_i}{1-\pi(x_i)}]\pi(x_i)' \\
= \sum_{i=1}^{m}(y_i - \pi(x_i))\frac{\pi(x_i)'}{pi(x_i)(1-\pi(x_i))} \\
= \sum_{i=1}^{m}x_i(y_i - \pi(x_i)) \\
= X^T(Y - \overline Y)
$$
其中$X = (x_1, x_2, \cdots, x_d)$，$\overline Y$表示使用$\pi(x)$计算得到的值，$Y = (y_1, y_2, \cdots, y_d)$表示实际分类值。

要计算上述方程的极大极小值比较困难，工程上普遍使用**梯度下降法**或者**牛顿法**来无限接近这个局部极大极小值点。



## 梯度下降法 

我们先假定一个初始值，然后从一个方向去逼近极值点，当迭代指定次数或者极值不在变化时，我们停止迭代，并把上次计算的值作为极值点返回，这就是梯度下降法。

根据泰勒级数，我们知道$y_{k + 1} = y_k + \Delta x y_k'$，要是$y_{k + 1} < y_k$，那么我们可以让$\Delta x = -\lambda y_k'$，其中$\lambda$为大于0的常数

很明显，当我们沿着$l(W)​$的导数方向逼近时，效率最佳

对于上面的例子，我们需要的是梯度上升法，这时候我们需要$\Delta x = \lambda y_k'$即可，我们假定t次迭代的W值为$W_k$，那么我们的迭代方程可以写成：
$$
W_{k+1} = W_k + \alpha\frac{\Phi lnL(W)}{\Phi W} = W_k + \alpha X^T(Y - \overline Y)
$$



## 小批量梯度下降

如果我们使用小批量梯度下降，那么我们可以将上式写成如下：
$$
W_{k+1} = W_k + \alpha\frac{\Phi lnL(W)}{\Phi W} = W_k + \alpha \frac{1}{m}X^{mT}(Y^m - \overline Y^m)
$$
m为每次小批量下降的样本数目，一般为32，64，128等，$\frac{1}{m}$表示取m次迭代的平均值

### 对数损失函数

对于logistic回归来说，我们一般使用对数损失函数来评估其精确度。
$$
cost = \frac{1}{N}\sum_{i = 1}^{N}[-y_ilogh(x_i) - (1 - y_1)log(1- h(x_i))]
$$
