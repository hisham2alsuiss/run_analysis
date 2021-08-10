library(dplyr)
fileZipname<- "Coursera_DS3_Final.zip"
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip",fileZipname, method = "curl")
if (!file.exists("UCI HAR Dataset")) { 
  unzip(filename)}
Features <- read.table("UCI HAR Dataset/features.txt", col.names = c("Number","Functions"))
Activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("Code", "Activity"))
Subject_Test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "Subject")
X_Test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = Features$Functions)
Y_Test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "Code")
Subject_Train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "Subject")
X_Train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = Features$Functions)
Y_Train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "Code")  
X <- rbind(X_Train, X_Test)
Y <- rbind(Y_Train, Y_Test)
Subject <- rbind(Subject_Train, Subject_Test)
Merged_Data <- cbind(Subject, Y, X)
TidyData <- Merged_Data %>% select(Subject, Code, contains("mean"), contains("std"))
TidyData$code <- Activities[TidyData$Code, 2]
names(TidyData)[2] = "Activity"
names(TidyData)<-gsub("Acc", "Accelerometer", names(TidyData))
names(TidyData)<-gsub("Gyro", "Gyroscope", names(TidyData))
names(TidyData)<-gsub("BodyBody", "Body", names(TidyData))
names(TidyData)<-gsub("Mag", "Magnitude", names(TidyData))
names(TidyData)<-gsub("^t", "Time", names(TidyData))
names(TidyData)<-gsub("^f", "Frequency", names(TidyData))
names(TidyData)<-gsub("tBody", "TimeBody", names(TidyData))
names(TidyData)<-gsub("-mean()", "Mean", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-std()", "STD", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-freq()", "Frequency", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("angle", "Angle", names(TidyData))
names(TidyData)<-gsub("gravity", "Gravity", names(TidyData))
FinalData <- TidyData %>%
  group_by(Subject, Activity) %>%
  summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)
