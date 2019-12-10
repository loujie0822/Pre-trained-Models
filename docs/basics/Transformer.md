Transformer内核解析

**1、Multi-Head Attention和Scaled Dot-Product Attention：**

本质是self attention通过attention mask动态编码变长序列，解决长距离依赖、无位置偏差、可并行计算；

- 为什么是缩放点积，而不是点积模型？ 
  - 当输入信息的维度 d 比较高，点积模型的值通常有比较大方差，从而导致 softmax 函数的梯度会比较小。因此，缩放点积模型可以较好地解决这一问题。
- 为什么是双线性点积模型（经过线性变换Q K）？ 
  - 双线性点积模型，引入非对称性，更具健壮性（Attention mask对角元素值不一定是最大的，也就是说当前位置对自身的注意力得分不一定最高）。
- 相较于加性模型，点积模型具备哪些优点？ 
  -  [Transformer.md](Transformer.md) 常用的Attention机制为**加性模型**和**点积模型**，理论上加性模型和点积模型的复杂度差不多，但是点积模型在实现上可以更好地利用矩阵乘积，从而计算效率更高（实际上，随着维度d的增大，加性模型会明显好于点积模型）。
- 多头机制为什么有效？ 
  - 类似于CNN中通过多通道机制进行特征选择；
  - Transformer中先通过切头（spilt）再分别进行Scaled Dot-Product Attention，可以使进行点积计算的维度d不大（防止梯度消失），同时缩小attention mask矩阵。

**2、Position-wise Feed-Forward Networks：**

- FFN 将每个位置的Multi-Head Attention结果映射到一个更大维度的特征空间，然后使用ReLU引入非线性进行筛选，最后恢复回原始维度。
- Transformer在抛弃了 LSTM 结构后，FFN 中的 ReLU成为了一个主要的提供非线性变换的单元。

**3、Positional Encoding：**

将Positional Embedding改为Positional Encoding，主要的区别在于Positional Encoding是用公式表达的、不可学习的，而Positional Embedding是可学习的（如BERT），两种方案的训练速度和模型精度差异不大；但是Positional Embedding位置编码范围是固定的，而Positional Encoding编码范围是不受限制的。

- 为什么引入 和 建模Positional Encoding？



- 引入 和 是为了使模型实现对相对位置的学习，两个位置 pos 和 pos+k 的位置编码是固定间距k的线性变化：

  

- 可以证明：**间隔为k的任意两个位置编码的欧式空间距离是恒等的，只与k有关**。

 