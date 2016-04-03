# Project-Getting-and-Cleaning-Data-
#0. Download the data an open the zip file in the desktop. After that set the working directory to this folder
setwd("C:/****/Desktop/UCI HAR Dataset")


#1.Merges the training and the test sets to create one data set.

# 1.1 With these commands, R reads all the data 
test.subjects <- read.table("test/subject_test.txt", col.names="subject") 
test.labels <- read.table("test/y_test.txt", col.names="label") 
test.data <- read.table("test/X_test.txt") 

train.subjects <- read.table("train/subject_train.txt", col.names="subject") 
train.labels <- read.table("train/y_train.txt", col.names="label") 
train.data <- read.table("train/X_train.txt") 

#1.2 That command merges all the subsets of data to create one data set, with the format of subjects, labels and data (everything else)
Project <- rbind(cbind(test.subjects, test.labels, test.data), 
                 cbind(train.subjects, train.labels, train.data)) 
 

#2.Extracts only the measurements on the mean and standard deviation for each measurement. 

#2.1 This command reads the features 
features <- read.table("features.txt", strip.white=TRUE, stringsAsFactors=FALSE)
features#as you can see

#2.2 This command only retains features of mean and standard deviation 
features.mean.std <- features[grep("mean\\(\\)|std\\(\\)", features$V2), ] 
features.mean.std#as you can see

# 2.3 This command selects only the means and standard deviations from data, it increments by 2 because the first and second column  of the "Project" are subjects and labels
Project.mean.std <- Project[, c(1, 2, features.mean.std$V1+2)] 
 

#3.Uses descriptive activity names to name the activities in the data set

#3.1 This table reads the labels from activities
labels <- read.table("activity_labels.txt", stringsAsFactors=FALSE)

#3.2 This command replaces the labels in "project" with the label names of "labels" 
Project.mean.std$label <- labels[Project.mean.std$label, 2] 
 

#4.Appropriately labels the data set with descriptive variable names.

#4.1 I create a list of the current column names and feature names 
good.colnames <- c("subject", "label", features.mean.std$V2) 

#4.2 Ahter that, I modified that list by removing every non-alphabetic character and converting to lowercase 
good.colnames <- tolower(gsub("[^[:alpha:]]", "", good.colnames)) 

#4.3 An I use the list as column names for Project 
colnames(Project.mean.std) <- good.colnames 
 

#5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

#5.1 With this command find the mean for each combination of subject and label 
Project2 <- aggregate(Project.mean.std[, 3:ncol(Project.mean.std)], 
                                      by=list(subject = Project.mean.std$subject,  
                                      label = Project.mean.std$label),mean) 


#5.2 Finally, this command writes the "project2" and View 
write.table(format(Project2, scientific=T), "Project2.txt", 
                            row.names=F, col.names=F, quote=2) 

View(Project2)
