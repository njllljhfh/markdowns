# lesson2_传统神经网络

> autor: njl
>
> from：小象学院



### 分类问题：

- illumination：光照
- Deformation：变形
- Occlusion：遮挡
- Cluttered Background：背景杂乱
- Intraclass Variation：细分类（如：黑猫，花猫，波斯猫等）



---

数据集：CIFAR10



---

训练的时间可以通过硬件来提高。

测试的时间希望尽量的少，如果测试时间太长，则该模型的可用性较差。



---

L1距离：曼哈顿距离

L2距离：常规意义上的两点间的直线距离

---

### K近邻算法（K Nearest Neighbor, KNN）

- 对K近邻算法来说，是没有训练过程的，将训练数据输入给模型，就认为模型记住了训练数据（即训练好了）。然后每一步迭代，将验证集中的一条数据加载进来，与从训练集中选出的**"距离最近"**的预测数据做对比，结果一样就是预测对了（即预测的label与ground_truth相同）。
- training的时间复杂度是O(1)。 

---

特征工程

支持向量机

随机森林 

特征空间的转移和改变

---

反向传播

- 链式法则



反向传播，梯度消失？

---

SGD:随机梯度下降 

- 过拟合-应对：
    - Dropout
    - Fine-tuning

- 欠拟合
- [AdamOptimizer](https://www.jianshu.com/p/bf10638b6fa7) :
    -  Adam 算法利用梯度的一阶矩估计和二阶矩估计动态调整每个参数的学习率。TensorFlow提供的tf.train.AdamOptimizer可控制学习速度，经过偏置校正后，每一次迭代学习率都有个确定范围，使得参数比较平稳。
    - 概率论中矩的含义是：如果一个随机变量 X 服从某个分布，X 的一阶矩是 E(X)，也就是样本平均值，X 的二阶矩就是 E(X^2)，也就是样本平方的平均值。



---

鞍点问题：非定点优化问题

- non-convexity, saddle point



