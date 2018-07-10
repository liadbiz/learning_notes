NumPy是python中做科学计算的基础性工具包，对于想要从事数据分析方面工作的同学可以说是必备技能。

NumPy的核心是`ndarray`对象，对应中python中`sequence`，是对n维同质数组的一层封装。因此在NumPy中所有的操作都是基于`ndarray`对象，而且这些操作都是预编译`c`代码，因此在速度上相比python代码来说快很多，这大概也是为什么叫`array`的原因吧。

我们来看一个基本的例子来感受一下二者在速度上的差异。

```bash
In [3]: my_arr = np.arange(100000)

In [4]: my_list = list(range(100000))

In [6]: %time for _ in range(10): my_arr2 = my_arr*2
CPU times: user 2.21 ms, sys: 2.04 ms, total: 4.24 ms
Wall time: 3.71 ms

In [7]: %time for _ in range(10): my_list2 = [x*2 for x in my_list]
CPU times: user 64.8 ms, sys: 8.7 ms, total: 73.5 ms
Wall time: 72.9 ms
```

可以看出，在对十万量级的数据进最基本的操作时，速度相差有20倍之多。因此在实际的大数据分析中，使用numpy带来的速度上的提升可见一斑。

细心的同学可能会注意到，上面的例子中，我们做的操作是将每个元素都变成两倍，在python中，我们可以通过列表推导来实现，而在numpy中，我们可以通过更简单的`my_arr2 = my_ayy*2`来实现。

实际上，使用numpy最重要的好处是：

+ 通过广播（如果还不理解广播是什么意思不要紧，后面会提到更多）操作将需要用循环来实现的操作变成`ndarray`的之间的操作，让代码更简洁，当然同时意味着更少的bug。我们把这叫做向量化，试想和数学中的向量操作是不是很相似？
+ 通过预编译的c代码提升效率

这样我们在拥有简介语法的同时，又拥有极高的效率！可以说是鱼与熊掌兼得了！


