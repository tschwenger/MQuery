# example to create a list and bring back dates. 

can be applied to a query to make dynamic

let
    Source = {2010..Date.Year(Date.AddMonths(DateTime.LocalNow(),-8))}
in
    Source
    
## example of creatign another basic list

let
    Source = {1,2,3}
in
    Source
    
## creating an array

let
    Source = [x=1,w=3,t=5]
in
    Source
    
## creating a table from manually entered data within the query

let
    Source = Table.FromRecords({[x=1,y=3,z=2],[x=2,y=3,z=4]})
in
    Source
