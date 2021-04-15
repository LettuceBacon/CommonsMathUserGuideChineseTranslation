# 1 统计

## 1.1 概述

统计软件包提供了基本描述性统计、频率分布、双变量回归、t平方、卡方和方差分析检验统计的框架和实现。

* [描述统计学][描述统计学]
* [频率分布][频率分布]
* [简单回归][简单回归]
* [多元回归][多元回归]
* [][]
* [协方差与相关][协方差与相关]
* [统计测试][统计测试]

## 1.2 描述统计学

统计软件包包括了下列描述统计方法的框架和默认实现:

* 算数平均值和几何平均值
* 方差和标准差
* 和、积、对数和、平方值之和
* 最小值、最大值、中位数和百分数
* 偏度和峰度
* 一阶、二阶、三阶、四阶矩

除了百分位数和中位数之外，所有这些统计都可以在不往内存中装入所有输入数据的情况下被执行。统计软件包提供了不需要存储值的接口和实现，以及对存储值的数组进行操作的实现。  

顶层接口是[UnivariateStatistic](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/UnivariateStatistic.html)。这个接口由所有统计实现。这个接口包括以double\[\]数组作为参数，并返回统计结果的函数`evaluate()`。这个接口由[StorelessUnivariateStatistic](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/StorelessUnivariateStatistic.html)继承，它增加了`increment()`、`getResult()`和相关支持"零存储"的方法，这些方法维护当使用`increment()`方法向统计增添新值后统计所包含值的个数、和或其他状态信息。  

[AbstractUnivariateStatistic](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/AbstractUnivariateStatistic.html)和[AbstractStorelessUnivariateStatistic](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/AbstractStorelessUnivariateStatistic.html)分别提供了顶层接口的抽象实现。  

每个统计都是作为一个独立的类，由其中一个子包（moment、rank、summary）以及每个继承了上面的一个抽象类（取决于计算统计量是否需要保存样本值）的类实现。有几种方法可以实例化和使用统计方法。这些统计方法可以直接实例化并使用。但一般来说，使用所提供的聚集、[描述统计](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/DescriptiveStatistics.html)和[汇总统计](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/SummaryStatistics.html)来访问它们更为方便（和有效）。  

`DescriptiveStatistics`将输入的数据保存在内存中，并且能够从一个包含刚刚添加的样本值的"窗口"中计算出"滚动更新"的统计数据。  

`SummaryStatistics`并不将输入的样本值存储在内存中，因此该聚集中包含的统计仅限于那些可以在不访问完整样本值数组的情况下一次性计算出来的数据。  

| 聚集 | 包含的统计 | 是否存储样本值 | 是否“滚动更新” |
| --- | --- | --- | --- |
| 描述统计 | min, max, mean, geometric mean, n, sum, sum of squares, standard deviation, variance, percentiles, skewness, kurtosis, median | 是 | 是 |
| 汇总统计 | min, max, mean, geometric mean, n, sum, sum of squares, standard deviation, variance | 否 | 否 |

`SummaryStatistics`可以使用[AggregateSummaryStatistics](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/stat/descriptive/AggregateSummaryStatistics.html)进行汇总。这个类可以用来并行收集多个数据集的统计数据，也可以收集包括所有数据在内的组合样本的统计数据。\
MultivariateSummaryStatistics类似于SummaryStatistics，但它处理的是n元组值而不是标量值。它还可以计算输入数据的完整协方差矩阵。\
DescriptiveStatistics和SummaryStatistics都不是线程安全的。SynchronizedDescriptiveStatistics和SynchronizedSummaryStatistics分别为需要多个线程并发访问统计聚合的应用提供了线程安全的版本。\
SynchronizedMultivariateSummaryStatistics提供线程安全的MultivariateSummaryStatistics。\
还有一个工具类StatUtils，它提供了直接从double\[\]数组统计的静态方法。\
这里有一些例子展示如何进行描述统计。\
对一组双精度浮点数进行汇总统计\
使用DescriptiveStatistics聚合（值存储在内存中）。\
\<\>\
使用SummaryStatistics聚合（值不存储在内存中）。\
\<\>\
使用StatUtils工具类:\
\<\>\
维护最近100个从输入流获得的值的 \"滚动平均值\"。\
使用DescriptiveStatistics实例，窗口大小设置为100。\
\<\>\
以线程安全的方式统计数据\
使用一个SynchronizedDescriptiveStatistics实例。\
\<\>\
同时执行多个样本的统计和总体统计。\
使用AggregateSummaryStatistics有两种方法。\<\>\
\<\>\
上述方法的缺点是，addValue调用必须在集合维护的SummaryStatistics实例上同步进行，而且每次加值都会更新集合以及子样本。对于可以等待到所有值被添加后再进行聚合的应用，可以使用静态聚合方法，如下例所示。当需要跨线程进行聚合时，应该使用该方法。\
\<\>\

