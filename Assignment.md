# Practical machine learning - Assignment(week4)

#### Name : Taesoon Kim
#### Date : Jul-14-2017

## Executive summary
In this lecture, I studied the ways how do I analyze data set and find the relationship between variables and result. Dealing with real data, I adjust a variety of prediction method, and forecast the outcome. As a result, when I use random forest, the accuracy is the highest. Thus I apply to test set and get a right result.

## Load & check data

setwd("D:/1-1. R studio/Lecture8. Practical machine learning")

# Load 2 data, training set & test set
train_set<-read.csv(file="pml-training.csv",header=TRUE,sep=",",na.strings=c("NA","#DIV/0!",""))
test_set<-read.csv(file="pml-testing.csv",header=TRUE,sep=",",na.strings=c("NA","#DIV/0!",""))

# Check the column names & data set
library(Hmisc)
# describe(train_set)

library(caret)





