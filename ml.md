# 16 机器学习

## 16.1 概述

目前，commons-math中对机器学习的支持提供了基于距离测量的聚类数据集的操作。

## 16.2 聚类算法和距离测量

[Clusterer](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/Clusterer.html)类表示一种聚类算法。以下是可用的算法：

* [KMeans++][KMeans++]：它基于著名的kMeans算法，但使用不同的方法来选择初始值（或 "种子"），从而避免了KMeans有时会因初始值选择而聚类不良的情况。KMeans/KMeans++聚类的目的是将n个观测值划分为k个聚类，使每个点都属于那个具有最近聚类中心的聚类。
* [Fuzzy-K-Means][Fuzzy-K-Means]。经典K-Means算法的一种变体，其主要区别在于，单个数据点并不是唯一地分配到单一的聚类。取而代之的是，每个点i都有一组权重uij，用来表示对聚类j的隶属度。模糊变体不需要聚类中心的初始值，因此更加健壮，尽管比原始的kMeans算法慢。
* [DBSCAN][DBSCAN]：带噪声的基于密度的空间聚类法(DBSCAN)从估计的<>节点的密度分布开始寻找若干个聚类。与KMeans/KMeans++相比，DBSCAN的主要优点是不需要指定初始聚类数，可以找到任意形状的聚类。
* [Multi-KMeans++][Multi-KMeans++]。Multi-KMeans++是一种元算法，基本上是用KMeans++执行n次运行，然后从这些运行中选择最佳聚类(即距离方差最小的聚类)。

可用聚类方法的比较：  
![聚类方法的比较](http://commons.apache.org/proper/commons-math/images/userguide/cluster_comparison.png)

## 16.3 距离测量

每一种聚类算法都需要一个距离测量来确定两个点（数据点或聚类中心）之间的距离。以下是可用的距离测量方法：

* [Canberra distance][Canberra distance]
* [ChebyshevDistance distance][ChebyshevDistance distance]
* [EuclideanDistance distance][EuclideanDistance distance]
* [ManhattanDistance distance][ManhattanDistance distance]
* [Earth Mover's distance][Earth Mover's distance]

## 16.3 例子

下面是一个聚类算法执行的例子。假设我们有一组来自域模型的样本点，其中每个样本点都有一个方法`double getX()`和`double getY()`代表它们在二维空间中当前的坐标。我们要根据样本点的欧氏距离将其聚成10个不同的聚类。  

聚类算法希望有一个[Clusterable](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/Clusterable.html)类的列表作为输入。通常情况下，我们不希望用助手API的接口污染我们的定义域中的对象。<!-- helper APIs如何翻译 -->因此，我们首先创建一个包装器对象。
```
// wrapper class
public static class LocationWrapper implements Clusterable {
    private double[] points;
    private Location location;

    public LocationWrapper(Location location) {
        this.location = location;
        this.points = new double[] { location.getX(), location.getY() }
    }

    public Location getLocation() {
        return location;
    }

    public double[] getPoint() {
        return points;
    }
}
        
```

现在我们将创建这些包装对象的列表（每个样本点一个），作为我们聚类算法的输入：
```
// we have a list of our locations we want to cluster. create a      
List<Location> locations = ...;
List<LocationWrapper> clusterInput = new ArrayList<LocationWrapper>(locations.size());
for (Location location : locations)
    clusterInput.add(new LocationWrapper(location));
        
```

最后，我们可以应用我们的聚类算法，输出找到的聚类。
```
       
// initialize a new clustering algorithm. 
// we use KMeans++ with 10 clusters and 10000 iterations maximum.
// we did not specify a distance measure; the default (euclidean distance) is used.
KMeansPlusPlusClusterer<LocationWrapper> clusterer = new KMeansPlusPlusClusterer<LocationWrapper>(10, 10000);
List<CentroidCluster<LocationWrapper>> clusterResults = clusterer.cluster(clusterInput);

// output the clusters
for (int i=0; i<clusterResults.size(); i++) {
    System.out.println("Cluster " + i);
    for (LocationWrapper locationWrapper : clusterResults.get(i).getPoints())
        System.out.println(locationWrapper.getLocation());
    System.out.println();
}
        
```



<!-- 16.2 无序列表 -->
[KMeans++]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/KMeansPlusPlusClusterer.html
[Fuzzy-K-Means]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/FuzzyKMeansClusterer.html
[DBSCAN]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/DBSCANClusterer.html
[Multi-KMeans++]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/clustering/MultiKMeansPlusPlusClusterer.html

<!-- 16.3 无序列表 -->
[Canberra distance]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/distance/CanberraDistance.html
[ChebyshevDistance distance]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/distance/ChebyshevDistance.html
[EuclideanDistance distance]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/distance/EuclideanDistance.html
[ManhattanDistance distance]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/distance/ManhattanDistance.html
[Earth Mover's distance]:http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math4/ml/distance/EarthMoversDistance.html