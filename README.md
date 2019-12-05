# Active Learning

## What is active learning?

active learning (AL) 又称作 "query learning" 或 "optimal experimental design".

> The key hypothesis is that if the learning algorithm is allowed to choose the data from which it learns, it will perform better with less training.    -- 《Active Learning Literature Survey》

![what_is_AL](https://raw.githubusercontent.com/youkenhou/active-learning-note/master/Active%20Learning/1.PNG)

如上图所示，(a)是一个400个instance的数据集，相当于每个点有两个feature，(b)是随机选择30个点进行逻辑回归的训练得到的结果，蓝线代表了决策边界(70%准确率)，(c)是经过active query得到的30个点训练得到的结果(90%准确率)。可以看出少量数据的选择对于网络的训练结果影响很大。

> Active learning systems attempt to overcome the labeling bottleneck by asking *queries* in the form of unlabeled instances to be labeled by an *oracle* (e.g., a human annotator).     -- 《Active Learning Literature Survey》

AL的目的是使用更少的数据去达到更精确的训练结果，从而减少标注瓶颈。

那么如何去选择需要标注的数据呢？



## Scenarios of posing query

主要有三种质询方案：

![query_scenarios](https://raw.githubusercontent.com/youkenhou/active-learning-note/master/Active%20Learning/2.PNG)

### Membership Query Synthesis

### Stream-Based Selective Sampling

从未标注的数据中抽取一个，由learner来决定query还是discard。

如果输入的分布是统一的，那么selective sampling表现会比较好；但如果输入分布不统一或者不清楚，我们可以保证queries还是合理的，因为会有一个潜在的分布。

### Pool-Based Sampling

利用greedy fashion等方法，从未标注的data pool中选取一个最具有信息量的instance进行query。

当内存受限或者处理能力有限时，stream-based selective sampling是一个更合适的方法。



## Query Strategy Frameworks

所有的active learning scenarios都包含了对未标注实例的informativeness进行评估，那如何进行评估？

### Uncertainty Sampling

#### Least Confidence

通俗来说，就是要找到模型给出的confidence最小的instance，因为这样的instance模型最有可能mislabel。

假设unlabeled pool中有3000个instance，要分成10类，那么我们在softmax后得到3000*10的一个结果，选出3000行中每一行的最大值，即模型对每个instance得到的最大confidence的类别。然后将这3000个confidence值从小到大排序，选出前n个instance作为query的结果。

#### Margin Sampling

least confidence的策略只考虑了最有可能的label的信息，而忽略了剩下的label包含的信息。为了修正这一缺陷，研究者使用了一个多类的uncertainty sampling叫做margin sampling。

直观上讲，对于一个instance计算模型推测它最有可能的前两类的confidence之间的差，这个差值越大，说明模型对最有可能的两类分的越清楚，所以差值小的会提供更多的信息来训练模型。

#### Entropy

对于label非常多的数据，margin sampling还是忽略了余下的label的潜在信息。一个更通用可能也是最流行的方法就是entropy策略。

对于二分类问题，entropy退化为上述的least confidence和margin sampling策略。
