# Overview of how to pass a parameterized sql query into m query

## Create a function out of your sql query like this

let fundseq = (cfundseq as text)=>

let
    Source = Oracle.Database("Name of Database", [Query="SELECT#(lf)    CFH.AS_OF_DATE#(lf)    ,CFH.ASSET_MGMT_FEE#(lf)    ,CFH.RP_CPSRPD_FUND_SEQ#(lf) FROM FUND_VIEW CFH#(lf)    WHERE CFH.AS_OF_DATE>=to_date('1/1/2010', 'MM/DD/YYYY')#(lf)    AND CFH.RP_CPSRPD_FUND_SEQ=TO_Number('"&cfundseq&"') ", CreateNavigationProperties=false])
in
    Source

in fundseq

## note above in the where clause is the function of a contract you would want to filter the query by

## Create the actual query and invoke the function:

let
    Source = Excel.CurrentWorkbook(){[Name="Table2"]}[Content],
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"Date Written Notice", type datetime}, {"Contract", Int64.Type}, {"CSEQ Number", type text}}),
    #"Removed Other Columns" = Table.SelectColumns(#"Changed Type",{"CSEQ Number"}),
    #"Invoked Custom Function" = Table.AddColumn(#"Removed Other Columns", "AMF_FUNCTION", each AMF_FUNCTION([CSEQ Number])),
    #"Expanded AMF_FUNCTION" = Table.ExpandTableColumn(#"Invoked Custom Function", "AMF_FUNCTION", {"AS_OF_DT", "ASSET_MGMT_FEE_DEDUCT_AMT", "RP_CPSRPD_FUND_SEQ"}, {"AS_OF_DT", "ASSET_MGMT_FEE_DEDUCT_AMT", "RP_CPSRPD_FUND_SEQ"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Expanded AMF_FUNCTION",{{"AS_OF_DT", type date}, {"ASSET_MGMT_FEE_DEDUCT_AMT", type number}, {"RP_CPSRPD_FUND_SEQ", Int64.Type}, {"CSEQ Number", Int64.Type}})
in
    #"Changed Type1"
    
    
  ### note: this can be funky depending on whether you are working with strings or numbers
  
  ## Another Example where two parameters are passed into a sql query via m
  
  ### create the function
  
  #### NOTE: DATES ARE CONVERTED TO CHARACTERS
  
  let

    oracle_dynamic = (contract as text, writtennotice as date) =>

let
    Source = Oracle.Database("name of your database", [Query="select  cf.CONTRACTNUMBEER kno,#(lf)         TO_CHAR(cfh.as_of_dt,'MM/DD/YYYY') aod,#(lf) from     FUND cfh,#(lf)         CFUND cf,#(lf)         PFUND pf,#(lf)         RFUND rf,#(lf)         PSERIES ps#(lf)where    cfh.rp_cpsrpd_fund_seq =  cf.rp_cpsrpd_fund_seq#(lf)and      cf.rp_psrpd_fund_seq = pf.rp_psrpd_fund_seq#(lf)and      pf.rp_rpd_fund_seq = rf.rp_rpd_fund_seq#(lf)and      pf.product_series_id = ps.product_series_id#(lf)and      TRUNC(cfh.as_of_dt) between to_date('1/1/2010', 'MM/DD/YYYY') and sysdate#(lf)and      cf.cntrct_nbr_id in ('"&contract&"')#(lf)--and     pf.rp_psrpd_fund_seq in ('CWY')#(lf)and      rf.short_nm in ('XSAFA','GSAFA')#(lf)and      rf.short_nm <> ('HOLDA')#(lf)--and      pf.invstmnt_fund_category_cd in ('GUAR')#(lf)--and      ps.short_name in ('AC3')#(lf)--and      cf.cntrct_fund_status_cd = 'A'#(lf)order by cfh.as_of_dt, kno, fund, hseq", CreateNavigationProperties=false]),
    #"Filtered Rows" = Table.SelectRows(Source, each ([KNO] = contract)),
    #"Changed Type" = Table.TransformColumnTypes(#"Filtered Rows",{{"AOD", type date}}),
    #"Filtered Rows1" = Table.SelectRows(#"Changed Type", each [AOD] <= #date(Date.Year(writtennotice), Date.Month(writtennotice), Date.Day(writtennotice)))
in
    #"Filtered Rows1"

in oracle_dynamic
  
  ### CREATE THE TABLE AND PASS THE FUNCTION INTO THE QUERY
  
  let
    Source = Excel.CurrentWorkbook(){[Name="Table2"]}[Content],
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"Date Written Notice", type date}, {"Contract", type text}}),
    #"Removed Columns1" = Table.RemoveColumns(#"Changed Type",{"CSEQ Number"}),
    #"Added Custom" = Table.AddColumn(#"Removed Columns1", "Custom", each Oracle_Extract_Function([Contract], [Date Written Notice])),
    #"Expanded Custom" = Table.ExpandTableColumn(#"Added Custom", "Custom", {"KNO", "FUND", "AOD", "DVAL", "DUQ", "ODIA", "CDIA", "TDIA", "XUV", "GUV", "TUQ", "TVAL", "PSEQ", "CSEQ", "HSEQ", "FRAT", "NCIE", "SCIE", "PROD", "INV"}, {"KNO", "FUND", "AOD", "DVAL", "DUQ", "ODIA", "CDIA", "TDIA", "XUV", "GUV", "TUQ", "TVAL", "PSEQ", "CSEQ", "HSEQ", "FRAT", "NCIE", "SCIE", "PROD", "INV"}),
    #"Removed Columns" = Table.RemoveColumns(#"Expanded Custom",{"Contract"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Removed Columns",{{"AOD", type date}, {"DVAL", type number}, {"DUQ", type number}, {"XUV", type number}, {"TUQ", type number}, {"TVAL", type number}, {"CSEQ", Int64.Type}, {"HSEQ", Int64.Type}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type1",{{"Date Written Notice", "Date Written Notice1"}}),
    #"Added Custom1" = Table.AddColumn(#"Renamed Columns", "Date Of Written Notice", each Date.EndOfMonth(Date.AddMonths([Date Written Notice1],-1))),
    #"Removed Columns2" = Table.RemoveColumns(#"Added Custom1",{"Date Written Notice1"}),
    #"Changed Type2" = Table.TransformColumnTypes(#"Removed Columns2",{{"Date Of Written Notice", type date}}),
    #"Added Custom2" = Table.AddColumn(#"Changed Type2", "Flag AOD Earlier Than DWN", each if[AOD]<=[Date Of Written Notice] then 1 else 0),
    #"Filtered Rows" = Table.SelectRows(#"Added Custom2", each ([Flag AOD Earlier Than DWN] = 1))
in
    #"Filtered Rows"
