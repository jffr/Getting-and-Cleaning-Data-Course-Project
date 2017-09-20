# Getting-and-Cleaning-Data-Course-Project

Verify directory and download zip file:

    if(!file.exists("./data")){dir.create("./data")}
    fileUrl = "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
    download.file(fileUrl, destfile="./data/Dataset.zip")

Then unzip file "Dataset.zip"

Part 1. Merges the training and the test sets to create one data set:

    # Reads files and creates data frames
    features <- read.csv("./data/UCI HAR Dataset/features.txt", sep = "", header=FALSE)
    
    # features$V2 is the second column
    train <- read.csv("./data/UCI HAR Dataset/train/X_train.txt", sep = "", header=FALSE, col.names = features$V2)
    test  <- read.csv("./data/UCI HAR Dataset/test/X_test.txt", sep = "", header=FALSE, col.names = features$V2)

    train_labels  <- read.csv("./data/UCI HAR Dataset/train/y_train.txt", header=FALSE, col.names = "label")
    test_labels  <- read.csv("./data/UCI HAR Dataset/test/y_test.txt", header=FALSE, col.names = "label")
    
    # combine by columns
    train <- train %>% cbind(train_labels)
    test  <- test %>% cbind(test_labels)

    train_subject <- read.csv("./data/UCI HAR Dataset/train/subject_train.txt", header=FALSE, col.names = "subject")
    test_subject  <- read.csv("./data/UCI HAR Dataset/test/subject_test.txt", header=FALSE, col.names = "subject")

    train2 <- train %>% cbind(train_subject)
    test2  <- test %>% cbind(test_subject)
    
    # combine by rows
    merge_train_test = rbind(train2,test2)

    activity_labels <- read.csv("./data/UCI HAR Dataset/activity_labels.txt", sep = "", header=FALSE, col.names = c("label","activity"))
    # search the descriptive activity names
    merge_train_test <- merge_train_test %>% mutate(activity = activity_labels$activity[match(merge_train_test$label, activity_labels$label)])

Part 2. Extracts only the measurements on the mean and standard deviation for each measurement.
    
    # search mean() or std()
    col_mean <- grep("mean[^a-zA-Z]",names(merge_train_test),value = TRUE)
    col_std <- grep("std[^a-zA-Z]",names(merge_train_test),value = TRUE)

    measurement_mean_std <- merge_train_test %>% select(c(col_mean, col_std))

Part 3. Uses descriptive activity names to name the activities in the data set

    # I did in part 1 with data frames "activity_labels" and "merge_train_test"

Part 4. Appropriately labels the data set with descriptive variable names.

    # I did in part 1 with data frames "features", "train" and "test"

Part 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

    second_data <- merge_train_test %>% select(c(col_mean, col_std, "activity", "subject"))

    second_data_average <- second_data %>% group_by(activity,subject) %>% summarise_all(mean)
    # second tidy data set
    second_data_average

    #write.table(second_data_average, file="./data/tidy_data_step5.txt", row.names=FALSE)
