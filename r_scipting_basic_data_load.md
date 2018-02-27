# Basic example of using r scipting to load in data and perform a basic manipulation

library(dplyr)
setwd("C:\\Advanced Power BI\\Module Resources\\Module 01")
data <- read.table("TotalSales_by_Customer.csv", header=TRUE, sep=",")
data2 <- tbl_df(data)
select(
	filter(data2, MaxPurchaseYear == 2014),
	-Avg_UnitPrice, -TotalPromotions)
	
