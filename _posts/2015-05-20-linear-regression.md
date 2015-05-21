---
layout: post
title: "Linear Regression"
modified:
categories: 
excerpt: This post mainly covered two approaches for solving Linear Regression, one is normal equations, another is gradient descent. 
tags: [machine learning]
image:
  feature: sample-image-5.jpg
date: 2015-05-20T16:08:42+08:00
---

谈到线性回归，我们脑海中总是会浮现出这样一幅画面：
<figure>
    <a href="http://cl.ly/image/3H3k2Z0C1E3g/lr.png">
        <img src="http://cl.ly/image/3H3k2Z0C1E3g/lr.png"/>
    </a>
</figure>
没错，这个是线性回归最简单的情形。横轴表示自变量x，纵轴表示应变量y，我们试图找到一个直线，使得图上的所有点，到直线的垂直距离的平方和最小。还是用数学表示来得准确些。我们假设直线的方程为$$w_1x+w_0=0$$。每个点$$x_i$$在直线上的值就是$$w_1x_i+w_0$$。因此，所有点的垂直距离平方和可以表示为:

$$\begin{aligned}
J(\mathbf{w})=\frac{1}{2m}\sum_{i=1}^m(\mathbf{w}^{\mathbf{T}}\mathbf{x}_i-y_i)^2
\end{aligned}$$

这里的$$\mathbf{w}=(w_0,w_1)$$是列向量，$$\mathbf{x}_i=(1,x)$$也是列向量。上面的公式不仅适用于二维的情形，对于N维的情况，上式也同样成立。此时有$$\mathbf{w}=(w_0,w_1,\dots,w_n)$$和$$\mathbf{x}=(1,x_1,x_2,\dots,x_n)$$成立。

线性回归的目标是找出一个向量$$\mathbf{w}$$使得$$J(\mathbf{w})$$的值最小。你既可以把$$J(\mathbf{w})$$看成是多元函数求极值的问题，这样我们就可以采用梯度下降的方法来求解线性回归问题了。同样地，如果把向量$$\mathbf{w}$$简单地看成是一个变量，通过矩阵的偏微分运算，对$$J(\mathbf{w})$$对$$\mathbf{w}$$求偏导，令结果等于0，则可以直接解出最优的$$\mathbf{w}$$，这种方法也叫做正规方程法。我们详细讨论下这两种不同的解法。

#### 正规方程法
首先，我们把成本函数$$J(\mathbf{w})$$改写成矩阵的形式：

$$
\begin{align}
	J(\mathbf{w}) & = \frac{1}{2m}\sum_{i=1}^m(\mathbf{w}^{\mathbf{T}}\mathbf{x}_i-y_i)^2 \\\
	&= \frac{1}{2m} \sum_{i=1}^{m}(\mathbf{x}_i^{\mathbf{T}}\mathbf{w}-y_i)^2 \\\
	&= \frac{1}{2m}\begin{Vmatrix}
		\mathbf{x}_1^{\mathbf{T}}\mathbf{w}-y_1 \\
		\mathbf{x}_2^{\mathbf{T}}\mathbf{w}-y_2 \\
		\cdots \\
		\mathbf{x}_m^{\mathbf{T}}\mathbf{w}-y_m
	\end{Vmatrix}^2 \\\
	&=\frac{1}{2m}{\left\| \begin{bmatrix} -\mathbf{x}_1^{\mathbf{T}}- \\ -\mathbf{x}_2^{\mathbf{T}}- \\ \cdots \\ -\mathbf{x}_m^{\mathbf{T}}- \end{bmatrix}\mathbf{w}-\begin{bmatrix} y_1\\y_2 \\ \cdots \\y_m \end{bmatrix}  \right\|}^2 \\\
	&=\frac{1}{2m}{\left\| \mathbf{Xw}-y \right\|}^2
\end{align}
$$

接下来，我们把平方展开，得到：

$$
\begin{align}
	J(\mathbf{w}) &= \frac{1}{2m}{\left\| \mathbf{Xw}-y \right\|}^2 \\\
				  &= \frac{1}{2m}(\mathbf{w^T X^T Xw}-2\mathbf{w^T X^T}y+y^{\mathbf{T}}y) \\\
				  &=\frac{1}{2m}(\mathbf{w^T A w}-2\mathbf{w^T B}+\mathbf{C})
\end{align}
$$

然后，我们对向量$$\mathbf{w}$$求偏导，这里涉及到了一些矩阵微分的一些运算法则，以后有时间我再研究下矩阵微分方面的知识。现在我们简单地当成是普通变量的求导，于是我们得到如下结果：

$$
\begin{align}
	\nabla J(\mathbf{w})=\frac{1}{2m}(2\mathbf{Aw}-2\mathbf{B})
\end{align}
$$

令$$\nabla J(\mathbf{w})=0$$，我们得到$$\mathbf{Aw}=\mathbf{B}$$，其中$$\mathbf{A}=\mathbf{X^T X}$$，$$\mathbf{B}=\mathbf{X^T}y$$。把它们的定义代入上式得到：$$\mathbf{X^T Xw}=\mathbf{X^T}y$$。当且仅当$$\mathbf{X^T X}$$的逆存在时，我们得到$$\mathbf{w}$$的解为：

$$
\begin{align}
	\mathbf{w}=(\mathbf{X^T X})^{-1}\mathbf{X^T}y
\end{align}
$$

上式中的$$(\mathbf{X^TX})^{-1}\mathbf{X^T}$$也称为伪逆，记作$$\mathbf{X}^{\dagger}$$。最小二乘法只有在$$N>>d$$的情况下才适用。对于$$N\approx d$$或者$$N < d $$的情况，$$(\mathbf{X^T X})^{-1}$$可能是奇异矩阵。这样线性回归的结果就会出现过拟合。岭回归可以解决传统线性回归出现的问题，准备在之后的博客中讨论下岭回归算法。

