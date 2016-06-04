# Prediction Assignment Writeup

## Data

The data for this project come from this source: 
http://groupware.les.inf.puc-rio.br/har.

The training data for this project are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

Packages installed are:
#install.packages("caret")
#install.packages("randomForest")

## Loading the data

```r
library(caret)
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```r
library(randomForest)
```

```
## randomForest 4.6-12
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## Attaching package: 'randomForest'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

```r
training_data <- read.csv("pml-training.csv", na.strings=c("NA",""), strip.white=T)
testing_data <- read.csv("pml-testing.csv", na.strings=c("NA",""), strip.white=T)
```
## Clean the Data

Data is cleaned by removing the columns that contains NA and features that are not in testing dataset. We also remove the first 7 features since they are not numeric.

```r
features <- names(testing_data[,colSums(is.na(testing_data)) == 0])[8:59]
training_data <- training_data[,c(features,"classe")]
testing_data <- testing_data[,c(features,"problem_id")]
```
## Partitioning the dataset

```r
set.seed(12345)

inTrain <- createDataPartition(training_data$classe, p=0.6, list=FALSE)
training <- training_data[inTrain,]
testing <- training_data[-inTrain,]
```
## Building the Random Forest Model

```r
set.seed(12345) 
modelRandomForest <- randomForest(classe ~ ., data = training, ntree = 1000)
predictionRF <- predict(modelRandomForest, testing_data, type = "class")
```
## Predicting on the  Data

```r
predictonTrainData <- predict(modelRandomForest, newdata=training)
confusionMatrix(predictonTrainData,training$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 3348    0    0    0    0
##          B    0 2279    0    0    0
##          C    0    0 2054    0    0
##          D    0    0    0 1930    0
##          E    0    0    0    0 2165
## 
## Overall Statistics
##                                      
##                Accuracy : 1          
##                  95% CI : (0.9997, 1)
##     No Information Rate : 0.2843     
##     P-Value [Acc > NIR] : < 2.2e-16  
##                                      
##                   Kappa : 1          
##  Mcnemar's Test P-Value : NA         
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   1.0000   1.0000   1.0000   1.0000
## Specificity            1.0000   1.0000   1.0000   1.0000   1.0000
## Pos Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Neg Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Prevalence             0.2843   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2843   0.1935   0.1744   0.1639   0.1838
## Detection Prevalence   0.2843   0.1935   0.1744   0.1639   0.1838
## Balanced Accuracy      1.0000   1.0000   1.0000   1.0000   1.0000
```

```r
predictonTestData <- predict(modelRandomForest, newdata=testing)
confusionMatrix(predictonTestData,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2230    9    0    0    0
##          B    2 1504    6    0    0
##          C    0    5 1362   17    2
##          D    0    0    0 1267    5
##          E    0    0    0    2 1435
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9939          
##                  95% CI : (0.9919, 0.9955)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9923          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9991   0.9908   0.9956   0.9852   0.9951
## Specificity            0.9984   0.9987   0.9963   0.9992   0.9997
## Pos Pred Value         0.9960   0.9947   0.9827   0.9961   0.9986
## Neg Pred Value         0.9996   0.9978   0.9991   0.9971   0.9989
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2842   0.1917   0.1736   0.1615   0.1829
## Detection Prevalence   0.2854   0.1927   0.1767   0.1621   0.1832
## Balanced Accuracy      0.9988   0.9948   0.9960   0.9922   0.9974
```

## Conclusion

From the result of the confusion matrix , we can conclude that the  Random Forest model is very accurate, about 99%.. Because of that we could expect nearly all of the submitted test cases to be correct. 

