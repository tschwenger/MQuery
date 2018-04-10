M Query Basics


## This is a good function reference

using the #shared returns all m query functions for manipulation

example m query

    let
    Source = #shared,
    #"Converted to Table" = Record.ToTable(Source)
in
       #"Converted to Table"


* filter the #"Converted to Table" to find the function you need to solve a problem. 


## Creating a list

This is simple

syntax: {,}

This will create a basic list

example:

    = {1,2,3}


## creating records

syntax: []


example:

    = [x=1,w=3,t=5]

