# Practical machine learning - Assignment(week4)

#### Name : Taesoon Kim
#### Date : Jul-14-2017

## Executive summary
In this lecture, I studied the ways how do I analyze data set and find the relationship between variables and result. Dealing with real data, I adjust a variety of prediction method, and forecast the outcome. As a result, when I use random forest, the accuracy is the highest. Thus I apply to test set and get a right result.

## Load & check data
```{r load data}
# set the route
setwd("D:/1-1. R studio/Lecture8. Practical machine learning")

# Load 2 data, training set & test set
train_set<-read.csv(file="pml-training.csv",header=TRUE,sep=",",na.strings=c("NA","#DIV/0!",""))
test_set<-read.csv(file="pml-testing.csv",header=TRUE,sep=",",na.strings=c("NA","#DIV/0!",""))

# Check the column names & data set
library(Hmisc)
# describe(train_set)

library(caret)
```

I read 2 data set, training data and test data, and check the training data set. 

Specially, I use describe() function, I can check the number of missing values and unique values in each column. However, when I print the result of describe() function, this report will be very long. So in this report, I mark as comment using "#" symbol.

There are 160 columns and 19,622 rows. However, when I check the training data set, I find a few useless columns, and I think it's better to ignore those columns.

## Prepossesing
```{r ignore column}
# Delete the column which has only "NA" value
na_col_train_set<-colSums(is.na(train_set))
col_name<-na_col_train_set==0     # store the column which includes "NA" value
col_name_t<-col_name[col_name==TRUE]
rem_name<-names(col_name_t)
new_train<-subset(train_set,select=rem_name)

# Also, delete the "X" and other columns. I don't need
new_train<-subset(new_train,select=-c(X,user_name,raw_timestamp_part_1,raw_timestamp_part_2,cvtd_timestamp,new_window))
dim(new_train)
```

I exclude "NA" columns, so 100 columns are deleted, and 60 columns are remained. Also, user name & time stamp are not needed, I delete 6 more columns.

## Cross validation

```{r cross validataion}
# Separate the training data and testing data
set.seed(135)
library(caret)     # In order to use createDataPartition() function
inTrain<-createDataPartition(new_train$classe,p=0.6,list=FALSE)
training<-new_train[inTrain,]
testing<-new_train[-inTrain,]
dim(training)
dim(testing)
```

I use createDataPartition() function, and separate training data(60%) and testing data(40%). I am going to adjust various modeling methods using training data, since then check the results correct or not using testing data.

## Tree models
### 1. rpart() function
```{r rpart}
# rpart() function
library(rpart)
rpart_training<-rpart(classe~.,data=training,method="class")
rpart_training
plot(rpart_training,compress=TRUE)
text(rpart_training,cex=0.8)

# Prettier graph
library(rpart.plot)
prp(rpart_training)

# Predict this tree is right or not
# I am going to use predict() function and confusionMatrix() function
library(e1071)
rpart_prediction<-predict(rpart_training,testing,type="class")
confusionMatrix(rpart_prediction,testing$classe)
```

![plot1](figure/plot1.jpg) 

![plot2](figure/plot2.jpg) 

I predict using rpart() function. When I draw a tree using plot() function, a little bit hard to analyze. Therefore I use prp() function, tree graph is much prettier and easy to look at.

Using rpart() function, the accuracy is **76.7%**. So I need to adjust another predictive method.

###2. tree() function
```{r tree}
# tree() function & draw a graph
library(tree)
tree_training<-tree(classe~.,data=training)
tree_training
plot(tree_training)
text(tree_training,cex=0.6)

# Predict testing values
tree_prediction<-predict(tree_training,testing,type="class")
confusionMatrix(tree_prediction,testing$classe)

```

![plot3](figure/plot3.jpg) 

I predict using tree() function. Accuracy is **76.2%**, and it's less than using rpart() function. Thus I will consider another way.

### 3. randomForest() function
```{r random forest}
# randomForest() function
library(randomForest)
rf_training<-randomForest(classe~.,data=training,method="class",importance=TRUE)
rf_training

# draw a graph
varImpPlot(rf_training,main="varImpPlot of train data",cex=0.7)

# check the accuracy
rf_prediction<-predict(rf_training,testing,type="class")
confusionMatrix(rf_prediction,testing$classe)
```

![plot4](figure/plot4.jpg) 

When I use random forest method, the accuracy is **99.8%**, almost 100%. So I think random forest is the best way, and apply to real test set. 

## Conclusion
Let's look at the accuracy
  - rpart : 76.7%
  - tree : 76.2%
  - random forest : 99.8%

```{r test value}
answer<-predict(rf_training,test_set,type="class")
answer
```
