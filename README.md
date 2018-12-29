Getting-and-cleaning-data-course-project
  The purpose of this project is to collect, work with, and clean a wearable computing data set. The goal is to prepare tidy data that can be used for later analysis. 
  One of the most exciting areas in all of data science right now is wearable computing.
  Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. 
  The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. 
  A full description is available at the site where the data was obtained - link below:
  http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 
Files overview:
  Context: The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. 
  Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone.
  Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz.
  activity_labels.txt': Links the class labels with their activity name
  features.txt': List of all features

Project approach 
  download zip file 
  read and analyze data using read.table, str, summary, View etc command 
  calling relevant packages - dplyr  
  read labels and vectors
  give appropriate names to columns
  create a combined dataset using cbind and rbind functions
  View combined data 
  exracing columns with mean and std data 
  create a second, independent tidy data set with the average of each variable for each activity and each subject.
  
code and detail steps are as follow: 
 
download & unzip dataset 
Library(dplyr)

if(file.exists("./data")){dir.create("./data")}
fileurl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileurl, destfile = "./data/Dataset.zip")

unzip(zipfile = "./data/Dataset.zip", exdir = "./data")

## Reading various files from dataset

## test data
x_test <- read.table("./data/UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./data/UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./data/UCI HAR Dataset/test/subject_test.txt")

## train data
x_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./data/UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")

## reading labels and vectors
dataactivity_labels <-read.table("./data/UCI HAR Dataset/activity_labels.txt")
datafeatures <-read.table("./data/UCI HAR Dataset/features.txt")


## give names to columns 

colnames(x_test) <-datafeatures[,2]
colnames(y_test) <-"ActID"
colnames(subject_test) <-"SubID"

colnames(x_train) <-datafeatures[,2]
colnames(y_train) <-"ActID"
colnames(subject_train) <-"SubID"



## Merges the training and the test sets to create one data set

testdataset <- cbind(y_test, subject_test, x_test)
traindataset <- cbind(y_train, subject_train, x_train)
combinedataset <-rbind(traindataset, testdataset)

View(head(combineddataset))

## Extracts only the measurements on the mean and standard deviation for each measurement

## To create unique columns 
validate.names = function(df){
  rtn = df
  valid_column_names = make.names(names=names(df), unique=TRUE, allow_ = TRUE)
  names(rtn) = valid_column_names
  rtn
}
combinedataset1 <- combinedataset%>% validate.names

## select only columns with mean and std measurements

MeanstdData <- combinedataset1 %>%
  select(ActID, SubID, contains("mean"), contains("std"))

## Uses descriptive activity names to name the activities in the data set 
## & Appropriately labels the data set with descriptive variable names. 

MeanstdDataNames <- merge(MeanstdData, dataactivity_labels, by.x = "ActID", by.y = "V1", all= TRUE)

## From the data set in step 4, creates a second, independent tidy data set
## ...with the average of each variable for each activity and each subject

Secdataset <- aggregate(MeanstdDataNames,list(SubID1 = MeanstdDataNames$SubID, ActID1 = MeanstdDataNames$ActID ), mean, simplify = TRUE)
View(head(Secdataset))


## create a txt file

write.table(Secdataset, file = "secdataset.txt", row.names = FALSE)
  
  
