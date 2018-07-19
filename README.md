# CouseraPracticalMachineLearningCourseProject
---
title: "Course8-PracticalMachineLearning-CourseProject"
author: "Steven Wang"
date: "July 19, 2018"
---

# Practical Machine Learning
(Data from Groupware)

# Loading, Processing and Cleaning Data
```{R}
urltrain<-"https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
urltest<-"https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
traindata<-read.csv(urltrain)
testdata<-read.csv(urltest)
```

```{R}
# The first 7 columns are not factors used from prediction so exclude them from the dataset used for building the model.
trainclean<-traindata[,8:length(colnames(traindata))]
testclean<-testdata[,8:length(colnames(testdata))]

# Omit columns with NAs
trainclean<-trainclean[,colSums(is.na(trainclean))==0]
testclean<-testclean[,colSums(is.na(testclean))==0]

# Get rid of predictors with near zero variances
library(caret)
nzv<-nearZeroVar(trainclean,saveMetrics = TRUE)
zerovar<-sum(nzv$nzv)
if(zerovar>0) {trainclean<-trainclean[,nzv$nzv==FALSE]}
```
## Spliting Data
```{R}
inTrain<-createDataPartition(y=trainclean$classe,p=0.6,list=FALSE)
trainset<-trainclean[inTrain,]
testset<-trainclean[-inTrain,]
dim(trainset)
dim(testset)
```
# Building up Model
### The training dataset will be used to fit the Random Forest Model as this model automatically select the predictors that are important for the outcome. Also, use class validation to train the model.
```{R}
rfmodel<-train(classe~., data = trainset,method="rf",trControl=trainControl(method = "cv",3))
rfmodel
```
