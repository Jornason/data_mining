Practical Machine Learning：Peer Assignment
========================================================

 
## Clean the Dataset

```r
data <- read.csv("F:/coursera/Data Science/peer assignment/data mining/pml-training.csv")
data <- data[, -c(grep("kurtosis", names(data)), grep("skewness", names(data)), 
    grep("max", names(data)), grep("min", names(data)), grep("amplitude", names(data)), 
    grep("var", names(data)), grep("avg", names(data)), grep("stddev", names(data)))]
data <- data[, -c(1:2, 5:7)]
```

## Cross Validation 
I use 70% data as the training set,and the left for testing.

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
library(ggplot2)
inTrain <- createDataPartition(y = data$classe, p = 0.7, list = FALSE)
training <- data[inTrain, ]
testing <- data[-inTrain, ]
```

## Model Building
I use support vector machine to build the model.

```r
library(e1071)
```

```
## Warning: package 'e1071' was built under R version 3.1.1
```

```r
modFit <- svm(classe ~ ., data = training, cross = 10, type = "C-classification")
modFit
```

```
## 
## Call:
## svm(formula = classe ~ ., data = training, cross = 10, type = "C-classification")
## 
## 
## Parameters:
##    SVM-Type:  C-classification 
##  SVM-Kernel:  radial 
##        cost:  1 
##       gamma:  0.01852 
## 
## Number of Support Vectors:  6546
```

## Evaluate the Model
First,I calculate the in sample error:

```r
pred1 <- predict(modFit,training)
(A<-table(pred1,training$classe))
```

```
##      
## pred1    A    B    C    D    E
##     A 3901  184    4    2    0
##     B    1 2427   57    0    4
##     C    4   46 2309  198   40
##     D    0    1   17 2051   48
##     E    0    0    9    1 2433
```

```r
sum(diag(A))/sum(A)
```

```
## [1] 0.9552
```
We can see the accuracy is 0.9552.If the model isn't overfitting,the out of sample error will be little big than in sample,I think it will no less than 90%.  
Now,we calculator the out of sample error:


```r
pred2 <- predict(modFit,testing)
(B<-table(pred2,testing$classe))
```

```
##      
## pred2    A    B    C    D    E
##     A 1667   89    2    8    0
##     B    0 1026   23    0    3
##     C    5   23  984   83   20
##     D    0    1   13  873   18
##     E    2    0    4    0 1041
```

## predict 20 different test cases

```r
test<-read.csv("F:/coursera/Data Science/peer assignment/data mining/pml-testing.csv")
test<-test[,-c(grep("kurtosis",names(test)),grep("skewness",names(test)),grep("max",names(test)),grep("min",names(test)),grep("amplitude",names(test)),grep("var",names(test)),grep("avg",names(test)),grep("stddev",names(test)))]
test<-test[,-c(1:2,5:7)]

pred<-predict(modFit,test)
pred
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  A  A  A  E  D  B  A  A  A  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```

