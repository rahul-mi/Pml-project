library(caret);
library(randomForest);

#create data sets
trainingCSV <- read.csv("pml-training.csv")
inTrain <- createDataPartition(trainingCSV$classe,p=0.70, list=FALSE)
training <- trainingCSV[inTrain,]
validation <- trainingCSV[-inTrain,]
testing <- read.csv("pml-testing.csv")

#removing non numeric columns 
training <- training[-(1:7)]

#removing columns with any NA
training.naNum <- colSums(sapply(training, is.na)) 
training <- training[,training.naNum == 0]

#removing columns with very less variance
nzv <- nearZeroVar(training) 
training <- training[-nzv]

#checking the number of column remaining
length(training)

#fitting model with random forest method
modFit <- randomForest(classe ~ ., data=training)

#model performance
modPerf <- predict(modFit, newdata=validation) 
confusionMatrix(modPerf,validation$classe)

#run on test set
classify <- predict(modFit , newdata = testing) 
classify
