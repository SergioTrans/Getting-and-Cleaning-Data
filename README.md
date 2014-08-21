Getting-and-Cleaning-Data
=========================

Getting and Cleaning Data from Coursera



```# Remember to set your corrent workspace where the folder UCI HAR Database is!!!

# Reading data from train folder and add on a single table
TrainData <- read.table("UCI HAR Dataset/train/X_train.txt")
TrainData[,562] <- read.table("UCI HAR Dataset/train/y_train.txt")
TrainData[,563] <- read.table("UCI HAR Dataset/train/subject_train.txt")

# Reading data from test folder and add on a single table
TestData <- read.table("UCI HAR Dataset/test/X_test.txt")
TestData[,562] <- read.table("UCI HAR Dataset/test/y_test.txt")
TestData[,563] <- read.table("UCI HAR Dataset/test/subject_test.txt")

# Reading data for labeling what people are doing
ActivLabels <- read.table("UCI HAR Dataset/activity_labels.txt")

# Reading data from sensors on person
FeatData <- read.table("UCI HAR Dataset/features.txt")

# Finding some patterns in labels of sensors and renaming for more readable
FeatData[,2] <- gsub('-mean', 'Mean', FeatData[,2])
FeatData[,2] <- gsub('-std', 'SDev', FeatData[,2])
FeatData[,2] <- gsub('[-()]', '', FeatData[,2])

# Joining by row the train and test data sets
JoinData <- rbind(TrainData, TestData)

# Finding the columns we will use searching among all by name patterns
SelectCol <- grep(".*Mean.*|.*SDev.*", FeatData[,2])

# Reducing the table of labels to desirable labels (columns with mean and sd)
FeatData <- FeatData[SelectCol,]

# Adding the position and person labels to the previous selection of labels
SelectCol <- c(SelectCol, 562, 563)

# Removing unused columns from previous joined data 
JoinData <- JoinData[,SelectCol]

# Adding the column names to joined data
colnames(JoinData) <- c(FeatData$V2, "position", "person")

# Creating the tidy data set by aggregating joined data by activity and person executing it
FinalData <- aggregate.data.frame(JoinData, by=list(Position = JoinData$position, Person=JoinData$person), mean)

# replacing the activity name in each row of tidy data
CurrActiv = 1
for (CurrActivLbl in ActivLabels$V2) {
  FinalData$Position <- gsub(CurrActiv, CurrActivLbl, FinalData$Position)
  CurrActiv <- CurrActiv + 1
}

# Delecting previous columns of position and person since are replaced by new aggregated ones
FinalData$position <- NULL
FinalData$person <- NULL

# finally, writing the file from tidy data set in csv format
write.table(FinalData, "TidyDataSet.txt", sep=",")

# ...That's All Folks!!!```
