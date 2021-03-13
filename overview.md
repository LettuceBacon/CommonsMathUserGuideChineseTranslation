# 概述

## 0.1 关于用户手册

本指南旨在帮助程序员快速找到使用Commons Math开发所需的资料。它也是对javadoc API文档的补充，为包中的数学对象和函数提供了更多解释。

## 0.2 commons-math中有什么

Commons Math是由一小套数学/统计工具组成的，它可以解决像下面列表中的编程问题。这个列表并不详尽，只是为了让大家了解Commons Math所提供的工具。

* 计算数字列表的均值、方差和其他汇总统计。<!-- 专业名词：summary statistics如何翻译，以及短语list of number是否对应java类List -->
* 使用线性回归对一组数据点进行拟合。
* 将曲线拟合到一组数据点上。
* 寻找一条平滑曲线，使其满足通过一个点的集合（插值）。<!-- 这句翻译是否符合相应的数学概念 -->
* 使用最小二乘法将参数模型拟合到一组测量值上。
* 解决涉及实数的方程（例如求根）。
* 解线性方程组。
* 解决普通微分方程。<!-- 词ordinary是否可以翻译为普通 -->
* 最小化多维函数。
* 生成有更多限制（如分布、范围）的随机数，这些限制是JDK不能实现的。
* 生成与输入文件中的数据 "相似 "的随机样本和/或数据集。
* 进行统计显著性测试。
* 多种数学函数，如阶乘、二项式系数和 "特殊函数"（如gamma、beta函数）。

我们正积极寻求更多符合Commons Math愿景的组件，即一套轻量级、自足的数学/统计、有效解决常见的编程问题的组件。我们欢迎对新组件或现有功能的改进提出建议。所有的反馈/改进建议都应该发送至commons-dev邮件列表，并在主题开头注明[math]。

## 0.3 commons-math库的结构

Commons Math根据提供的功能分为十六个子包。

* org.apache.commons.math4.stat - 统计学与统计测试
* org.apache.commons.math4.analysis - 求根、积分、插值、多项式
* org.apache.commons.math4.random - 生成随机数、随机字符串、随机数据
* org.apache.commons.math4.special - 特殊函数（如Gamma、Beta函数）
* org.apache.commons.math4.linear - 矩阵以及求解线性方程组
* org.apache.commons.math4.util - 由java.lang.Math扩展而来的常用数学、统计函数
* org.apache.commons.math4.complex - 复数
* org.apache.commons.math4.distribution - 概率分布
* org.apache.commons.math4.fraction - 有理数
* org.apache.commons.math4.transform - 变换 (如快速傅里叶变换)
* org.apache.commons.math4.geometry - 几何（如欧几里得空间和二进制空间分割）
* org.apache.commons.math4.optim - 函数最大化或最小化 <!-- 原句function maximization or minimization是否可以这样翻译 -->
* org.apache.commons.math4.ode - 常微分方程积分
* org.apache.commons.math4.genetics - 遗传算法
* org.apache.commons.math4.fitting - 曲线拟合
* org.apache.commons.math4.ml - 机器学习

在[这里](http://commons.apache.org/proper/commons-math/apidocs/index.html)查看包的Java文档

## 0.4 接口规约是如何在Java文档中描述的 <!-- 词contracts和词specified如何翻译 -->

当你在程序中使用Commons Math组件时，一定要仔细阅读java文档中类和方法的注释。java文档提供了所使用算法的参考，关于限制、性能等的使用说明，以及接口规约。接口规约是以先决条件（为了使方法返回有效的结果必须为真）、返回的特殊值（如Double.NaN）或如果不满足先决条件时可能抛出的异常，以及返回值/对象或状态变化的定义来指定的。<!-- 词referances如何翻译 -->  

当提供给方法的实际参数或对象的内部状态使计算失去意义时，可能会抛出MathIllegalArgumentException或MathIllegalStateException。像抛出运行时异常（和任何其他异常）这样确切的条件在java文档方法注释中被指定。在某些情况下，为了与IEEE 754浮点算术标准和java.lang.Math保持一致，Commons Math方法会返回Double.NaN值。返回Double.NaN或其他特殊的值的情况，在java文档方法注释中被完全指定。

从2.2版本开始，处理空引用的策略如下：当一个null参数意外出现时，一个NullArgumentException将被抛出，以标记该非法参数。请注意，这个类并不继承于标准的NullPointerException，而是MathIllegalArgumentException的一个子类。

## 0.5 依赖关系

Commons Math需要JDK 1.5+，并且没有运行时依赖。

## 0.6 许可

Commons Math是根据Apache License 2.0版本的条款发布的。  

本产品包含由其他第三方开发的软件，并且这些软件在与Apache License 2.0版本兼容的许可条款下发布。所有这些第三方产品的许可证都可以在发行版的LICENSE.txt文件中找到。有些产品需要额外的归属，这些归属可以在NOTICE.txt文件中找到。这些文件在源码包和二进制发行版的jar文件中都有。