我们可以用python和numpy快速地实现线性回归的正规方程的算法。代码如下所示。

{% highlight python linenos%}
# -*- coding: utf-8 -*-
import numpy as np
import pylab as plt

def psudoInverse(X):
    inv=np.linalg.inv(np.dot(X.T,X))
    psudoInv=np.dot(inv,X.T)
    return psudoInv

def linearRregression(theta,X,Y):
    psudoInv=psudoInverse(X)
    return np.dot(psudoInv,Y)

#prepare data

x=np.arange(1,11,0.5)
theta0=2
theta1=3
y=theta0+theta1*x+np.random.randn(len(x))

#train linear regression
bias=np.ones(len(x))
X=np.vstack((bias,x)).T
theta=np.zeros(2)
theta=linearRregression(theta,X,y)

plt.scatter(x,y)
plt.plot(x,theta[0]+theta[1]*x)
plt.xlim(0,11)
plt.show()
{% endhighlight %}

在命令行下运行上面的程序，得到如下图的结果。
<figure>
    <a href="http://cl.ly/image/2j103l1e1H0Y/Image%202015-05-21%20at%203.36.04%20%E4%B8%8B%E5%8D%88.png">
        <img src="http://cl.ly/image/2j103l1e1H0Y/Image%202015-05-21%20at%203.36.04%20%E4%B8%8B%E5%8D%88.png"/>
    </a>
</figure>

#### 梯度下降法
如果把$$J(\mathbf{w})$$看成是多变量的函数时，我们可以采用梯度下降算法计算最优的参数向量$$\mathbf{w}$$，使得成本函数最小。梯度下降算法的思路很直观，每次更新我们都沿着每个维度的梯度下降的方向更新参数，直到算法收敛。每个参数的更新规则如下：

$$
\begin{align}
	\mathbf{w}_i &=\mathbf{w}_i-\alpha\frac{\partial J(\mathbf{w})}{\partial\mathbf{w}_i} \\\
	&=\mathbf{w}_i-\frac{\alpha}{m}\sum_{i=1}^m (\mathbf{w^T}\mathbf{x}^{(i)}-y^{(i)})x_j^{(i)}
\end{align}
$$

需要注意的是，我们需要同时更新$$\mathbf{w}_i$$。也就是说，在每次更新时，必须用上一次迭代时得到的$$\mathbf{w}$$，当所有的$$\mathbf{w}_i$$在这次迭代更新完成后，得到新的$$\mathbf{w}$$向量，并用于下一次的迭代，直到算法收敛。

我们同样用python简单地实现了下梯度下降算法。代码如下：

{%highlight python linenos%}
import numpy as np
import pylab as plt
def hypothesis(theta,X):
    return np.dot(X,theta)

def costfunction(theta,X,y):
    hyp=hypothesis(theta,X)
    m=X.shape[0]
    cost=sum((hyp-y)**2)/(2*m)
    return cost

def gradientDescent(theta,X,y,alpha=0.02,iteration=100):
    thetatemp=np.zeros_like(theta)
    m=X.shape[0]
    for i in range(iteration):
        cost=costfunction(theta,X,y)
        print("iter %d: %f" %(i,cost))
        diff=hypothesis(theta,X)-y
        for i in range(len(theta)):
            derivative=sum(diff*X[:,i])/m
            #print derivative
            thetatemp[i]=theta[i]-alpha*derivative
        theta=thetatemp
    return theta

# prepare data 
x=np.arange(1,11,0.5)
theta0=3
theta1=2
y=theta0+theta1*x+np.random.randn(len(x))

# train a linear odel
bias=np.ones(len(x))
X=np.vstack((bias,x)).T
theta=np.zeros(2)
theta=gradientDescent(theta,X,y,iteration=200)

plt.scatter(x,y)
plt.plot(x,theta[0]+theta[1]*x)
plt.xlim(0,11)
plt.show()
{% endhighlight %}

梯度下降算法中学习率$$\alpha$$的选择非常重要，通常太大的学习率会导致算法不收敛，使得成本函数越来越大。而太小的学习率则导致收敛比较慢。我们一般根据经验和实际尝试来选择合适的$$\alpha$$。

最后，我们在terminal上运行上面的程序：python linearRegression.py，得到如下图的结果。

<figure>
    <a href="http://cl.ly/image/1N122I0G1Y0p/Image%202015-05-21%20at%203.32.37%20%E4%B8%8B%E5%8D%88.png">
        <img src="http://cl.ly/image/1N122I0G1Y0p/Image%202015-05-21%20at%203.32.37%20%E4%B8%8B%E5%8D%88.png"/>
    </a>
</figure>

#### 方法对比

| 对比维度 | 正规方程法 | 梯度下降法 |
|:--------|:-------:|:--------:|
| 参数调优 | 没有参数需要调优 | 需要调整学习率$$\alpha$$ |
|----
| 增量训练   | 不能增量训练 | 批量梯度下降可以从前一次训练的参数作为初始值，进行增量训练 |
|----
| 训练样本维度 | 当特征维度大于10万时，矩阵求逆的运行非常慢 | 可以适用于特征维度很大的训练 |
|----
| 迭代 | 一次计算即可 | 需要多次迭代 |
|=====
{: rules="groups"}

最后，当样本特征维数大于样本数时，或者当样本中存在很多线性相关的样本时，$$(\mathbf{X^TX})^{-1}$$可能不存在。此时正规方程法就失效了。但是我们仍然可以用梯度下降算法来计算。

#### 参考
* 机器学习的基石，第九讲。`https://class.coursera.org/ntumlone-002/lecture`。