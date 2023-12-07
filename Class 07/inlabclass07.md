# Class 07: Machine Learning 1
Jesus (A17597539)

\#Clustering

We will start today’s lab eith clustering methods, in particular
so-called K-means. The main function for this in R is `kmeans()`.

Let’s try it on some made up data where we know what the answer should
be.

``` r
x <- rnorm(10000, mean=3)
hist(x)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-1-1.png)

60 points

``` r
tmp <- c(rnorm(30, mean=3), rnorm(30, mean= -3))
x <- cbind(x=tmp, y=rev(tmp))
head(x)
```

                x          y
    [1,] 3.635810 -0.1036373
    [2,] 3.330599 -2.9760689
    [3,] 1.529784 -2.1639814
    [4,] 1.879047 -3.5349406
    [5,] 1.418659 -2.4900677
    [6,] 2.573563 -3.3541573

We can pass this to the base R `plot()` function for a quick

``` r
plot(x)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-3-1.png)

``` r
k <- kmeans(x, centers=2, nstart=20)
k
```

    K-means clustering with 2 clusters of sizes 30, 30

    Cluster means:
              x         y
    1 -3.048521  2.853518
    2  2.853518 -3.048521

    Clustering vector:
     [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1 1
    [39] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

    Within cluster sum of squares by cluster:
    [1] 62.79549 62.79549
     (between_SS / total_SS =  89.3 %)

    Available components:

    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

> Q1. How many points are in each cluster?

``` r
k$size
```

    [1] 30 30

> Q2. Cluster membership?

``` r
k$cluster
```

     [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1 1
    [39] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

> Q3. Cluster Centers?

``` r
k$centers
```

              x         y
    1 -3.048521  2.853518
    2  2.853518 -3.048521

> Q4. Plot my clustering results

``` r
plot(x, col=k$cluster, pch=16)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-8-1.png)

> Q5. Cluster the data again with kmeans() into 4 groups and plot the
> results.

``` r
k4 <- kmeans(x, centers=4, nstart=20)
plot(x, col=k4$cluster, pch=16)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-9-1.png)

K-mean is very popular mostly because it is fast and relatively straight
forward to run and understand. It has a big limitation in that you needs
to tell it how many grouos (k, or centers) you want.

\#Hierarchical clustering

The main function in base R is called `hclust()`. You have to pass it in
a “distance matrix” not just your input data

You can generate a distance matix with the `dist()` funciton.

``` r
hc <- hclust( dist(x) )
hc
```


    Call:
    hclust(d = dist(x))

    Cluster method   : complete 
    Distance         : euclidean 
    Number of objects: 60 

``` r
plot(hc)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-11-1.png)

To find the clusters (cluster memebership vector) from a `hclust()`
result we can “cut” the tree at a certain height that we like. For this
we use the \`

``` r
plot(hc)
abline(h=8, col="red")
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-12-1.png)

``` r
grps <- cutree(hc, h=8)
```

``` r
table(grps)
```

    grps
     1  2 
    30 30 

> Q6. Plot our hclust results

``` r
plot(x, col=grps, pch=16)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-14-1.png)

\#Principal Componenet Analysis

\##PCA of UK food data

Read data showing the consumption in grams (per person, per week) of 17
different types of food-stuff measured and averaged in the four
countries of the United Kingdom in 1997

Let’s see how PCA can help us but first we can try conventional analysis

``` r
url <- "https://tinyurl.com/UK-foods"
x <- read.csv(url)
x
```

                         X England Wales Scotland N.Ireland
    1               Cheese     105   103      103        66
    2        Carcass_meat      245   227      242       267
    3          Other_meat      685   803      750       586
    4                 Fish     147   160      122        93
    5       Fats_and_oils      193   235      184       209
    6               Sugars     156   175      147       139
    7      Fresh_potatoes      720   874      566      1033
    8           Fresh_Veg      253   265      171       143
    9           Other_Veg      488   570      418       355
    10 Processed_potatoes      198   203      220       187
    11      Processed_Veg      360   365      337       334
    12        Fresh_fruit     1102  1137      957       674
    13            Cereals     1472  1582     1462      1494
    14           Beverages      57    73       53        47
    15        Soft_drinks     1374  1256     1572      1506
    16   Alcoholic_drinks      375   475      458       135
    17      Confectionery       54    64       62        41

Q1. 5 columns and 17 rows

``` r
nrow(x)
```

    [1] 17

``` r
ncol(x)
```

    [1] 5

``` r
# Note how the minus indexing works
rownames(x) <- x[,1]
x <- x[,-1]
head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

``` r
dim(x)
```

    [1] 17  4

Second approach to correct row names

``` r
x <- read.csv(url, row.names=1)
head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

Q2. I prefer to use the second approach to correc tour row-names problem
because it is faster and takes less work for me. The second approach is
more robust because if you rerun the first approach it will overwrite
itself.

``` r
barplot(as.matrix(x), beside=T, col=rainbow(nrow(x)))
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-20-1.png)

Q3.

``` r
barplot(as.matrix(x), beside=F, col=rainbow(nrow(x)))
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-21-1.png)

A pairs plot can be useful if we dont have 2 dimensions…

``` r
pairs(x, col=rainbow(17), pch=16, cex=2)
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-22-1.png)

Q5. This pairwise plot shows a matrix of scatterplots. Each country has
its own respective row and column. The rainbow code give each of the 17
food types its own color. If a point lies within the diagonal line for a
given plot, it means that the food type is consumes equally in those
countries

