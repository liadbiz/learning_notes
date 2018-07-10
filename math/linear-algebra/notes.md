学习线性代数绝佳材料： [linear algebra done right]
还有配套的[youtube](https://www.youtube.com/playlist?list=PLGAnmvB9m7zOBVCZBUUmSinFV0wEir2Vw)视频。每个视频只有十几分钟，可以


线性代数是研究有限向量空间的线性变换。


injective  内射 一对一变换
surjective  满射  T = L(V, W)  对于任何一个W内的向量，都可以表示称Tv的形式
invertible  可逆

dim(T) = dim NUll(T) = dim RANGE(T)

某个特征值对应的特征向量是原集合的子集

矩阵是用来描述特征变换的 矩阵的第k列是指 Tv_k 在w_1, w_2, ..., w_m表示的坐标。

同一个线性变换不同的bases（基）下的矩阵表示是不同的。

相似矩阵实际上只是同一个线性变换在不同基下的representation.

行列式：

行列式的值实际上就是向量空间的体积，其计算可从二维三维推广而来，二维就是计算面积，三维就是计算体积。也就是base times height.

if we pick one vector, then height is the distance from this vector to the subspace spanned by the remaining vectors, and the base is the (n−1)-dimensional volume of the parallelepiped determined by the remaining vectors.

__体积__的计算相对于每个vector（row或者column）来说具有线性的性质。

spectral theory:

主要思想就是把transformation/operator拆分成简单的块儿，然后单独分析每个块儿。

相似矩阵的特征空间是coincide的，也就是说一个operator的特征多项式是well-defined的。

矩阵对角化就是找一个bases使得operator在改bases下面的对应的矩阵恰好是对角矩阵，当然啦，不是所有的矩阵都可以对角化。

如果有r个不同的特征值，那么对应的特征向量是线性无关的。

能对角化的条件是正好有n = dim V个不同发的特征值

inner product:

A space V together with an inner product on it is called an inner product space.


Normed spaces:

Suppose in a vector space V we assigned to each vector v a number ||V|| such that above properties 1-4 of the norm are satisfied. then the function of v -> ||V|| is a norm. A vector space V equipped with a norm is called a normed space.


Orthogonality:



一个很实际的问题是：如何求在AX=b没有理论解的时候求AX=b的解。也就是$$ \minimize \vert Ax-b \vert  $$，这个问题叫least square method. 该方法可以用于line fitting, curve fitting等等。

isometry and unitary:

an operator is isometry if and only if (X, Y) = (UX, UY) or U*U = I. an operator is unitar if it is invertiable.

an orthogonal transformation is simply a unitary transformation in a real inner product space.  

the singular value decomposition is simply diago- nalization with respect to two different orthonormal bases


矩阵可以表达一个线性变换，也可以表达一个向量在给定基底下的坐标

一个线性变换是可逆的当且仅当是内射（一对一）和满射

一个可逆的线性变化是同构变换，对应的两个空间则是同构空间

算子：从一个向量空间映射到自己的线性变换

在有限维向量空间中，内射和满射是等价的？同时也就可逆。

对偶空间和对偶映射：

首先一个向量空间V上的linear functional是一个从V到F（R或者C）的线性变换。
V的对偶空间V'是治由所有V上的linear functional的集合组成的向量空间。
假设$$ T \in L(V, W)$$，那么对偶空间的$$ T' \in L(W', V')$$，其中$$T'(\phi) = \phi \circ \T$$，which means if $$ \phi $$ 是W上一个linear fractional即$$ \phi \in W'$$，那么$$ \phi \circ \T\$$就是V上的一个linear fractional。

实际上转置矩阵就是对偶变换的矩阵表示。

annihilator($$U^0$$):  subset of V' that map all $$ u \in U $$(U \subset V) to 0

算子部分：

几个重要概念：
+ 算子  将一个有限维向量空间映射到自身的线性变换  即T=L(V,V)一般就表示成L(V)
+ 不变子空间  如果一个空间U是V的子空间，而且for $$ u \in U$$, we have $$Tu \in U$$ 这里是T是V上的一个operator


复空间中一个operator一定有特征值！！
复空间中一个operator的矩阵形式一定有一个是上三角的（取决于选什么bases）


orthogonal的意思就是inner product为0

毕达哥拉斯定理： 如果两个向量是orthogonal的，那么$$ \Vert u+v \Vert_{2} = \Vert u \Vert^{2} + $\Vert v \Vert^{2}$

orthogonal decomposition:  u = cv + w

cauthy-schwarz inequality: $$ \vert \langle u, v \rangle\vert \leq \Vert u \Vert \Vert v \Vert$$
triangle inequality: $$ \Vert u + v \Vert \leq \Vert u \Vert \Vert v \Vert$$

标准正交基(orthonormal bases)：如果一组向量是标准正交基，那么每个向量的norm是1，而且和向量组里面的其他所有向量都是垂直的，即orthogonal的，向量组本身是V的bases。

格拉姆 - 施密特: 正交化过程，将一组base变成orthonomral bases.

每一个有限维内积空间都有一个orthonormal basis。每一组正交向量都可以扩展成正交基。

Schur’s Theorem： 如果V是有限维复空间，T是V上的一个算子，那么一定存在一组正交基是的该算子的矩阵形式是上三角矩阵。












