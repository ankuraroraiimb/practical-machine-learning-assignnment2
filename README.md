# practical-machine-learning-assignnment2Remove variables that we believe have too many NA values
training.dena <- trainingOrg[ , colSums(is.na(trainingOrg)) == 0]
#head(training1)
#training3 <- training.decor[ rowSums(is.na(training.decor)) == 0, ]
dim(training.dena)

Remove unrelevant variables There are some unrelevant variables that can be removed as they are unlikely to be related to dependent variable.
remove = c('X', 'user_name', 'raw_timestamp_part_1', 'raw_timestamp_part_2', 'cvtd_timestamp', 'new_window', 'num_window')
training.dere <- training.dena[, -which(names(training.dena) %in% remove)]
dim(training.dere)

Check the variables that have extremely low variance (this method is useful nearZeroVar() )
library(caret)
## Loading required package: lattice
## Loading required package: ggplot2
# only numeric variabls can be evaluated in this way.

zeroVar= nearZeroVar(training.dere[sapply(training.dere, is.numeric)], saveMetrics = TRUE)
training.nonzerovar = training.dere[,zeroVar[, 'nzv']==0]
dim(training.nonzerovar

Remove highly correlated variables 90% (using for example findCorrelation() )
corrMatrix <- cor(na.omit(training.nonzerovar[sapply(training.nonzerovar, is.numeric)]))
dim(corrMatrix)
## [1] 52 52
# there are 52 variables.
corrDF <- expand.grid(row = 1:52, col = 1:52)
corrDF$correlation <- as.vector(corrMatrix)
levelplot(correlation ~ row+ col, corrDF)

Split data to training and testing for cross validation.
inTrain <- createDataPartition(y=training.decor$classe, p=0.7, list=FALSE)
training <- training.decor[inTrain,]; testing <- training.decor[-inTrain,]
dim(training);dim(testing)
## [1] 13737    46
## [1] 5885   46

Random forests build lots of bushy trees, and then average them to reduce the variance.
require(randomForest)
## Loading required package: randomForest
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
set.seed(12345)
Lets fit a random forest and see how well it performs.
rf.training=randomForest(classe~.,data=training,ntree=100, importance=TRUE)
rf.training
## 
## Call:
##  randomForest(formula = classe ~ ., data = training, ntree = 100,      importance = TRUE) 
##                Type of random forest: classification
##                      Number of trees: 100
## No. of variables tried at each split: 6
## 
##         OOB estimate of  error rate: 0.72%
## Confusion matrix:
##      A    B    C    D    E class.error
## A 3899    4    0    1    2 0.001792115
## B   23 2622   13    0    0 0.013544018
## C    0   13 2379    4    0 0.007095159
## D    0    0   27 2224    1 0.012433393
## E    0    1    2    8 2514 0.004356436
#plot(rf.training, log="y")
varImpPlot(rf.training,)

Our Random Forest model shows OOB estimate of error rate: 0.72% for the training data. Now we will predict it for out-of sample accuracy.
Now lets evaluate this tree on the test data.

Now we can predict the testing data from the website.
answers <- predict(rf.training, testingOrg)
answers
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E