## 1.3 频率分布

Frequence提供了一个简单的接口，用于维护离散值的计数和百分比。\
字符串、整型、长整型和字符型都可以作为值的类型\<\>。\<\>但可以通过向构造函数提供一个Comparator来重写。添加与已经添加的值不具有可比性的值会抛出IllegalArgumentException。\
下面是一些例子。\
基于整数值计算频率分布\
混合整型、长整型、整型和长整型\
\<\>\
对字符串频率进行计数\
使用对大小写敏感的比较和字母顺序排序顺序（自然比较器）。\
\<\>\
使用不区分大小写的比较器。\
\<\>\
简单回归\
SimpleRegression提供了普通的最小二乘回归，用一个独立变量估计线性模型。\
y = intercept + slope \* x\
或\
y = slope \* x\
\<\>\
观测值(x,y对)可以一次一个添加到模型中，也可以由一个二维数组提供。观测值不存储在内存中，因此对可添加到模型中的观测值数量没有限制。\
使用说明\
当模型中的观测值少于两个，或者x值没有变化（即所有x值都相同）时，所有统计方法都返回NaN。至少需要两个具有不同x坐标的观测值才能估计一个双变量回归模型。\
统计的getter总是根据当前的观测值集来计算值\--也就是说，你可以get统计，然后添加更多的数据，并在不使用新实例的情况下获取更新的统计数据。没有
\"compute
\"方法可以更新所有的统计，而是每个getter都会执行必要的计算来返回所请求的统计数据。\
\<\>\
实现说明\
\<\>所有的回归统计都是由这些总和计算出来的。\
推断统计（置信区间、参数显著性水平）是基于模型中的观测值来自于双变量正态分布这样的假设。\
下面是一些例子。\
根据每次数量加一的观测值估计模型。\
实例化回归实例并添加数据点。\
\<\>\
根据目前添加的观察值进行统计。\
\<\>\
使用回归模型来预测新的x值所对应的y值。\
\<\>\
可以添加更多的数据点，后续的getXxx调用会将新添加的数据纳入统计中。\
从一个double\[\]\[\]的数据点数组中估计一个模型。\
实例化回归对象并加载数据集。\
\<\>\
根据数据估计回归模型\
\<\>\
可以添加更多的数据点\--甚至是另一个double\[\]\[\]数组，后续的getXxx调用将在统计中纳入新添加的数据。\
从一个double\[\]\[\]的数据点数组中估计一个模型，不包括截距。\
实例化回归对象并加载数据集。\
\<\>\
根据数据估计回归模型\
\<\>\
当没有估计常数时，解释斜率时必须谨慎。斜率可能有偏差。\
多线性回归\
OLSMultipleLinearRegression和GLSMultipleLinearRegression提供最小二乘回归来拟合线性模型。\
Y=X\*b+u\
\<\>\
OLSMultipleLinearRegression提供了普通最小二乘回归，GLSMultipleLinearRegression实现了广义最小二乘。请参阅这些类的java文档以了解所使用的算法和公式的细节。\
\<\>\
详见AbstractMultipleLinearRegression\#newSampleData(double\[\],int,int)、OLSMultipleLinearRegression\#newSampleData(double\[\],double\[\]\[\])和GLSMultipleLinearRegression\#newSampleData(double\[\],double\[\]\[\],double\[\]\[\])。\
使用说明\
当调用newSample、newX、newY或newCovariance方法中的任何一个方法时，数据都会被验证。当输入数据的数组没有匹配的维度或不包含足够的数据来估计模型时，会抛出IllegalArgumentException。\
默认情况下，回归模型是带截距项估计的。\<\>提供给 newX 或 newSample
方法的 X
数据不应包括此列\--数据加载方法将自动创建它。要估计一个没有截距项的模型，请将
noIntercept 属性设置为 true。\
下面是一些例子\
OLS回归\
实例化一个OLS回归对象并加载一个数据集。\
\<\>\
\<\>\
\<\>\
GLS regression\
实例化一个GLS回归对象并加载一个数据集。\
\<\>\
\<整个rank transformation\>\
协方差与相关系数\
org.apache.commons.math4.stat.correlation包计算多对数组或矩阵的各列之间的协方差和相关系数。Covariance计算协方差，PearsonsCorrelation提供皮尔逊积矩相关系数，SpearmansCorrelation计算斯皮尔曼秩相关系数，KendallsCorrelation计算肯德尔秩相关系数。\
执行说明\
无偏协方差由公式给出\
cov(X, Y) = sum \[(xi - E(X))(yi - E(Y))\] / (n - 1)
，其中E(X)是X的平均值，E(Y)是Y值的平均值。无偏差修正估计使用n代替n-1。是否对协方差进行了偏差修正由可选参数
\"biasCorrected \"决定，该参数默认为true。\
PearsonsCorrelation计算由公式\
cor(X, Y) = sum\[(xi - E(X))(yi - E(Y))\] / \[(n -
1)s(X)s(Y)\]定义的相关系数。其中E(X)和E(Y)为X和Y的平均值，s(X)、s(Y)为标准差。\
\<\>\
KendallsCorrelation计算两个可测量的量之间的关联。\<\>\
例子：\
两个数组的协方差\
要计算2个double数组x和y之间的无偏协方差，使用：\
\<\>\
对于无偏差校正的协方差，使用：\
\<\>\
协方差矩阵\
求源矩阵列上的协方差矩阵可以用以下方法计算\
\<\>\
返回矩阵的第i-j项是源矩阵第i列和第j列的无偏协方差。如上所述，要想得到无偏差校正的协方差，使用\
\<\>\
2个数组的皮尔逊相关系数\
要计算两个double数组x和y之间的皮尔逊积矩相关系数，使用\
\<\>\
皮尔逊相关系数矩阵\
在源矩阵列上的（皮尔逊）相关矩阵可以用以下方法计算出来\
\<\>\
返回矩阵的第i-j项是源矩阵第i列和第j列数据之间的皮尔逊积矩相关性。\
皮尔逊相关显著性和标准误差\
要计算与皮尔逊相关系数相关的标准误差和/或显著性，首先创建一个PearsonsCorrelation实例\
\<\>\
其中数据是一个矩形数组或RealMatrix。那么标准误差的矩阵是\
\<\>\
用于计算标准误差的公式是\
SER = (((1 - r2)/(n - 2))1/2\
其中r为估计的相关系数，n为源数据集中的观测值数量。\
\<\>\
getCorrelationPValues().getEntry(i,j)是一个分布为tn-2的随机变量取绝对值大于或等于\
\|rij\|((n - 2) / (1 -
rij2))1/2的概率。其中rij是源数组或RealMatrix的第i列和第j列之间的估计的相关系数。这有时被称为系数的显著性。\
例如，如果数据是一个有2列10行的RealMatrix，则\
\<\>\
是两列数据之间的皮尔逊相关系数的显著性。如果该值小于0.01，我们可以说两列数据之间的相关性在99%的水平上是显著的。\
斯皮尔曼秩相关系数\
要计算两个double数组x和y之间的斯皮尔曼秩-矩相关系数\
\<\>\
这相当于\
\<\>\
肯德尔tau相关系数\
要计算两个double数组x和y之间的肯德尔 tau秩相关系数。\
\<\>\
统计检验\
org.apache.commons.math4.stat.inference软件包提供了学生t检验、卡方检验、G检验、单因素方差分析、曼-惠特尼u检验、威尔科克森符号秩检验和二项分布检验统计的实现，以及与t检验、卡方检验、G检验、单因素方差分析、曼-惠特尼u检验、威尔科克森符号秩检验和K-S检验相关的p值。各个检验的实现类分别为TTest、ChiSquareTest、GTest、OneWayAnova、MannWhitneyUTest、WilcoxonSignedRankTest、BinomialTest和KolmogorovSmirnovTest。TestUtils类提供了静态方法来获取测试实例或直接进行检验。下面的例子都使用TestUtils中的静态方法来执行测试。要获得检验实例，可以使用例如TestUtils.getTTest()，或者直接使用其构造函数，例如new
TTest()。\
执行说明\



<!-- 1.1 概述中无序列表 -->
[描述统计学]:#1.2-描述统计学
[频率分布]: #1.3-频率分布