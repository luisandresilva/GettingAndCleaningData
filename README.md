### Pre-processing

#### Download and extract original data
```r
library(downloader)
download("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip", 
         dest="dataset.zip", mode="wb") 
unzip ("dataset.zip", exdir = "./")
```
#### Read test data
```r
test_features <- read.table("UCI HAR Dataset/test/X_test.txt", 
                            header=FALSE, stringsAsFactors = FALSE)
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", 
                           header=FALSE, stringsAsFactors = FALSE)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", 
                     header=FALSE, stringsAsFactors = FALSE)
test <- cbind(subject_test,test_features,y_test)
```
#### Read train data
```r
train_features <- read.table("UCI HAR Dataset/train/X_train.txt", 
                             header=FALSE, stringsAsFactors = FALSE)
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", 
                            header=FALSE, stringsAsFactors = FALSE)
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", 
                            header=FALSE, stringsAsFactors = FALSE)
train <- cbind(subject_train,train_features,y_train)
```
### Merge test and train datasets
```r
full <- rbind(test,train)
```
#### Read feature Names
```r
feature_names <- read.table("UCI HAR Dataset/features.txt", 
                            header=FALSE, stringsAsFactors = FALSE)
columns <- as.vector(feature_names[,2])
columns <- c("Subject", columns, "Label")
```
#### Set column names
```r
colnames(full) <- columns
```
### Extract only the measurements on the mean and std for each measurement
```r
extracted <- full[,grep("(Subject|Label|[-]mean|[-]std)",columns)]
```
#### Use descriptive activity names to name the activities in the data set
```r
activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt", 
                            header=FALSE, stringsAsFactors = TRUE)
descriptive <- merge(extracted,activity_labels,by.x = "Label", by.y = "V1")
colnames(descriptive)[82] <- "Activity"
columns <- colnames(descriptive)
```
### Create a second, independent tidy data set with the average of each activity and each subject
```r
tidy_dataset <- aggregate(descriptive[,grep("mean|std",columns)], 
                          by=descriptive[,c("Activity","Subject")], mean)
write.table(tidy_dataset,"tidy_dataset.txt")
```