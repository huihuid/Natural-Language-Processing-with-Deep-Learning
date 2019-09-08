

### 一、word2vec

#### 1.cbow模型

  <p align="center">
	<img src=./pictures/090701.png alt="Sample"  width="550">
	<p align="center">
		<em> </em>
	</p>


用窗口内目标词的上下文词来预测目标词

**（1）算法是希望计算一个概率![](./pictures/090806.png)，并希望这个概率越大越好**

  <p align="center">
	<img src=./pictures/090802.png alt="Sample"  width="600">
	<p align="center">
		<em> </em>
	</p>
</p>

其中m是窗口尺寸，![](./pictures/090807.png)是最后预测出的一个向量，维度是整个词典的大小，而用上下文预测出其它词的概率$\hat{y_t}^{(i)}=P(w_i|w_{t-m},...,w_{t+m})\  i\neq t$越小越好

引入对数似然函数，然后取反作为损失函数

  <p align="center">
	<img src=./pictures/090810.png alt="Sample"  width="300">
	<p align="center">
		<em> </em>
	</p>

而又![](./pictures/090808.png)是word_target的onehot表示的，因此可以简化写为

  <p align="center">
	<img src=./pictures/090811.png alt="Sample"  width="400">
	<p align="center">
		<em> </em>
	</p>
</p>

SGD的目标函数来达到最大化![](./pictures/090806.png) ，最小化![](./pictures/090812.png)的目标

  <p align="center">
	<img src=./pictures/090813.png alt="Sample"  width="400">
	<p align="center">
		<em> </em>
	</p>
</p>

其中![](./pictures/090809.png)是cbow模型的输出向量，![](./pictures/090807.png)是输出向量经过softmax后的预测向量,![](./pictures/090808.png)是word_target的onehot表示

**（2）概率![](./pictures/090806.png) 的计算方法**

   <p align="center">
	<img src=./pictures/090814.png alt="Sample"  width="500">
	<p align="center">
		<em> </em>
	</p>
</p>

其中![](./pictures/090809.png)是cbow模型的输出向量

在tensorflow中tf.nn.softmax是把一个向量中的元素进行概率分布化，得到的仍是一个与softmax输入维度相同的概率分布向量

```shell
>>> A=[[1.0,2.0,3.0],[1.0,5.0,5.0],[9.0,8.0,7.0]]
>>> with tf.Session() as sess: 
...     print(sess.run(tf.nn.softmax(A)))
... 
[[0.09003057 0.24472848 0.66524094]
 [0.00907471 0.4954626  0.4954626 ]
 [0.66524094 0.24472848 0.09003057]]

```

而数学公式里面的softmax则是输出一个概率值，所以会导致表示不一样

在实际中可以将cbow模型中间层经仿射变换后过一个softmax，得到上下文预测出目标词的概率，用v_t标乘上这个概率向量，就取出了这个概率

  <p align="center">
	<img src=./pictures/090815.png alt="Sample"  width="350">
	<p align="center">
		<em> </em>
	</p>
</p>

举例：x_t为某个单词onehot表示的列向量[0,0,0...,0,1,0,...,0]^T

#### 2.skip-gram模型

  <p align="center">
	<img src=./pictures/090803.png alt="Sample"  width="600">
	<p align="center">
		<em> </em>
	</p>
</p>

用窗口内的目标词来预测上下文词，让预处上下文词的概率极大化，以此来得到目标词的词向量

可以写出目标函数

  <p align="center">
	<img src=./pictures/090801.png alt="Sample"  width="550">
	<p align="center">
		<em> </em>
	</p>
</p>
#### 3.负样本



对朴素的word2vec方法改进

（1）将需要计算全局的softmax改成二分类的sigmoid

![](./pictures/090816.png) 

就变成

![](./pictures/090817.png)

即将多分类改成二分类

（2）同时增加对非窗口词的负采样

目标词的上下文窗口内的词抽样称为正样本，窗口外的词的抽样则为负样本

现在目标变成：最大化目标词窗口内真实单词出现的概率，最小化随机单词出现在目标词周围的概率

目标函数就可以变成：

  <p align="center">
	<img src=./pictures/090818.png alt="Sample"  width="550">
	<p align="center">
		<em> </em>
	</p>
</p>

+ 从窗口外随机抽取k个词作为负样本
+ 对每个词出现的频率进行3/4的幂函数
  + 即每次词抽取的概率为![](./pictures/090819.png)，U(w)为词w的频数
  + 这种幂指数使频率较低的单词也可被更频繁地采样

### 二、glove词向量

共现矩阵

统计规律

用矩阵矩阵变换来模拟统计规律

  <p align="center">
	<img src=./pictures/090804.png alt="Sample"  width="350">
	<p align="center">
		<em> </em>
	</p>
</p>

其中f(x)为权重，当词频过高时，权重不应过分增大，作者通过实验确定权重函数为： 

 <p align="center">
	<img src=./pictures/090805.png alt="Sample"  width="330">
	<p align="center">
		<em> </em>
	</p>
</p>

[如何记忆这个公式](<https://blog.csdn.net/coderTC/article/details/73864097>)
