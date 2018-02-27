# Example of looping over a directory and loading multiple files using r scripting

# Load Library
library(data.table)

# Get a List of all files named with a key word, say all `.csv` files
  filenames <- list.files("C:\\Advanced Power BI\\Module Resources\\Module 01\\Multiple Files", pattern="*.csv", full.names=TRUE)
  #head(filenames) ## SHOW THE LIST OF FILE NAMES MATCHING THE PATTERN.....
# Load data sets
  data <- rbindlist(lapply(filenames,fread)) # fread is equilavent to read.table but much faster.
  head(data)
