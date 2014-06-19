Project
========================================================



**Reading and Processing the Data**

First: Reading the csv file, and look at the name of its variable
and Remove variables that are nulls



```r
nullstrings=c("","NA")
data = read.csv("pml-training.csv", na.strings=nullstrings)
whichcolsarenull<-apply(data, 2, function(curcol){sum(is.na(curcol))})
Finaldata<-data[,which(whichcolsarenull==0)]
dim(Finaldata)
```

```
## [1] 19622    60
```
Column with mostly Nulls are removed

Also, The first 7 variables need to be removed since they are not related to the result. 

```r
Finaldata = Finaldata[,-c(1:7)]
dim(Finaldata)
```

```
## [1] 19622    53
```
**Data Partition**

Partitioning data into Training, and Testing set


```r
inTrain=createDataPartition(Finaldata$classe,p=0.7,list=FALSE)
training=Finaldata[-inTrain,]
testing=Finaldata[inTrain,]
dim(training);dim(testing)
```

```
## [1] 5885   53
```

```
## [1] 13737    53
```
**Building the Model**

*using random forest to build the model*


```r
modelFit <- train(training$classe ~ ., data=training, method='rf',  trControl = trainControl(method = "cv", number = 4), allowParallel=T)
```

```
## Loading required package: randomForest
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
```

**Checking the accuracy of the model (in-sample error)**
The model is expected to be accurate , and using the model Fit, the accuracy was 1

```r
modelFit
```

```
## Random Forest 
## 
## 5885 samples
##   52 predictors
##    5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (4 fold) 
## 
## Summary of sample sizes: 4414, 4413, 4415, 4413 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
##   2     1         1      0.002        0.003   
##   30    1         1      0.004        0.005   
##   50    1         1      0.005        0.006   
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 27.
```

**Using the model on testing data to predict its classe**

```r
pre<-predict(modelFit,testing)
```

```
## Loading required package: randomForest
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
```
**Testing the Model on CrossValidation Test by:**

    *Estimating the out-of-sample error using Confusion matrix*


```r
confusionMatrix(testing$classe,pre)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 3887   16    0    2    1
##          B   36 2613    4    0    5
##          C    0   21 2368    6    1
##          D    0    1   22 2228    1
##          E    0    9    8   17 2491
## 
## Overall Statistics
##                                         
##                Accuracy : 0.989         
##                  95% CI : (0.987, 0.991)
##     No Information Rate : 0.286         
##     P-Value [Acc > NIR] : <2e-16        
##                                         
##                   Kappa : 0.986         
##  Mcnemar's Test P-Value : NA            
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.991    0.982    0.986    0.989    0.997
## Specificity             0.998    0.996    0.998    0.998    0.997
## Pos Pred Value          0.995    0.983    0.988    0.989    0.987
## Neg Pred Value          0.996    0.996    0.997    0.998    0.999
## Prevalence              0.286    0.194    0.175    0.164    0.182
## Detection Rate          0.283    0.190    0.172    0.162    0.181
## Detection Prevalence    0.284    0.193    0.174    0.164    0.184
## Balanced Accuracy       0.994    0.989    0.992    0.993    0.997
```
