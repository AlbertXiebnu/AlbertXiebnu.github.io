---
layout: post
title: "Use IPython to Debug"
modified:
categories: 
excerpt:
tags: []
image:
  feature:
date: 2015-05-19T16:53:17+08:00
---

最近学习了下python，用python写了一个线性回归的程序，因为对numpy的api不是很熟练，所以写出来的程序发现有bug。之前没有python程序的调试经验，虽然可以打印变量，但是个人觉得还是没有单步调试来的方便。于是就自己折腾了下IPython的调试功能。特此记录一下。

首先打开terminal，进入ipython，这里有两个文件，其中linearRegression.py就是我们需要调试的程序。

<figure>
	<a href="http://cdn1.snapgram.co/imgs/2015/05/19/screenshot.png">
		<img src="http://cdn1.snapgram.co/imgs/2015/05/19/screenshot.png">
	</a>
</figure>

首先把我们需要调试的代码展示出来：
{% highlight python linenos%}
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

运行%run -d linearRegression.py进入调试模式
<figure>
	<a href="http://cdn2.snapgram.co/imgs/2015/05/19/2015-05-195.48.21.png"><img src="http://cdn2.snapgram.co/imgs/2015/05/19/2015-05-195.48.21.png">
	</a>
</figure>

输入b 36 在36行处设置断点，输入c继续执行到断点处，输入s表示step into，进入函数。
<figure>
	<a href="http://cdn1.snapgram.co/imgs/2015/05/19/2015-05-195.55.06.png"><img src="http://cdn1.snapgram.co/imgs/2015/05/19/2015-05-195.55.06.png"></a>
</figure>

一直单步调试，直到运行到22行，我们可以输入p derivative打印变量derivative
<figure>
	<a href="http://cdn2.snapgram.co/imgs/2015/05/19/2015-05-196.09.45.png"><img src="http://cdn2.snapgram.co/imgs/2015/05/19/2015-05-196.09.45.png"></a>
</figure>

输入?，查看所有可用的命令。输入q退出调试
<figure>
	<a href="http://cdn1.snapgram.co/imgs/2015/05/19/2015-05-196.12.28.png"><img src="http://cdn1.snapgram.co/imgs/2015/05/19/2015-05-196.12.28.png"></a>
</figure>
