# cleaning data and using r scripting in m query


demo 1 = summarizing data using summarise.
library(dplyr)
setwd("C:\\Advanced Power BI\\Module Resources\\Module 01")
data <- read.table("Customer Sales.csv", header=TRUE, sep=",")

data2 <- tbl_df(data)
data2 %>%
		summarise(totalpurchases = sum(salesamount))

		
		
# DEMO 2 = GROUP BY
library(dplyr)
setwd("C:\\Advanced Power BI\\Module Resources\\Module 01")
data <- read.table("Customer Sales.csv", header=TRUE, sep=",")

data2 <- tbl_df(data)
data3 <- data2 %>%
	group_by(Customer) %>%
	summarise(TotalPurchases = sum(SalesAmount),
			  TotalTransactions = n()) 
data3

			  
# DEMO 3 = ORDER BY using ARRANGE
library(dplyr)
setwd("C:\\Advanced Power BI\\Module Resources\\Module 01")
data <- read.table("Customer Sales.csv", header=TRUE, sep=",")

data2 <- tbl_df(data)
data3 <- data2 %>%
	group_by(Customer) %>%
	summarise(TotalPurchases = sum(SalesAmount),
			  TotalTransactions = n()) %>%
	arrange(., desc(TotalPurchases)) #new line of code
data3

# DEMO 4 = MUTATE TO ADD A NEW COLUMN FOR PROFIT (SALES - COST)
