Getting and Cleaning Data Project
===================
The following explains the script code of run_analysis.R:

```r
cat("\014")
rm(list=ls())
```
First clears the console and Data environment.
```r
X_test<-read.table("X_test.txt")
Y_test<-read.table("y_test.txt",col.names="Activity")
subject_test<-read.table("subject_test.txt",col.names="Subject")
```
Loads text files X_test.txt, y_test.txt (changes the column label of subject_train to "Activity" which was "V1" before) and subject_test.txt (changes the column label of subject_test to "Subject" which was "V1" before).
```r
X_train<-read.table("X_train.txt")
Y_train<-read.table("y_train.txt",col.names="Activity")
subject_train<-read.table("subject_train.txt",col.names="Subject")
```r
Loads text files X_train.txt, y_train.txt (changes the column label of subject_train to "Activity" which was "V1" before) and subject_train.txt (changes the column label of subject_train to "Subject" which was "V1" before).
```r
test <- cbind(subject_test,Y_test,X_test)
```
Creats a new data frame named "test" by combining the columns of subject_test, Y_test and X_test respectively.
```r
train <- cbind(subject_train,Y_train,X_train)
```
Creats a new data frame named "train" by combining the columns of subject_train, Y_train and X_train respectively.

```r
all <- rbind(test,train)
```
Creats a new data frame named "all" by combining the rows of test and train respectively.
```r
features<-read.table("features.txt")
setnames(all, names(all)[3:length(names(all))], as.character(features[,2]))
```
Loads "features.txt" and renames the labels of columns in "all" data frame with the values in the second column of "features". The first two labels are kept unchanged as they are "Subject" and "Activity".
```r
activity_labels<-read.table("activity_labels.txt")
all$Activity<-sapply(all$Activity, function(x)
   activity_labels[which(activity_labels[,1] %in% x),2])
```
Loads "activity_labels.txt" and replace numbers in "Activity" column of "all" with the coresponding values in the second column of "activity_lables".
```r
m<-c("TRUE","TRUE",grepl("mean", features[,2]) | grepl("std", features[,2]))
all<-all[,which(m %in% "TRUE")]
```
Finds the position of the features including words "mean" and "std" and extracts the corespoding columns of "all". It also keeps the first two columns which are "Activity" and "Subject".
```r
all<-all[,1:72]
```
Some last features' names seem to be wrong (i.e. fBodyBodyGyroJerkMag-meanFreq()) and they are removed in this code line. In the case that we are interested to keep them anyway, we can skip this line!
```r
all<-melt(all,id=c("Activity","Subject"), measure.vars=names(all)[3:72])
```
Here "all" is reshaped by melt function where "Activity" and "Subject" are ids and the rest 69 variables are measures.
```r
tidy_data <- dcast(all, Subject + Activity ~ variable, mean)
```
Now based on "Subject" and "Activity" dcast function calculates the mean for all the measure variables ang generates a wide tidy data frame (called "tidy_data").
```r
write.table(tidy_data,file="tidy_data.txt")
```
Finaly "tidy_data" is saved as a text file named "tidy_data.txt"
