#create temporary file
temp <-tempfile()

#download zip to temp file location
urlziplocation = "https://file.ac/i4Zvnt8wSgo/Baseball.zip"
download.file(urlziplocation, temp)

#
CSVFile = unz(temp,"Teams.csv")

#load new data frame from file
teams <- read.csv(CSVFile)

#cleanup temp files
unlink(temp)
remove(CSVFile)
remove(temp)
remove(urlziplocation)
