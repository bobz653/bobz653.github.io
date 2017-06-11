---
layout: post
title: "浅析RNN(LSTM)网络结构【附源码】"
description: "LSTM basic "
author: "张波"
category: 机器学习
tags: [machine learning]
---

前一篇博文讨论了深度学习基础和一些数学知识，比如**Word2vec**，为啥要选mikolov的word2vec，虽然这个模型还不是深度网络，但包含了多次LR的参数训练，这个过程我觉得就是所有神经网络的算法基础。目前深度学习用的比较多的是CNN，RNN。在图像中用CNN比较多，因为CNN中的filter+pooling可以在图片各种位置空间上学习出多种多样的特征。在时序信息（*信息是有关时间和序列的*）中用的比较多的是RNN，比如语音，NLP，以及时序信息。LSTM是RNN中的用的比较多的网络，看了许多资料现在算是明白一些了。本文也分3个部分，最后会附上样例代码，现在开始从底层分析LSTM的结构吧。

* 模型分析：RNN到LSTM，结构和参数分析
* 模型应用：预测（分类、序列Seq2Seq）
* 安装和源码：注意的问题

## 模型分析
Word2vec的无监督学习可以学习到当前词周边的语义，似然函数是$$ P(W_i \| context_wi) = -log(($$ $${ exp (W^tx)} \over {\Sigma^V_1 exp(W^ix)}$$$$ )) $$,损失函数使用交叉熵，使得似然函数最大，当然这里似然函数取了负值。当然说是无监督学习，里面用的是监督。这里在训练的时候参数不断的更新，其实这已经像RNN了，上次的参数可以继承。

* RNN：在RNN序列信息学习中，目标是分类或者是序列。这里训练数据需要上一时刻的隐藏层参数$$ HiddenLayer_t $$ 即为 $$ H_t $$，注意这个参数是列向量，里面是数值类型。这里向量的合并可以理解成相加，类似word2vec那样。![RNN结构图](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-SimpleRNN.png)
* LSTM：RNN的问题就在于直接进行状态相加，太粗暴，变化也太剧烈，容易把之前的状态覆盖掉。那么我们可不可以改变RNN这种粗暴的方式，把状态记住呢，这里就有了long term memory,有了存储这个向量的空间，同事我们训练的时候，从个里面拿出有用的memory和当前的memory构成short memory传输给输出层，或者下一层，如下图：![LSTM结构图](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-chain.png)，需要注意的是图中的叉是point-wise点乘，不是点积。这里有4个参数运算要进行，从左边到右边，依次是
 1. 第一个参数运算**Forget gate:** $$ f_t = \sigma(W_f(h_{t-1},X_t)+bias) $$
 2. 第二，三个参数运算**Input gate & candidate memory:** $$ f_i = \sigma(W_i(h_{t-1},X_t)+bias) * tanh(W_c(h_{t-1},X_t)+bias)$$
 3. 形成长期记忆**Long term meory**： $$C_t = f_t + f_i $$
 4. 第四个参数运算**Ouput Gate**输出到下一层： $$ h_t = \sigma(W_o(h_{t-1},X_t)+bias) * tanh(C_t) $$
 
完整的模型分析就是这四步。可以通过链式求导推导出参数更新，和LR并无不同，比较繁琐具体的推导过程就不写了。

## 模型应用
在机器学习中，只有2种任务，预测任务（分类，回归）、描述任务（聚类，关联模式，异常检测）。我们LSTM经常用来解决分类或者回归任务，常用的分类任务，比如情感分类，回归任务比如速度预测等，LSTM通过是作用底层的语义向量层，然后上面会级联分类模型（LR、softmax），或者回归模型（广义回归（LR））。模式都差不多，对于LSTM来说

* 如果最后用的是LSTM比如历史N个时刻的向量做Max pooling 或者mean pooling,一般这是分类任务。![分类图](http://www.th7.cn/d/file/p/2016/02/15/36970b182f7ce34b223ac79443fafdb2.jpg)
* 如果最后用的是每个LSTM时刻的向量，通常这是序列标注任务。![序列标注图](http://pic.w2bc.com/upload/201703/10/201703101724089047.jpg) 这是就类似咱们通常说的**Seq2Seq**。

## 安装和源码
网上有很多相关的源码，很容易获取，这里我给个基本安装配置,源码是做情感分类。

1. 版本兼容问题
> <pre>import keras 
> print keras.__version__
> import tensorflow
> print tensorflow.__version__
> </pre> 我机器上的keras版本是2.0.4,tensorflow版本是1.1.0，注意keras默认的后端是TensorFlow，因为这个作者本来也是tensorflow的作者，所以当然用自己的东西啦，Theano目前来看已经式微了。版本一定使用最新的，不然会出现model.add()的时候报错哦。如果要安装新的记得
> <pre>pip uninstall keras/tensorflow</pre>

2. 网络参数个数
> 代码中通过model.summary()可以看到参数数量的多少，我这里可以给大家算一下，这样基本就知道网络模型是个什么样子。这里有词汇库14322个，embedding层是256维度，那么embeddign参数是14322*256,LSTM层的参数是4次计算，每次有个偏置，还有连接层的参数，所以参数为256 * 128 + 128 * 129 = 197120.明白参数的个数对模型了解很重要。
> <pre>model = Sequential()
model.add(Embedding(14322, 256, input_length=maxlen))
model.add(LSTM(128))
model.add(Dropout(0.5))
model.add(Dense(1))
model.add(Activation('sigmoid'))
model.compile(loss='binary_crossentropy',
optimizer='adam',
metrics=['accuracy'])</pre>
 
3.运算时间 
> 目前15000个句子，词汇量是14322，batchsize为128，我机器比较弱，一次epoch大概需要3分钟，30次epoch训练完毕需要1个半小时。我没有GPU，所以比较慢了。

源码见我的GITHUB：[https://github.com/bobz653/machine-learning/tree/master/emotion_category_lstm](https://github.com/bobz653/machine-learning/tree/master/emotion_category_lstm)

PS： **要指出的是网络上有篇传播很广的[博文](http://blog.csdn.net/Dark_Scope/article/details/47056361)，里面提到了LSTM的结构，说的相当不清楚，大家不要去看。**

## 参考
LSTM相关文章：

* [http://www.jianshu.com/p/9dc9f41f0b29](http://www.jianshu.com/p/9dc9f41f0b29)
* [http://blog.echen.me/2017/05/30/exploring-lstms/](http://blog.echen.me/2017/05/30/exploring-lstms/)
* [http://kexue.fm/archives/3863/](http://kexue.fm/archives/3863/)
* [http://blog.csdn.net/malefactor/article/details/50725480](http://blog.csdn.net/malefactor/article/details/50725480)
