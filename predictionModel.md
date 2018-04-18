I first opened the training data and glanced the columns. I found that there are 160 columns. Many columns are with empty data or non-numerical data or having #DIV/0!. I removed these by choosing columns without any NA values and columns with non-zero variance. This reduced the number of columns to 53. I also created a set of 30% data from training set for cross validation later. After that I used the prediction model of random forests and found the accuracy of the model > 99% and therefore chose this to classify the 20 data points in the testing set as well. I got 100% answers correct in the Predction quiz.

```
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

#model performance/cross-validation
modPerf <- predict(modFit, newdata=validation) 
confusionMatrix(modPerf,validation$classe)

#run on test set
classify <- predict(modFit , newdata = testing) 
classify
```


Confusion Matrix and Statistics

             A    B    C    D    E
         A 1674    6    0    0    0
         B    0 1130    7    0    0
         C    0    3 1019   11    2
         D    0    0    0  953    5
         E    0    0    0    0 1075

Overall Statistics
               Accuracy : 0.9942         
                 95% CI : (0.9919, 0.996)
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                  Kappa : 0.9927         

Statistics by Class
                     Class A Class B Class C Class D Class E
Sensitivity            1.0000   0.9921   0.9932   0.9886   0.9935
Specificity            0.9986   0.9985   0.9967   0.9990   1.0000
