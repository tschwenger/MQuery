# Example of looping over a folder and scheduling ETL via Power query

fileexists <- FALSE
fileName   <- "C:\\Advanced Power BI\\Module Resources\\Module 01\\Customer Sales1.csv"
while (fileexists == FALSE) 
	{
   #fileexists = TRUE ##Test Expression
   fileexists <- 
			if(
				file.exists(fileName))
				{TRUE} else {FALSE}
   #print(fileexists) 
   ## Add 10 second Delay
   Sys.sleep(10)	
	}
	
	data  = read.csv(fileName)
	head(data)