Q6. The main difference we can see between N. Irelandis that they have
different amount of consumption of food types and this can be seen as
the best of fit line is not very diagonal compared to the others.

\###Principal Componenet Analysis

PCA can help us make sense of these types of datasets. Let’s see how it
works.

The main function in “base” R is called `prcomp()`. In this case we want
to first take the transpose `t()` of our input `x` so the columns are
the food types and the countries are the rows

``` r
head( t(x) )
```

              Cheese Carcass_meat  Other_meat  Fish Fats_and_oils  Sugars
    England      105           245         685  147            193    156
    Wales        103           227         803  160            235    175
    Scotland     103           242         750  122            184    147
    N.Ireland     66           267         586   93            209    139
              Fresh_potatoes  Fresh_Veg  Other_Veg  Processed_potatoes 
    England               720        253        488                 198
    Wales                 874        265        570                 203
    Scotland              566        171        418                 220
    N.Ireland            1033        143        355                 187
              Processed_Veg  Fresh_fruit  Cereals  Beverages Soft_drinks 
    England              360         1102     1472        57         1374
    Wales                365         1137     1582        73         1256
    Scotland             337          957     1462        53         1572
    N.Ireland            334          674     1494        47         1506
              Alcoholic_drinks  Confectionery 
    England                 375             54
    Wales                   475             64
    Scotland                458             62
    N.Ireland               135             41

``` r
# Use the prcomp() PCA function 
pca <- prcomp( t(x) )
summary(pca)
```

    Importance of components:
                                PC1      PC2      PC3       PC4
    Standard deviation     324.1502 212.7478 73.87622 3.176e-14
    Proportion of Variance   0.6744   0.2905  0.03503 0.000e+00
    Cumulative Proportion    0.6744   0.9650  1.00000 1.000e+00

Q7.

``` r
pca$x
```

                     PC1         PC2        PC3           PC4
    England   -144.99315   -2.532999 105.768945 -4.894696e-14
    Wales     -240.52915 -224.646925 -56.475555  5.700024e-13
    Scotland   -91.86934  286.081786 -44.415495 -7.460785e-13
    N.Ireland  477.39164  -58.901862  -4.877895  2.321303e-13

``` r
plot(pca$x[,1], pca$x[,2], col= c("orange", "red", "blue", "darkgreen"), pch=16 )
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-26-1.png)

``` r
# Plot PC1 vs PC2
plot(pca$x[,1], pca$x[,2], xlab="PC1", ylab="PC2", xlim=c(-270,500))
text(pca$x[,1], pca$x[,2], colnames(x))
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-27-1.png)

Q8

``` r
# Plot PC1 vs PC2
plot(pca$x[,1], pca$x[,2], xlab="PC1", ylab="PC2", xlim=c(-270,500))
text(pca$x[,1], pca$x[,2], colnames(x), col= c("orange", "red", "blue", "darkgreen"))
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-28-1.png)

``` r
v <- round( pca$sdev^2/sum(pca$sdev^2) * 100 )
v
```

    [1] 67 29  4  0

``` r
## or the second row here...
z <- summary(pca)
z$importance
```

                                 PC1       PC2      PC3          PC4
    Standard deviation     324.15019 212.74780 73.87622 3.175833e-14
    Proportion of Variance   0.67444   0.29052  0.03503 0.000000e+00
    Cumulative Proportion    0.67444   0.96497  1.00000 1.000000e+00

``` r
barplot(v, xlab="Principal Component", ylab="Percent Variation")
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-31-1.png)

The loadings tell us how much of the originalvariables (in our case the
foods) contribute to the new variables i.e. the PCs

``` r
## Lets focus on PC1 as it accounts for > 90% of variance ]
head(pca$rotation)
```

                            PC1         PC2         PC3          PC4
    Cheese         -0.056955380  0.01601285  0.02394295 -0.694538519
    Carcass_meat    0.047927628  0.01391582  0.06367111  0.489884628
    Other_meat     -0.258916658 -0.01533114 -0.55384854  0.279023718
    Fish           -0.084414983 -0.05075495  0.03906481 -0.008483145
    Fats_and_oils  -0.005193623 -0.09538866 -0.12522257  0.076097502
    Sugars         -0.037620983 -0.04302170 -0.03605745  0.034101334

``` r
par(mar=c(10, 3, 0.35, 0))
barplot( pca$rotation[,1], las=2 )
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-32-1.png)

``` r
head(pca$rotation)
```

                            PC1         PC2         PC3          PC4
    Cheese         -0.056955380  0.01601285  0.02394295 -0.694538519
    Carcass_meat    0.047927628  0.01391582  0.06367111  0.489884628
    Other_meat     -0.258916658 -0.01533114 -0.55384854  0.279023718
    Fish           -0.084414983 -0.05075495  0.03906481 -0.008483145
    Fats_and_oils  -0.005193623 -0.09538866 -0.12522257  0.076097502
    Sugars         -0.037620983 -0.04302170 -0.03605745  0.034101334

``` r
par(mar=c(10, 3, 0.35, 0))
barplot( pca$rotation[,2], las=2 )
```

![](inlabclass07_files/figure-commonmark/unnamed-chunk-33-1.png)

Q9. The two prominent good groups are fresh potatoes and soft drinks.
PC2 mainly tells us about the more prominent food groups because of the
lesser amount of variance with PC2
