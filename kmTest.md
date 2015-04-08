
```r
set.seed(3)
kmTestInput <- data.frame(x = runif(30, max = 5), y = runif(30, max = 5))
km <- kmeans(kmTestInput, 2)
# png('kmTest.png', width = 1000, height = 1000, pointsize = 12, res = 150)
plot(kmTestInput, col = km$cluster, pch = "+")
```

![plot of chunk createTestInput](figure/createTestInput-1.png) 

```r
# dev.off() write.csv(km$cluster, 'kmTestOutput.csv', row.names = FALSE,
# quote = FALSE) write.csv(kmInputTest, 'kmTestInput.csv', row.names =
# FALSE, quote= FALSE)

set.seed(1)
km1 <- kmeans(kmTestInput, 2)
set.seed(4)
km2 <- kmeans(kmTestInput, 2)
# png('kmComparison.png', width = 1000, height = 1000, pointsize = 12, res =
# 150)
par(mfrow = c(1, 2))
plot(kmTestInput, pch = km1$cluster)
plot(kmTestInput, pch = km2$cluster)
```

![plot of chunk createTestInput](figure/createTestInput-2.png) 

```r
# dev.off()


withinSS <- function(dat, km_object) {
    # dat = k-means input matrix/dataframe km_object = object of class kmeans
    # returns total within group sum of squares
    
    dat$cluster <- km_object$cluster
    
    within_ss <- 0
    
    for (c in 1:length(unique(dat$cluster))) {
        s2c <- 0
        for (i in 1:ncol(dat[, names(dat) != "cluster"])) {
            s2c <- s2c + sum((dat[dat$cluster == c, i] - mean(dat[dat$cluster == 
                c, i]))^2)
        }
        within_ss <- within_ss + s2c
    }
    return(within_ss)
}

betweenSS <- function(dat, km_object) {
    # dat = k-means input matrix/dataframe km_object = object of class kmeans
    # returns between group sum of squares
    
    dat$cluster <- km_object$cluster
    
    between_ss <- 0
    
    for (c in 1:length(unique(dat$cluster))) {
        d <- 0
        for (i in 1:ncol(dat[, names(dat) != "cluster"])) {
            d <- d + (mean(dat[dat$cluster == c, i]) - mean(dat[, i]))^2
        }
        between_ss <- between_ss + (d * sum(dat$cluster == c))
    }
    return(between_ss)
}

totalSS <- function(dat, km_object) {
    # dat = k-means input matrix/dataframe km_object = object of class kmeans
    # returns total sum of squares
    
    100 * betweenSS(dat, km_object)/(betweenSS(dat, km_object) + withinSS(dat, 
        km_object))
}

totalSS(kmTestInput, km1)
```

```
## [1] 43.28807
```

```r
100 * betweenSS(kmTestInput, km1)/(betweenSS(kmTestInput, km1) + withinSS(kmTestInput, 
    km1))  # percentage of variance explained
```

```
## [1] 43.28807
```

```r
var_explained <- numeric()

for (i in 1:10) {
    km <- kmeans(kmTestInput, i)
    var_explained <- c(var_explained, 100 * km$betweenss/km$totss)
}

plot(var_explained)  ## looks like 5 is optimal

plot(kmTestInput, col = kmeans(kmTestInput, 5)$cluster, pch = "+")
```

![plot of chunk createTestInput](figure/createTestInput-3.png) 

```r
##### using the shakespeare's weights matrix

var_explained <- numeric()

for (i in 1:nrow(weights)) {
    km <- kmeans(weights, i)
    var_explained <- c(var_explained, 100 * km$betweenss/km$totss)
}
```

```
## Error: number of cluster centres must lie between 1 and nrow(x)
```

```r
plot(var_explained)

require(skmeans)
```

![plot of chunk createTestInput](figure/createTestInput-4.png) 