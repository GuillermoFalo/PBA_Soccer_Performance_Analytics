# Power Query — Team vs Player (Training Only)
**Version:** current M-code (no modifications)

## Objective
Combine all STATSports practice CSVs into a clean **player × session** fact table
with numbered sessions and standardized metrics for Tableau visualization.

## Steps
1. Load all CSVs from folder using `Folder.Files` and ignore hidden files.  
2. Apply your existing `"Transform File"` function to convert each file to a table.  
3. Apply data types and remove non-essential columns.  
4. Rename fields (HSR, HID, etc.) and derive `Session_No` from `Session Name`.  
5. Export final table to Tableau.

## Output columns
- Keys / context: `Player_Name`, `Session_No`, `Session_Name`, `Session_Date`, `Session_Type`  
- Metrics: `Total_Distance`, `HID`, `HSR`, `Accelerations`, `Decelerations`, `No of Sprints`, `Sprint_Distance`, `Max_Speed`, `Impacts`

> **Note:** Team-average values per session are calculated in Tableau, not in Power Query.

## M-code (as implemented)
```powerquery
let
    Source = Folder.Files("C:\Users\Usuario\Desktop\PBA Mens Soccer 25\StatSports\Fall_25_Sess"),
    #"Filtered Hidden Files1" = Table.SelectRows(Source, each [Attributes]?[Hidden]? <> true),
    #"Invoke Custom Function1" = Table.AddColumn(#"Filtered Hidden Files1", "Transform File", each #"Transform File"([Content])),
    #"Renamed Columns1" = Table.RenameColumns(#"Invoke Custom Function1", {"Name", "Source.Name"}),
    #"Removed Other Columns1" = Table.SelectColumns(#"Renamed Columns1", {"Source.Name", "Transform File"}),
    #"Expanded Table Column1" = Table.ExpandTableColumn(#"Removed Other Columns1", "Transform File", Table.ColumnNames(#"Transform File"(#"Sample File"))),
    #"Changed Type" = Table.TransformColumnTypes(#"Expanded Table Column1",{{"Source.Name", type text}, {"Player Name", type text}, {"Squad Name", type text}, {"Club Name", type text}, {"Session Date", type date}, {"Session Name", type text}, {"Session Type", type text}, {"Total Distance", type number}, {"High Speed Running", Int64.Type}, {"Distance per min", Int64.Type}, {"Impacts", Int64.Type}, {"Max Speed", type number}, {"High Intensity Distance", Int64.Type}, {"No of Sprints", Int64.Type}, {"Sprint Distance", Int64.Type}, {"Accelerations", Int64.Type}, {"Decelerations", Int64.Type}, {"Calories", Int64.Type}, {"HSR Per Min", Int64.Type}, {"HID Per Min", Int64.Type}, {"Sprint Distance Per Min", Int64.Type}, {"Step Balance (L)", Int64.Type}, {"Step Balance (R)", Int64.Type}, {"DSL", Int64.Type}, {"Max HR", Int64.Type}, {"Avg HR", Int64.Type}, {"Time In Red Zone", type time}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"Squad Name", "Club Name", "Max HR", "Avg HR", "Distance per min", "Calories", "HSR Per Min", "HID Per Min", "Sprint Distance Per Min", "Step Balance (L)", "Step Balance (R)", "Time In Red Zone", "DSL"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Columns",{{"High Speed Running", "HSR"}, {"High Intensity Distance", "HID"}}),
    #"Reordered Columns" = Table.ReorderColumns(#"Renamed Columns",{"Source.Name", "Player Name", "Session Date", "Session Name", "Session Type", "Total Distance", "HID", "HSR", "Accelerations", "Decelerations", "No of Sprints", "Sprint Distance", "Max Speed", "Impacts"}),
    #"Removed Columns1" = Table.RemoveColumns(#"Reordered Columns",{"Source.Name"}),
    #"Split Column by Delimiter" = Table.SplitColumn(#"Removed Columns1", "Session Name", Splitter.SplitTextByEachDelimiter({"_"}, QuoteStyle.Csv, false), {"Session Name.1", "Session Name.2"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Session Name.1", Int64.Type}, {"Session Name.2", type text}}),
    #"Renamed Columns2" = Table.RenameColumns(#"Changed Type1",{{"Session Name.1", "Session_No"}, {"Session Type", "Session_Type"}, {"Total Distance", "Total_Distance"}, {"Session Date", "Session_Date"}, {"Player Name", "Player_Name"}, {"Sprint Distance", "Sprint_Distance"}, {"Max Speed", "Max_Speed"}})
in
    #"Renamed Columns2"
