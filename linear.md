# 3 线性代数

## 3.1 概述

commons-math中的对线性代数的支持提供了对实矩阵（包括密集和稀疏矩阵）和向量的操作。它的特点是基本运算（加法、减法......）和分解算法，可以用来解决线性系统的精确解法和最小二乘法。<!-- 词组exact sense如何翻译 -->

## 3.2 实矩阵

[RealMatrix](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/RealMatrix.html)接口表示一个以实数为元的矩阵。它支持以下基本的矩阵操作：

* 矩阵加法、减法和乘法
* 标量加法和乘法
* 转置
* 范数和迹
* 向量运算

例如：

```
// 创建一个两行三列的实矩阵，使用工厂方法为我们选择实现类。
double[][] matrixData = { {1d,2d,3d}, {2d,5d,3d}};
RealMatrix m = MatrixUtils.createRealMatrix(matrixData);

// 再来一个三行两列，这次直接实例化RealMatrix实现类。
double[][] matrixData2 = { {1d,2d}, {2d,5d}, {1d, 7d}};
RealMatrix n = new Array2DRowRealMatrix(matrixData2);

// 注意：在这两种情况下，构造函数都会复制输入的double[][]数组。

// 现在用m乘以n
RealMatrix p = m.multiply(n);
System.out.println(p.getRowDimension());    // 2
System.out.println(p.getColumnDimension()); // 2

// 用LU分解法求p的逆
RealMatrix pInverse = new LUDecomposition(p).getSolver().getInverse();

```

接口的三个主要实现是用于密集矩阵的[Array2DRowRealMatrix](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/Array2DRowRealMatrix.html)和[BlockRealMatrix](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/BlockRealMatrix.html)（第二种更适合50或100以上的维度）以及用于稀疏矩阵的[SparseRealMatrix](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/SparseRealMatrix.html)。

## 3.3 实向量

[RealVector](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/RealVector.html)接口表示一个以实数为元的向量。它支持以下基本的矩阵操作：

* 向量加法和减法
* 元素对元素的乘法和除法
* 标量的加法、减法、乘法、除法和乘方
* 数学函数的映射(cos, sin ...) <!-- mapping如何翻译 -->
* 点积和外积
* 求符合L1、L2和无穷范数定义的距离和范数。

[RealVectorFormat](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/RealVectorFormat.html)类以可定制的文本格式处理向量的输入/输出。

## 3.4 解线性方程组

[DecompositionSolver](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/DecompositionSolver.html)接口的`solve()`方法支持求解AX=B形式的线性方程组，可以是线性意义上的，也可以是最小二乘法上的。一个`RealMatrix`实例被用来表示方程组的系数矩阵。求解方程组分为两个阶段：首先对系数矩阵进行某种方式的分解，然后用建立在前一步分解上的求解器求解方程组。这样，如果要用同一个系数矩阵解决几个方程组，只需进行一次分解和建立求解器。  

例如，要求解下面的方程组

```
            2x + 3y - 2z = 1
            -x + 7y + 6x = -2
            4x - 3y - 5z = 1


```

首先对系数矩阵A进行分解（本例中使用LU分解），并建立一个求解器

```
RealMatrix coefficients =
    new Array2DRowRealMatrix(new double[][] { { 2, 3, -2 }, { -1, 7, 6 }, { 4, -3, -5 } },
                       false);
DecompositionSolver solver = new LUDecomposition(coefficients).getSolver();

```

接下来创建一个`RealVector`数组来表示常向量B，并使用`solve(RealVector)`对方程组进行求解。

```
RealVector constants = new ArrayRealVector(new double[] { 1, -2, 1 }, false);
RealVector solution = solver.solve(constants);

```

`solution`(即解向量)将包含解值x(`solution.getEntry(0)`)、y(`solution.getEntry(1)`)和z(`solution.getEntry(2)`)。  
如下表所示，每种类型的分解都有其特定的语义和对系数矩阵的约束。对于用最小二乘法求解AX=B的算法，X的返回值满足残差AX-B具有最小的范数。最小二乘法是指可以计算出一个超定方程组解的求解器。（超定方程组指方程多于未知数的方程组，相当于一个行数多于列数的A矩阵）。如果存在一个精确的解（即对于某些X，残差AX-B正好为0），那么这个精确的解也是最小二乘法的解。这意味着适合于最小二乘问题的算法也可以用来解决精确问题，但反之则不然。在任何情况下，如果矩阵在构造时的公差内是奇异的<!-- tolerance set如何翻译 -->，那么在调用求解方法时就会触发错误。这一错误响应既存在于计算精确解的算法，也存在于计算最小二乘法解的算法中。

| 分解算法 |     |     |
| --- | --- | --- |
| 名称 | 系数矩阵 | 问题类型 |
| [LU][LU] | 方阵 | 仅精确解 |
| [Cholesky][Cholesky] | 对称正定矩阵 | 仅精确解 |
| [QR][QR] | 任意矩阵 | 最小二乘解 |
| [特征分解][特征分解] | 方阵 | 仅精确解 |
| [SVD][SVD] | 任意矩阵 | 最小二乘解 |

可以使用一个简单的双精度浮点数组代替`RealVector`。在这种情况下，解也会是双精度浮点数组。  

可以在一次方法调用中解决具有相同系数矩阵的多个方程组。要做到这一点，请创建一个矩阵，其列向量对应于要解决的系方程组的常向量，然后使用`solve(RealMatrix)`，它返回一个列向量是对应方程组解的矩阵。

## 3.5 特征值、特征向量、奇异值和奇异向量

分解算法可以用于自身，而不仅仅用于线性系统的求解。这一点对于特征分解和奇异值分解来说是最重要的。  

`EigenDecomposition`接口的`getEigenvalue()`、`getEigenvalues()`、`getEigenVector()`、`getV()`、`getD()`和`getVT()`方法支持求解形式为AX = lambda X的特征问题，其中lambda为实向量。  

`SingularValueDecomposition`接口的`getSingularValues()`、`getU()`、`getS()`和`getV()`方法允许解决形式为AXi = lambda Yi的奇异值问题，其中lambda是一个实标量，Xi和Yi向量形成各自向量空间的正交基（Xi和Yi的向量空间可能具有不同的维度）。

## 3.6 非实数域(复数、分数 ......)

除了实数域外，还可以使用包含非实数[域元素](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/FieldElement.html)的矩阵和向量。已经支持这些非实数域的库有：

* [complex][complex]
* [Fraction][Fraction]
* [BigFraction][BigFraction]
* [BigReal][BigReal]



<!-- 表格“分解算法” -->
[LU]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/LUDecomposition.html
[Cholesky]: commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/CholeskyDecomposition.html
[QR]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/QRDecomposition.html
[特征分解]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/EigenDecomposition.html
[SVD]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/linear/SingularValueDecomposition.html

<!-- 3.6 非实数域中无序列表 -->
[complex]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/complex/Complex.html
[Fraction]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/fraction/Fraction.html
[BigFraction]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/fraction/BigFraction.html
[BigReal]: http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/util/BigReal.html