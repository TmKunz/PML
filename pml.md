Practical Machine Learning - Prediction Assignment Writeup
========================================================




Download the training and test data and load them to variables `Train` and `Test`, respectively.

```r
setInternet2(TRUE)
download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv", 
    destfile = "Train.csv")
download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv", 
    destfile = "Test.csv")
Train <- read.csv("Train.csv")
Test <- read.csv("Test.csv")
```


Clean the data by removing columns with more than 80% NAs, X and raw timestamps:

```r
ind <- which(colSums(is.na(Test))/nrow(Test) > 0.8)
Test2 <- Test[, c(-1, -3, -4, -ind)]
Train2 <- Train[, c(-1, -3, -4, -ind)]
Train2 <- Train2[complete.cases(Train2), ]
```


Train with Trees with 10-fold cross validation

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
tc <- trainControl("cv", 10)
rpart.grid <- expand.grid(.cp = 0.01)
modTree <- train(classe ~ ., method = "rpart", data = Train2, trControl = tc, 
    tuneGrid = rpart.grid)
```

```
## Loading required package: rpart
```


The tree model:

```r
modTree
```

```
## CART 
## 
## 19622 samples
##    56 predictors
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 17660, 17659, 17660, 17661, 17659, 17660, ... 
## 
## Resampling results
## 
##   Accuracy  Kappa  Accuracy SD  Kappa SD
##   0.8       0.8    0.02         0.02    
## 
## Tuning parameter 'cp' was held constant at a value of 0.01
## 
```


Since the accuracy is about 80% by 10-fold cross validation, we expect the out-of-sample error to be about 20%.

Predictions:

```r
predict(modTree, newdata = Test2)
```

```
##  [1] B A C A A E D C A A C C B A E E E A A B
## Levels: A B C D E
```





**References**

Ugulino, W.; Cardador, D.; Vega, K.; Velloso, E.; Milidiu, R.; Fuks, H. Wearable Computing: Accelerometers' Data Classification of Body Postures and Movements. Proceedings of 21st Brazilian Symposium on Artificial Intelligence. Advances in Artificial Intelligence - SBIA 2012. In: Lecture Notes in Computer Science. , pp. 52-61. Curitiba, PR: Springer Berlin / Heidelberg, 2012. ISBN 978-3-642-34458-9. DOI: 10.1007/978-3-642-34459-6_6. 
