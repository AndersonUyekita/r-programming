# [Week 2][R Programming] Programming Assignment 1: Air Pollution

#### Tags

* Specialization: Data Science: Foundations using R Specialization
* Course: R Programming
    * Chapter: Week 2
    * Instructor: Roger D. Peng
    * URL: https://www.coursera.org/learn/r-programming
    * Rpubs URL: [Programming Assignment 1: Air Pollution](https://rpubs.com/AndersonUyekita/r_programming_air_pollution)
* Date: 13/05/2022

***

## Sinopsis

This Programming Assignment is part of the R Programming course and consists of three parts, each one with one function development:

* Part 1: The [`pollutantmean()`](https://github.com/AndersonUyekita/r_programming-programming_assignment_1/blob/main/R/pollutantmean.R) function calculates the mean of a specific pollutant (could be sulfate or nitrate), excluding any `NA` observation.
* Part 2: The [`complete()`](https://github.com/AndersonUyekita/r_programming-programming_assignment_1/blob/main/R/complete.R) function analyses the number of non-NA observations of each monitor id.
* Part 3: The [`corr()`](https://github.com/AndersonUyekita/r_programming-programming_assignment_1/blob/main/R/corr.R) function calculates the correlation between nitrate and sulfate, excluding rows with `NA` observations.

***

## Programming Assignment 1 INSTRUCTIONS: Air Pollution

##### Introduction

For this first programming assignment you will write three functions that are meant to interact with dataset that accompanies this assignment. The dataset is contained in a zip file **specdata.zip** that you can download from the Coursera web site.

**Although this is a programming assignment, you will be assessed using a separate quiz.**

##### Data

The zip file containing the data can be downloaded here:

* [specdata.zip](https://d396qusza40orc.cloudfront.net/rprog%2Fdata%2Fspecdata.zip) [2.4MB]

The zip file contains 332 comma-separated-value (CSV) files containing pollution monitoring data for fine particulate matter (PM) air pollution at 332 locations in the United States. Each file contains data from a single monitor and the ID number for each monitor is contained in the file name. For example, data for monitor 200 is contained in the file "200.csv". Each file contains three variables:

* Date: the date of the observation in YYYY-MM-DD format (year-month-day)
* sulfate: the level of sulfate PM in the air on that date (measured in micrograms per cubic meter)
* nitrate: the level of nitrate PM in the air on that date (measured in micrograms per cubic meter)

For this programming assignment you will need to unzip this file and create the directory 'specdata'. Once you have unzipped the zip file, do not make any modifications to the files in the 'specdata' directory. In each file you'll notice that there are many days where either sulfate or nitrate (or both) are missing (coded as NA). This is common with air pollution monitoring data in the United States.

```r
# Download the specdata.zip
download.file(url = "https://d396qusza40orc.cloudfront.net/rprog%2Fdata%2Fspecdata.zip",
              destfile = "./R Programming/specdata.zip")

# unzip the specdata and extract it in R Programming folder.
unzip(zipfile = "./R Programming/specdata.zip", exdir = "./R Programming", overwrite = TRUE)
```

### Part 1

Write a function named 'pollutantmean' that calculates the mean of a pollutant (sulfate or nitrate) across a specified list of monitors. The function 'pollutantmean' takes three arguments: 'directory', 'pollutant', and 'id'. Given a vector monitor ID numbers, 'pollutantmean' reads that monitors' particulate matter data from the directory specified in the 'directory' argument and returns the mean of the pollutant across all of the monitors, ignoring any missing values coded as NA. A prototype of the function is as follows

You can see some example output from this function below. The function that you write should be able to match this output.

```
# Expected values from the pollutantmean() function:

pollutantmean("specdata", "sulfate", 1:10)
## [1] 4.064128

pollutantmean("specdata", "nitrate", 70:72)
## [1] 1.706047

pollutantmean("specdata", "nitrate", 23)
## [1] 1.280833
```

Please save your code to a file named pollutantmean.R.

#### Solution

```r
# Function to solve the Part 1.
# This function calculate sulfate/nitrate from monitor files.
pollutantmean <- function(directory, pollutant, id = 1:332) {
    # directory: Folder with all monitor's data
    # pollutant: nitrate or sulfate
    # id: Varies from 1 to 332

    # Inner Function to ease the loading process
    read_data <- function(monitor_id, directory, path = "R Programming") {
        # monitor_id: The number of Monitor File
        # directory: default folder
        # path: The location where I have stored the files

        # Create the file name.
        # Should be like this: "R Programming/specdata/001.csv"
        file_name <- base::paste0(stringr::str_pad(string = monitor_id, #
                                                   width =  3,          # Creates the zeros on left
                                                   pad = "0"),          #                       00X
                                  ".csv")                               # Insert the file type.
        
        # Given the file_name and path, this will import the CSV file.
        df <- utils::read.csv(file = base::file.path(path,        # file.path should create a string to be used as path
                                                     directory,   # "./R Programming/specdata/00X.csv"
                                                     file_name))  # 
        
        # Return the data frame.
        return(df)
    }
    
    # Decision Tree.
    
    # Single monitor analysis.
    if (base::length(id) == 1) {
        # If the operator inserts a wrong number of monitor (negative number, zero or above of 322)
        if (id <= 0 | id > base::length(list.files(path = "R Programming/specdata"))) {
            # Message to the operator.
            return(base::print("Please, check your input to ID. Should be between 1 and 322."))
        }
        
        # All other aceptable conditions.
        else {
            df_raw <- read_data(monitor_id = id, directory = directory, path = "R Programming")
            # return the result of Inner Function to read the monitor files.
            return(base::mean(x = df_raw[[pollutant]], na.rm = TRUE))
        }
    }
    
    # Multiple monitor analysis.
    else {
        # It tracks an id out of the boundaries of 1 and 332.
        # If the operator selects one or more ID out of the boundaries the sum should be greater than zero
        if (base::sum(id > 332 | id <= 0) > 0) {
            # Message to the operator.
            return(base::print("Please, check your input to ID. Should be between 1 and 322."))
        }
        
        # All other acceptable conditions with multiple monitors analysis.
        if (base::length(id) > 1 & base::length(id) <= base::length(base::list.files(path = "R Programming/specdata"))) {
            # Data Frame initialization.
            df_raw <- data.frame()
            
            # Loop to create a dataset with all data of monitors from id.
            for (i in id) {
                # Stack two dataframes to create a bigger one.
                df_raw <- rbind(df_raw,
                                read_data(monitor_id = i,          #
                                          directory = directory,   # Inner function to read the monitor file.
                                          path = "R Programming")) #
            }
            # Return the result of the Inner Function to read the monitor files.
            return(base::mean(x = df_raw[[pollutant]], na.rm = TRUE))
        }
    }
}
```

### Part 2

Write a function that reads a directory full of files and reports the number of completely observed cases in each data file. The function should return a data frame where the first column is the name of the file and the second column is the number of complete cases. A prototype of this function follows

You can see some example output from this function below. The function that you write should be able to match this output. Please save your code to a file named complete.R.

```
# Expected values from the complete() function:

complete("specdata", 1)
##   id nobs
## 1  1  117

complete("specdata", c(2, 4, 8, 10, 12))
##   id nobs
## 1  2 1041
## 2  4  474
## 3  8  192
## 4 10  148
## 5 12   96

complete("specdata", 30:25)
##   id nobs
## 1 30  932
## 2 29  711
## 3 28  475
## 4 27  338
## 5 26  586
## 6 25  463

complete("specdata", 3)
##   id nobs
## 1  3  243
```
To run the submit script for this part, make sure your working directory has the file complete.R in it.

#### Solution

```r
# Function to solve the Part 2.
# This function calculate the number of validy observation from each monitor file.
complete <- function(directory, id) {
    # directory: Folder with all monitor's data
    # id: Varies from 1 to 332

    # Inner Function to ease the loading process
    read_data <- function(monitor_id, directory, path = "R Programming") {
        # monitor_id: The number of Monitor File
        # directory: default folder
        # path: The location where I have stored the files

        # Create the file name.
        # Should be like this: "R Programming/specdata/001.csv"
        file_name <- base::paste0(stringr::str_pad(string = monitor_id, #
                                                   width =  3,          # Creates the zeros on left
                                                   pad = "0"),          #                       00X
                                  ".csv")                               # Insert the file type.
        
        # Given the file_name and path, this will import the CSV file.
        df <- utils::read.csv(file = base::file.path(path,        # file.path should create a string to be used as path
                                                     directory,   # "./R Programming/specdata/00X.csv"
                                                     file_name))  # 
        
        # Return the data frame.
        return(df)
    }
    
    # Decision Tree.
    
    # Single monitor analysis.
    if (base::length(id) == 1) {
        # If the operator inserts a wrong number of monitor (negative number, zero or above of 322)
        if (id <= 0 | id > base::length(list.files(path = "R Programming/specdata"))) {
            # Message to the operator.
            return(base::print("Please, check your input to ID. Should be between 1 and 322."))
        }
        
        # All other aceptable conditions.
        else {
            # Read a single monitor
            df_raw <- read_data(monitor_id = id, directory = directory, path = "R Programming")
            
            # Select only complete rows (without any NA)
            df_tidy <- stats::na.omit(df_raw)   # This function (na.omit) is way better
                                                # It has the same result of:
                                                # df_raw[!is.na(df_raw$sulfate) & !is.na(df_raw$nitrate),]
            
            # Count the number of complety rows
            nobs <- base::nrow(df_tidy)

            # Return the dataframe with columns: ID and nobs
            return(data.frame(id,nobs))
        }
    }
    
    # Multiple monitor analysis.
    else {
        # It tracks an id out of the boundaries of 1 and 322.
        # If the operator selects one or more ID out of the boundaries the sum should be greater than zero
        if (base::sum(id > 332 | id <= 0) > 0) {
            # Message to the operator.
            return(base::print("Please, check your input to ID. Should be between 1 and 322."))
        }
        
        # All other acceptable conditions with multiple monitors analysis.
        if (base::length(id) > 1 & base::length(id) <= base::length(base::list.files(path = "R Programming/specdata"))) {
            # Data Frame initialization.
            df_raw <- data.frame()
            nobs <- base::vector()
            
            # Loop to create a dataset with all data of monitors from id.
            for (i in id) {
                # Stack two dataframes to create a bigger one.
                df_raw <- read_data(monitor_id = i,          #
                                    directory = directory,   # Inner function to read the monitor file.
                                    path = "R Programming")
                
                # Select only complete rows (without any NA)
                df_tidy <- stats::na.omit(df_raw)   # This function (na.omit) is way better
                                                    # It has the same result of:
                                                    # df_raw[!is.na(df_raw$sulfate) & !is.na(df_raw$nitrate),]
                
                # Count the number of complety rows
                nobs <- base::append(nobs, base::nrow(df_tidy))
                
            }
            # Return the dataframe with columns: ID and nobs
            return(data.frame(id,nobs))
        }
    }
}
```

### Part 3

Write a function that takes a directory of data files and a threshold for complete cases and calculates the correlation between sulfate and nitrate for monitor locations where the number of completely observed cases (on all variables) is greater than the threshold. The function should return a vector of correlations for the monitors that meet the threshold requirement. If no monitors meet the threshold requirement, then the function should return a numeric vector of length 0. A prototype of this function follows

```
# Expected results from corr function:
cr <- corr("specdata", 150)
head(cr)
## [1] -0.01895754 -0.14051254 -0.04389737 -0.06815956 -0.12350667 -0.07588814
summary(cr)
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## -0.21057 -0.04999  0.09463  0.12525  0.26844  0.76313

cr <- corr("specdata", 400)
head(cr)
## [1] -0.01895754 -0.04389737 -0.06815956 -0.07588814  0.76312884 -0.15782860
summary(cr)
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## -0.17623 -0.03109  0.10021  0.13969  0.26849  0.76313

cr <- corr("specdata", 5000)
summary(cr)     
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## 

length(cr)
## [1] 0

cr <- corr("specdata")
summary(cr)
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## -1.00000 -0.05282  0.10718  0.13684  0.27831  1.00000

length(cr)
## [1] 323
```

#### Solution

```r
# This function calculate the correlation between nitrate and sulfate from validy observations.
corr <- function(directory, threshold = 0) {
    
    # Calculate all nobs from all monitors
    table_nobs <- complete(directory, 1:332)
    
    # Filter the monitor within the threshold
    table_threshold <- table_nobs[table_nobs$nobs > threshold,]
    
    # Inner Function to ease the loading process
    read_data <- function(monitor_id, directory, path = "R Programming") {
        # monitor_id: The number of Monitor File
        # directory: default folder
        # path: The location where I have stored the files

        # Create the file name.
        # Should be like this: "R Programming/specdata/001.csv"
        file_name <- base::paste0(stringr::str_pad(string = monitor_id, #
                                                   width =  3,          # Creates the zeros on left
                                                   pad = "0"),          #                       00X
                                  ".csv")                               # Insert the file type.
        
        # Given the file_name and path, this will import the CSV file.
        df <- utils::read.csv(file = base::file.path(path,        # file.path should create a string to be used as path
                                                     directory,   # "./R Programming/specdata/00X.csv"
                                                     file_name))  # 
        
        # Return the data frame.
        return(df)
    }
    
    # Vector to store the Correlation. According to the statement must be NUMERIC.
    correlation <- vector(mode = "numeric")
    
    # Loop to calculate all ID within the threshold
    for (j in table_threshold$id) {
        # Create a dataset with no NA.
        df_tidy <- stats::na.omit(read_data(monitor_id = j, directory = directory, path = "R Programming"))
        
        # Adds the new corr data to the vector of correlation.
        correlation <- base::append(correlation, stats::cor(df_tidy$sulfate, df_tidy$nitrate))
        
    }
    
    # Insert a new column to the table_threshold. It will not be RETURNED but it is good to debug and general understanding.
    table_threshold['corr'] <- correlation
    
    # Return only the correlation of monitor within the threshold
    return(correlation)
}

```
