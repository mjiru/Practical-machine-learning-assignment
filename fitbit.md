# FitBit
Michaela Jírů  
15 června 2017  



## Fitbit prediction

This paper is an assigment from the Coursera course Practical Machine Learning, a part of Data Science Specialization. The goal is to predict the manner of fitbit's users the practised and excercise based on the data from  accelerometers on the belt, forearm, arm, and dumbell of 6 participants.

##Prepartion
Firstly, I am going to load in the traing and the testing data and see the dimensions.

```r
train <- read.csv("pml-training.csv",na.strings=c("NA",""))
test <- read.csv("pml-testing.csv",na.strings=c("NA",""))
#head(train)
dim(train)
```

```
## [1] 19622   160
```
There is a lot of variables, wee need to remove those with a lot of NAs those which seem to be irrelevant.

```r
isNA <- apply(train, 2, function(x) { sum(is.na(x)) })
notNa <- subset(train[, which(isNA == 0)], )
finalTrain <- notNa[,-(1:7)]
dim(finalTrain)
```

```
## [1] 19622    53
```
That leaves us with 53 variables. Let's split it into training and testing dataset.

```r
inTrain <- createDataPartition(finalTrain$classe, p=0.7, list=F)
training <- finalTrain[inTrain,]
testing <- finalTrain[-inTrain,]
```

##Modeling with Random Forest
First we set the train control to be 5-fold cross-validation and than we train the model with random forest method. 

```r
train_control <- trainControl( method="cv", number=5)
fit <- train(classe ~ ., data=training, model="rf", trControl=train_control)
prediction <- predict(fit, newdata=testing)
acc<-sum(prediction == testing$classe) / length(prediction)
```
Now we can check the prediction against the data. The  accuracy is 0.9937128. Which is quite high, there is no need to try a different model.
Let us see what are the most imporant variables.


```r
varImp(fit)
```

```
## rf variable importance
## 
##   only 20 most important variables shown (out of 52)
## 
##                   Overall
## roll_belt          100.00
## yaw_belt            79.87
## magnet_dumbbell_z   69.42
## pitch_forearm       65.36
## pitch_belt          64.26
## magnet_dumbbell_y   63.36
## magnet_dumbbell_x   53.69
## roll_forearm        51.22
## accel_dumbbell_y    48.17
## accel_belt_z        47.61
## magnet_belt_z       46.44
## magnet_belt_y       43.79
## roll_dumbbell       42.28
## roll_arm            41.64
## accel_dumbbell_z    35.43
## gyros_belt_z        33.17
## accel_forearm_x     32.89
## accel_dumbbell_x    32.72
## yaw_dumbbell        31.26
## gyros_dumbbell_y    29.48
```
Now we predict on the real test data.

```r
predTest<-predict(fit, newData=test)
```
##Conclusion

A model was made with 5-fold cross validation and random forest, which led to 99% accuracy. The most important predictors are roll_belt, yaw_belt     magnet_dumbbell_z, magnet_dumbbell_y, pitch_forearm, pitch_belt, magnet_dumbbell_x and roll_forearm.  
