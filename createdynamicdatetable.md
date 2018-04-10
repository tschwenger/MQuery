# Create your own datedim table in Power Query

    let

    Source = #date(2017, 1, 1),

    Custom1 = List.Dates(Source, Number.From(DateTime.LocalNow())- Number.From(Source) ,#duration(1,0,0,0)),
    #"Converted to Table" = Table.FromList(Custom1, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Date"}}),
    #"Inserted Year" = Table.AddColumn(#"Renamed Columns", "Year", each Date.Year([Date]), type number),
    #"Inserted Start of Year" = Table.AddColumn(#"Inserted Year", "StartOfYear", each Date.StartOfYear([Date]), type date),
    #"Inserted End of Year" = Table.AddColumn(#"Inserted Start of Year", "EndOfYear", each Date.EndOfYear([Date]), type date),
    #"Inserted Month" = Table.AddColumn(#"Inserted End of Year", "Month", each Date.Month([Date]), type number),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted Month", "Month Name", each Date.MonthName([Date]), type text),
    #"Inserted Quarter" = Table.AddColumn(#"Inserted Month Name", "Quarter", each Date.QuarterOfYear([Date]), type number),
    #"Inserted Start of Quarter" = Table.AddColumn(#"Inserted Quarter", "StartOfQuarter", each Date.StartOfQuarter([Date]), type date),
    #"Inserted Week of Year" = Table.AddColumn(#"Inserted Start of Quarter", "WeekOfYear", each Date.WeekOfYear([Date]), type number),
    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Week of Year", "WeekOfMonth", each Date.WeekOfMonth([Date]), type number),
    #"Inserted Day of Week" = Table.AddColumn(#"Inserted Week of Month", "DayOfWeek", each Date.DayOfWeek([Date]), type number),
    #"Added Custom" = Table.AddColumn(#"Inserted Day of Week", "Weekday Flag", each if [DayOfWeek]= 0 then 0 else if [DayOfWeek]=6 then 0 else 1),
    #"Added Custom1" = Table.AddColumn(#"Added Custom", "Weekend Flag", each if [DayOfWeek] =0 then 1 else if [DayOfWeek]=6 then 1 else 0),
    #"Added Custom2" = Table.AddColumn(#"Added Custom1", "Holiday Flag", each if [Weekday Flag]=1 and [DAY_OF_YEAR_NUMBER] =1 then 1
    else if [DAY_OF_YEAR_NUMBER]=2 and [DAY_OF_WEEK_NUMBER]=2 then 1
    else if [DAY_OF_YEAR_NUMBER] =3 and [DAY_OF_WEEK_NUMBER] =2 then 1
    else if [WEEK_IN_MONTH_NUMBER] = 3 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE] = 2 then 1
    else if [MONTH_VALUE]=7 and [DAY_OF_MONTH_NUMBER]=4 and [Weekday Flag]=1 then 1
    else if [MONTH_VALUE]=7 and ([DAY_OF_MONTH_NUMBER]=5 or [DAY_OF_MONTH_NUMBER]=6) and [DAY_OF_WEEK_NUMBER] =2 then 1
    else if [MONTH_VALUE]=5 and [WEEK_IN_MONTH_NUMBER]=5 and [DAY_OF_WEEK_NUMBER]=2 then 1 
    else if [DAY_OF_MONTH_NUMBER]>=25 and [MONTH_VALUE]=5 and [WEEK_IN_MONTH_NUMBER]=4 and [DAY_OF_WEEK_NUMBER]=2 then 1 else if [WEEK_IN_MONTH_NUMBER]=1 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE]=9 then 1 
    else if [WEEK_IN_MONTH_NUMBER]=2 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE]=9 and ([DAY_OF_MONTH_NUMBER]<=7) then 1
    else if [MONTH_VALUE]=11 and [WEEK_IN_MONTH_NUMBER]=4 and ([DAY_OF_WEEK_NUMBER]=5 or [DAY_OF_WEEK_NUMBER]=6) then 1 else if [MONTH_VALUE]=12 and ([DAY_OF_MONTH_NUMBER]=24 or [DAY_OF_MONTH_NUMBER]=25) and [Weekday Flag]=1 then 1 
    else if [MONTH_VALUE]= 12 and [DAY_OF_MONTH_NUMBER]=23 and [DAY_OF_WEEK_NUMBER]=6 then 1
    else if [MONTH_VALUE]= 12 and [DAY_OF_MONTH_NUMBER]=26 and [DAY_OF_WEEK_NUMBER]=3 then 1 else 0),
    #"Removed Columns" = Table.RemoveColumns(#"Added Custom2",{"Holiday Flag"}),
    #"Inserted Day of Year" = Table.AddColumn(#"Removed Columns", "DayOfYear", each Date.DayOfYear([Date]), type number),
    #"Renamed Columns1" = Table.RenameColumns(#"Inserted Day of Year",{{"DayOfYear", "DAY_OF_YEAR_NUMBER"}, {"WeekOfMonth", "WEEK_IN_MONTH_NUMBER"}, {"Month", "MONTH_VALUE"}, {"DayOfWeek", "DAY_OF_WEEK_NUMBER"}}),
    #"Inserted Start of Month" = Table.AddColumn(#"Renamed Columns1", "StartOfMonth", each Date.StartOfMonth([Date]), type date),
    #"Added Custom3" = Table.AddColumn(#"Inserted Start of Month", "DAY_OF_MONTH_NUMBER", each Duration.Days([Date]-[StartOfMonth])+1),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom3",{{"DAY_OF_MONTH_NUMBER", Int64.Type}}),
    #"Added Custom4" = Table.AddColumn(#"Changed Type", "Holiday Flag", each if [Weekday Flag]=1 and [DAY_OF_YEAR_NUMBER] =1 then 1
    else if [DAY_OF_YEAR_NUMBER]=2 and [DAY_OF_WEEK_NUMBER]=2 then 1
    else if [DAY_OF_YEAR_NUMBER] =3 and [DAY_OF_WEEK_NUMBER] =2 then 1
    else if [WEEK_IN_MONTH_NUMBER] = 3 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE] = 2 then 1
    else if [MONTH_VALUE]=7 and [DAY_OF_MONTH_NUMBER]=4 and [Weekday Flag]=1 then 1
    else if [MONTH_VALUE]=7 and ([DAY_OF_MONTH_NUMBER]=5 or [DAY_OF_MONTH_NUMBER]=6) and [DAY_OF_WEEK_NUMBER] =2 then 1
    else if [MONTH_VALUE]=5 and [WEEK_IN_MONTH_NUMBER]=5 and [DAY_OF_WEEK_NUMBER]=2 then 1 
    else if [DAY_OF_MONTH_NUMBER]>=25 and [MONTH_VALUE]=5 and [WEEK_IN_MONTH_NUMBER]=4 and [DAY_OF_WEEK_NUMBER]=2 then 1 else if [WEEK_IN_MONTH_NUMBER]=1 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE]=9 then 1 
    else if [WEEK_IN_MONTH_NUMBER]=2 and [DAY_OF_WEEK_NUMBER]=2 and [MONTH_VALUE]=9 and ([DAY_OF_MONTH_NUMBER]<=7) then 1
    else if [MONTH_VALUE]=11 and [WEEK_IN_MONTH_NUMBER]=4 and ([DAY_OF_WEEK_NUMBER]=5 or [DAY_OF_WEEK_NUMBER]=6) then 1 else if [MONTH_VALUE]=12 and ([DAY_OF_MONTH_NUMBER]=24 or [DAY_OF_MONTH_NUMBER]=25) and [Weekday Flag]=1 then 1 
    else if [MONTH_VALUE]= 12 and [DAY_OF_MONTH_NUMBER]=23 and [DAY_OF_WEEK_NUMBER]=6 then 1
    else if [MONTH_VALUE]= 12 and [DAY_OF_MONTH_NUMBER]=26 and [DAY_OF_WEEK_NUMBER]=3 then 1 else 0),
    #"Added Custom5" = Table.AddColumn(#"Added Custom4", "Business Day", each if [Weekday Flag]=1 and [Holiday Flag]=0 then 1 else 0),
    #"Renamed Columns2" = Table.RenameColumns(#"Added Custom5",{{"MONTH_VALUE", "Month Number"}, {"WEEK_IN_MONTH_NUMBER", "Week In Month Number"}, {"DAY_OF_WEEK_NUMBER", "Day of Week Number"}, {"DAY_OF_YEAR_NUMBER", "Day of Year Number"}, {"DAY_OF_MONTH_NUMBER", "Day Of Month Number"}, {"Quarter", "Quarter Value"}}),
    #"Added Custom6" = Table.AddColumn(#"Renamed Columns2", "Quarter", each if [Quarter Value] =1 then "Q1" else if [Quarter Value]=2 then "Q2" else if [Quarter Value]=3 then "Q3" else "Q4"),
    #"Added Custom7" = Table.AddColumn(#"Added Custom6", "Quarter Year", each [Quarter]&" "&Number.ToText([Year])),
    #"Added Custom8" = Table.AddColumn(#"Added Custom7", "Year Quarter Number", each Number.ToText([Year])&"."&Number.ToText([Quarter Value])),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom8",{{"Year Quarter Number", type number}, {"Date", type date}}),
    #"Duplicated Column" = Table.DuplicateColumn(#"Changed Type1", "Month Name", "Month Name - Copy"),
    #"Split Column by Position" = Table.SplitColumn(#"Duplicated Column","Month Name - Copy",Splitter.SplitTextByPositions({0, 3}, false),{"Month Name - Copy.1", "Month Name - Copy.2"}),
    #"Changed Type2" = Table.TransformColumnTypes(#"Split Column by Position",{{"Month Name - Copy.1", type text}, {"Month Name - Copy.2", type text}}),
    #"Removed Columns1" = Table.RemoveColumns(#"Changed Type2",{"Month Name - Copy.2"}),
    #"Renamed Columns3" = Table.RenameColumns(#"Removed Columns1",{{"Month Name - Copy.1", "Month"}})

    in

    #"Renamed Columns3"
