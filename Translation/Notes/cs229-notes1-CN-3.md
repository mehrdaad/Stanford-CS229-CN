原文：http://cs229.stanford.edu/notes/cs229-notes1.pdf  
翻译：[MIL Learning Group](https://github.com/milLearningGroup/Stanford-CS229-CN) - [San-greal](https://San-greal.github.io)

# Part III 广义线性模型 (Generalized Linear Models)

到目前为止，我们已经看到了一个回归和一个分类的例子。在这个回归的例子中，我们有 $y|x$ $～$ $N$($\mu$,$\sigma^{2}$),而在这个分类的的例子中，有 $y|x$；$\theta$  $～$ 伯努利 (Bernoulli)($\phi$) ，对于 $\mu$ 和 $\phi$ 的一些适当的定义可以作为函数的 $x$ 和 $\theta$  。在这一节中，我们将展示这两种方法的更为广泛的家族模型的特殊情况，其称为广义线性模型 (GLMS)。我们还将教授如何使用在GLM家族中的其他模型，并将他们应用于其他的分类与回归问题



## 8 The exponential family

​    为了进一步了解GLMS，我们先说一下指数分布，我们定义的一个分布如果是指数分布，则他可以被写为以下形式： 

​                                                                    $p(y;\eta) = b(y)\exp(\eta^{T}T(y) - a(\eta))$                                                    $(6)$

该式子中的 $\eta$ 叫做**特性参数** (也叫做**典型参数**) ；$T(y)$ 是**充分统计量 **(通常情况下 $T(y) = y$) ；而 $a(\eta)$ 是**对数划分函数**。分量 $e^{-a(\eta)}$ 的作用是归一化参数，确保 $p(y;\eta)$ 的和是大于1的。

​     $T$ 是一个复杂的选择，$a$ 和 $b$ 定义一个关于参数 $\eta$ 的分布族；当我们改变 $\eta$ 的值时，我们在这个分布族中能得到不同的分布。

​    现在看到的伯努利 (Bernoulli) 和高斯 (Gaussian) 分布都是指数分布的一个例子。伯努利 (Bernoulli) 分布均值为 $\phi$ 写为 Bernoulli( $\phi$ )，指定一个分布 $y \in {0,1}$ ，写成 $p (y = 1；\phi) = \phi ；p (y = 0；\phi) =1 - \phi$ 。

​    当我们改变 $\phi$ 的值，我们得到不同均值的伯努利 (Bernoulli)分布。现在我们证明这一类的伯努利 (Bernoulli) 分布，在例子中选择 $T$ ，$a$ ，$b$ 所以式子(6)是伯努利 (Bernoulli)分布。

我们把伯努利 (Bernoulli) 分布写为：

​	$p(y；\phi) = \phi^{y}(1 - \phi)^{1 - y}$ 

​	               $ = \exp(ylog \phi + (1 - y)log(1 - \phi))$ 

​	               $ = \exp((log\frac{\phi}{1 - \phi})y + log(1 - \phi))$ 

因此，特性参数由 $\eta = log(\phi/(1 - \phi))$ 给出。有意思的是，当我们使用 $\eta$ 表示 $\phi$ 的时候我们会得到 $\phi = 1/(1 + e^{-\eta})$ 。这看起来和S型 (sigmoid) 函数很像。当我们把逻辑回归分析看作GLM时这将会再次被提及。为了完成伯努利 (Bernoulli) 分布是指数分布的一种猜想，我们进一步得到：

​	$T(y) = y$ 

​         $a(\eta)  = -\log(1 - \phi)$ 

​	          $ = \log(1 + e^{\eta})$ 

​	 $ b(y) = 1$ 

这表明选择适当的 $T$ $a$ 和 $b$ 的时候，伯努利(Bernoulli)分布乐意被写为(6)的形式。我们进一步来讨论高斯(Gaussian)分布。回想一下，当我们推导线性回归时，$\sigma^{2}$ 的值对我们最终选择的 $\theta$ 和 $h_{\theta}(x)$ 没有任何影响。因此我们可以选择任意值作为 $\sigma^{2}$ 而不去改变他的值。为了简化式子，我们定义 $\sigma^2 = 1$ 。我们可以得到：

​	$p(y；\eta) = \frac{1}{\sqrt{2π}}\exp(-\frac{1}{2}(y - \mu)^{2})$ 

​	               $ = \frac{1}{\sqrt{2π}}\exp(-\frac{1}{2}y^{2})*exp(\mu y - \frac{1}{2}\mu^{2})$

因此，我们得出高斯 (Gaussian) 分布也在指数分布族里面，当

​	$ \eta = \mu$

  $T(y) = y$

  $a(\eta) = \mu^{2}/2$                                         

​	   $ = \eta^{2}/2$

   $b(y) = (1/\sqrt{2π})\exp(-y^{2}/2)$ 

这有许多其他分布指数的成员：多项分布 (我们稍后会遇见) ，在泊松分布(造型计算数据；也看到问题集)；伽马和指数(造型连续，非负随机变量，如时间间隔)， $\beta$ 和狄利克雷 (对概率分布) ，以及更多。在下一节中，我们将描述构建模型的一般“食谱''，$y(x 和 \theta)$ 来自这些版本。     

## 9 构建广义线性模型 (Constructing GLMs)

​	假设你想要构建一个模型用来估计将来的任何时候客户到你商店的人数 $y$ (或者你网站页面的访客数) ，而该模型基于某些特性 $x$ 如商店促销，最近的广告，天气或者读写等方面。我们了解到泊松分布 (Poisson distribution) 通常为参观者数量提供了一个很好的模型。那么当我们知道这一点后，我们如何对我们的问题设计一个模型呢？幸运的是泊松分布 (Poisson distribution) 是一个指数分布族，所以我们是可以应用广义线性模型 (GLMS)来解决我们的问题。因此，接下来我们将在本节中，应用这个方法构建全球语言检测模型等问题。

​	更一般的说，我们将通过考虑回归或分类问题来预测一些以 $x$ 为随机变量的函数 $y$ 。为了导出这个问题的GLMS，我们将做出以下三个关于我们模型的，给出 $x$ 关于 $y$ 的条件分布的假设：

​	$1.$ $y | x$ ；$\theta$ $～$ $ExponentialFamily(\eta)$。即：鉴于 $x$ 和 $\theta$ ， $y$ 的分布在参数 $\eta$ 遵循一些指数分布。

​	$2.$ 鉴于 $x$ ，我们的目标是预测 $T$ 的预期值 $y(x)$ ，在我们大多数例子中，我们将会使 $T(y) = y$ ，所以这意味着我们将会通过我们的学习假说 $h$ 预测 $h(x)$ 的输出来满足 $h(x) = E[y | x]$ 。(注意，这个假设是让 $h(x)$ 选择满足逻辑回归或线性回归。例如，在逻辑回归中，我们有 $h(x) = p(y = 1 | x；\theta) = 0 * p(y = 0|x；\theta) + 1 *p(y = 1|x；\theta) = E[y|x；\theta]$。)

​	$3.$ 自然参数 $\eta$ 和输入的 $x$ 线性相关：$\eta = \theta T x$。(或者，如果η是向量值，则$\eta i =\theta i T x$)

以上三个假设中的第三个似乎是最不合理的，该假设可能会被更好地被认为是我们用于设计GLM的配方的“设计选择”，而不是作为假设本身这三个假设或设计选择将使我们得出一个非常优雅的学习算法课程，即GLM，其具有如轻松学习等许多期望的性质。 此外，GLM所得到的模型通常可以非常有效地为y建立出不同类型的分布; 例如我们将很快展示出来的逻辑回归和普通最小二乘法，两者都可以都被导出为GLM。

### 9.1 普通最小二乘法 (Ordinary Least Squares)

为了表明普通最小二乘法是一种特殊的GLM的家庭模型，于是我们考虑设置目标变量 $y$ (在GLM术语中也称为响应变量) 是连续的，而且我们模型 $x$ $y$ 给定的条件分布为高斯分布 $N(\mu，\sigma^2)$。(在这里，$\mu$ 可能取决于 $x$ )。所以，我们让指数分布族 (ExponentiaFamily)( $\eta$ ) 的分布是高斯分布。 正如我们之前看到的，配方的高斯分布是作为指数族分布的，我们将 $\mu = \eta$。所以，我们有：

​		$h_{\theta(x)} = E[y|x；\theta]$

​	          $ = \mu$

​	          $ = \eta$

​     	          $ = \theta^{T}x.​$

上面 (9) 中的第一个假设2，平等；第二个等式的合集为 $y |x：\theta$ $～$ $N (\mu，\sigma^2)$，所以预测值等于$\mu$ ；第三个等式遵从假设 1 (由我们先前的推导表明，$\mu = \eta$ 为配方的高斯指数族分布)，而最后一个等式遵从假设3。



### 9.2 S型回归 (Logistic Regression)

我们现在考虑逻辑回归。我们感兴趣的是二进制分类，因此 $y \in \{0，1\}$。鉴于 $y$ 是只有两个值的集合，这个自然选择的伯努利家族分布模型的条件为: $y$ 是由 $x$ 决定的。在我们制定的伯努利分布指数族分布，我们有 $\phi = 1/(1 + e^{-\eta})$。此外，请注意，如果 $x，y|\theta ～伯努利分布(\phi)$，然后 $E[x|y;\theta] = \phi$。similardervation 后，作为一个普通的最小二乘法，我们得到：

​	$h_\theta = E[y|x;\theta]$ 

​             $ = \phi$  

​	     $= 1/(1 + e^{-\eta})$

​             $= 1/(1 + e^{-\theta^{T}x})$

所以，这给了我们假设函数形式为: $h(x) = 1/(1 + e-Tx)$。如果你特别想知道我们为什么认为这个函数的回归形式是 $1/(1 + e-z)$，我们在这给出其中一个答案：一旦我们假设 $y$ 决定于 $x$ 的条件是伯努利分布，它决定了后果的漠视和指数分布的定义。在这我们引入更多一些的术语，该函数 $g$ 给分配的平均作为自然参数的函数 $(g(\eta) = E[T(y);\eta)]$被称为**规范响应函数**。其逆 $g^{-1}$ ，称为**规范化链接功能**。因此，规范响应函数只是高斯家庭的识别功能，而伯努利响应函数是规范化功能。

### 9.3 Softmax Regression