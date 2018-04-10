# Set up a functions to dynamically call a directory

## use case

Important if directory location changes. you only have to update the variable that gets passed in

### create a function

(Directory, FileName) =>

    let
        Source = Excel.Workbook(File.Contents(Directory & FileName), null, true),
        Table1_Table = Source{[Item="Table1",Kind="Table"]}[Data],
        #"Changed Type" = Table.TransformColumnTypes(Table1_Table,{{"Name", type text}, {"State", type text}}),
        #"Split Column by Delimiter" = Table.SplitColumn(#"Changed Type", "Name", Splitter.SplitTextByEachDelimiter({" "}, QuoteStyle.Csv, false), {"Name.1", "Name.2"}),
        #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Name.1", type text}, {"Name.2", type text}}),
        #"Renamed Columns" = Table.RenameColumns(#"Changed Type1",{{"Name.1", "FirstName"}, {"Name.2", "LastName"}})
    in
        #"Renamed Columns"
    
    
   let
    Source = Folder.Files("C:\Advanced Power BI\Module Resources\Module 02"),
    #"Filtered Rows" = Table.SelectRows(Source, each ([Name] <> "Body Mass Index.xlsx")),
    #"Removed Columns" = Table.RemoveColumns(#"Filtered Rows",{"Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Content"}),
    #"Reordered Columns" = Table.ReorderColumns(#"Removed Columns",{"Folder Path", "Name"}),
    #"Invoked Custom Function" = Table.AddColumn(#"Reordered Columns", "Table1", each Table1([Folder Path], [Name])),
    #"Expanded Table1" = Table.ExpandTableColumn(#"Invoked Custom Function", "Table1", {"FirstName", "LastName", "State"}, {"FirstName", "LastName", "State"}),
    #"Removed Columns1" = Table.RemoveColumns(#"Expanded Table1",{"Folder Path", "Name"})
in
    #"Removed Columns1"
