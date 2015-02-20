# GetData
Get and Cleaning Data assignment
#Load the labels and features first
labels<-read.table("UCI HAR Dataset/activity_labels.txt")
colnames(labels)<-c("activity.label","activity")
feat<-read.table("UCI HAR Dataset/features.txt")[,2]

#Save the column of the features we want to keep
ColWanted<-grep("mean|std",feat)

#Load the test data
X_test<-read.table("UCI HAR Dataset/test/X_test.txt")
y_test<-read.table("UCI HAR Dataset/test/y_test.txt")
subject_test<-read.table("UCI HAR Dataset/test/subject_test.txt")

#Keep the relevant data from X, rename the columns, add the label and the subject
data_test<-X_test[,ColWanted]
colnames(data_test)<-feat[ColWanted]
data_test$activity.label<-y_test$V1
data_test$subject<-subject_test$V1

#Load the train data
X_train<-read.table("UCI HAR Dataset/train/X_train.txt")
y_train<-read.table("UCI HAR Dataset/train/y_train.txt")
subject_train<-read.table("UCI HAR Dataset/train/subject_train.txt")

#Keep the relevant data from X, rename the columns, add the label and the subject
data_train<-X_train[,ColWanted]
colnames(data_train)<-feat[ColWanted]
data_train$activity.label<-y_train$V1
data_train$subject<-subject_train$V1

#merge the two sets of data
data<-rbind(data_train,data_test)

#add the activity corresponding to each label to the dataset, and remove the activity label
data<-merge(data,labels, by.x="activity.label",by.y="activity.label")[,-1]

## aggregate the data by calculating the mean by activity and subject (except on activity and subject columns)
tidydata<-aggregate(data[,-c(80,81)],by=list(activity=data$activity,subject=data$subject),mean)

#create the tidydata file
write.table(tidydata, "tidydata.txt",row.name=FALSE)
