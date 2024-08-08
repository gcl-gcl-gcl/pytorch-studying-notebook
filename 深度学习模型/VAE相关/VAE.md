# VAE 笔记

## 简介：

VAE的大体作用就是将原有的图片进行特征学习后再输出一张（压缩后的）图片，并且两张图片会尽可能地接近。可以看作是简化并重新生成的过程，也可以有时序降噪等等应用。



## 为什么要用VAE

左图为auto-encoder，右图为VAE

![image-20240807214655983](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240807214655983.png)

我们发现，VAE的主要作用在于加入噪音，故而一个图片对应code的周围都会被影响，从而当我们需要某个中间点的时候，由于中间点需要最小化两个图片的差异，所以他会有两个图的特征。

### 通俗解释

![image-20240807220050137](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240807220050137.png)

上图中阐述了VAE的过程。m1-m3是正常的code，σ1-σ3是噪音。

如果这样去生成，那么σ会取0，这也code直接对应图片会使差距最小，所以我们需要加入限制。

![image-20240807221518511](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240807221518511.png)

直观理由：我们根据函数发现，若想使前面的式子最小那么σ要取到0，此时的噪音值为e^0=1，这样就不会让他的值太小。后面mi^2可以防止过拟合。

### 客观解释

![image-20240808084809988](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808084809988.png)

我们需要生成P(x)的样子，从而通过逐渐生成概率高的图片去把需要生成的图片生成。

#### GMM高斯混合模型

![image-20240808090118426](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808090118426.png)

也就是均值与方差，权重是我们需要提前给定的，然后我们可以根据这些信息去生成一个整体的P（x）图像。

就像我们有很多果汁的具体信息，然后我们就可以通过GMM去生成一个混合后的果汁的总体信息。

![image-20240808090950584](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808090950584.png)

#### VAE

z   是一个标准型的正态分布。

x|z  可以由σ(z)与μ(z)共同生成。代表在z的情况下，x发生的概率。

![image-20240808095001924](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808095001924.png)

通俗理解就是，给你一个群体的营养情况，然后在这个的情况下，普遍人身高分布的概率

![image-20240808093930804](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808093930804.png)

我们如何通过z得到σ与μ呢？可以通过一个神经网络去训练即可。

随后我们就可以得到P（x）

![image-20240808095436671](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808095436671.png)



显然需要训练的是σ与μ函数。我们需要最大化的值是

每个image对应的x，随后代入p（x）取log并相加的L。

![image-20240808100256434](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808100256434.png)

优化：我们得出logP（x）的表示形式。

通过这个优化我们可以更好的衡量Lb与L的增加变化关系，二者一定为正相关。且可以顺带着弱化KL，让KL减小。

![image-20240808103047112](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808103047112.png)



同理，我们拆分Lb，发现需要minimize一个KL

![image-20240808103805343](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808103805343.png)

![image-20240808103552932](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808103552932.png)

从而推导了前面的那个式子。

你说得对，VAE的损失函数可以更具体地写出来，以反映我们在训练过程中实际优化的目标。让我们更详细地写出这个损失函数。

![image-20240808112707892](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808112707892.png)

![image-20240808112725275](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808112725275.png)

![image-20240808112803432](C:\Users\86139\AppData\Roaming\Typora\typora-user-images\image-20240808112803432.png)