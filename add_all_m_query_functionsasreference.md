# This is a sample m script that returns all fucntions as a query into your data model. it's a good reference to have whilee working

    let
      Source = #shared,
      #"Converted to Table" = Record.ToTable(Source)
    in
      #"Converted to Table"
