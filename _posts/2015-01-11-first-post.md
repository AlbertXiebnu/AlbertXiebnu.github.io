---
layout: post
title: "First Post"
modified:
categories: 
excerpt:
tags: []
comments: true
author: xiexiaobo
image:
  feature: sample-image-4.jpg
date: 2015-01-11T15:37:07+08:00
---
## contain the most water

这个题最初理解错了意思，然后想了个O(n2)的算法，超时。最后把这个题和58同城的面试题联系起来了，写到一半发现思路错了。

后来的思路就是对所有高度排序，先找到最大的两条边，求出容积。然后再找第三高的边，如果这边落在区间的中间，则不考虑，若落在区间的外边，则计算新的容积，和最优解比较，如果更优，则更新最优解，同时更新区间。这个思路提交上去，答案是错的。

错误的原因在于，当更新了区间时，原来的某个边界即不能再作为解的边界。但是有可能这条边就是最优解的边界之一。因此会遗漏解。
正确的解法是看了别人的代码，好简单啊尼玛，怎么没有想到！！！

思路和我的相反，先取最左边和最右边作为边界，计算容积。然后每次挑选最矮的边进行拓展，试图提高最矮的边，虽然区间范围变小，但是最矮的边提高，也有可能增加容积。

为什么正确？

首先，最长的区间只有一个，即初始解。接下来考虑区间长度n-1的情况，此时有两种情况，一种是1- n-1, 另一种是2-n。此时的最优策略肯定是移动最短的边。因为不管移动左边还是右边，区间的长度已经定了。如果移动最短边，则最短边可能会增大，如果移动最长边，则最短边肯定会下降或不变，因此总的容积减小。

{% highlight c linenos%}
int maxArea(vector<int>& height){
    int len=height.size();
    int left=0;
    int right=len-1;
    int water=0;
    while(left<right){
        int s=min(height[left],height[right])*(right-left);
        if(water<s) water=s;
        if(height[left]<height[right]){
            left++;
        }else{                                                                                               
            right--;                                                                                
        }
        return water;
    }
}
{% endhighlight %}
