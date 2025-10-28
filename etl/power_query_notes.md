let
    Source = Folder.Files("C:\Users\Usuario\Desktop\PBA Mens Soccer 25\StatSports\Fall_25_Games"),
    #"Filtered Hidden Files1" = Table.SelectRows(Source, each [Attributes]?[Hidden]? <> true),
    #"Invoke Custom Function1" = Table.AddColumn(#"Filtered Hidden Files1", "Transform File", each #"Transform File"([Content])),
    #"Renamed Columns1" = Table.RenameColumns(#"Invoke Custom Function1", {"Name", "Source.Name"}),
    #"Removed Other Columns1" = Table.SelectColumns(#"Renamed Columns1", {"Source.Name", "Transform File"}),
    #"Expanded Table Column1" = Table.ExpandTableColumn(#"Removed Other Columns1", "Transform File", Table.ColumnNames(#"Transform File"(#"Sample File"))),
    #"Changed Type" = Table.TransformColumnTypes(#"Expanded Table Column1",{{"Source.Name", type text}, {"Player Name", type text}, {"Squad Name", type text}, {"Club Name", type text}, {"Session Date", type date}, {"Session Name", type text}, {"Drill Title", type text}, {"Session Type", type text}, {"Total Distance", type number}, {"High Speed Running", Int64.Type}, {"Distance per min", Int64.Type}, {"Impacts", Int64.Type}, {"Max Speed", type number}, {"High Intensity Distance", Int64.Type}, {"No of Sprints", Int64.Type}, {"Sprint Distance", Int64.Type}, {"Accelerations", Int64.Type}, {"Decelerations", Int64.Type}, {"Calories", Int64.Type}, {"HSR Per Min", Int64.Type}, {"HID Per Min", Int64.Type}, {"Sprint Distance Per Min", Int64.Type}, {"Step Balance (L)", Int64.Type}, {"Step Balance (R)", Int64.Type}, {"DSL", Int64.Type}, {"Max HR", Int64.Type}, {"Avg HR", Int64.Type}, {"Time In Red Zone", type time}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"Source.Name", "Squad Name", "Club Name", "Distance per min", "Calories", "HSR Per Min", "HID Per Min", "Sprint Distance Per Min", "Step Balance (L)", "Step Balance (R)", "Max HR", "Avg HR", "Time In Red Zone"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Columns",{{"High Speed Running", "HSR"}, {"High Intensity Distance", "HID"}, {"Session Name", "Opponent"}, {"Session Type", "Period"}, {"Drill Title", "Session Type"}, {"Session Date", "Date"}}),
    #"Removed Columns1" = Table.RemoveColumns(#"Renamed Columns",{"DSL"}),
    #"Split Column by Position" = Table.SplitColumn(#"Removed Columns1", "Opponent", Splitter.SplitTextByPositions({0, 2}, false), {"Opponent.1", "Opponent.2"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Position",{{"Opponent.1", type text}, {"Opponent.2", type text}}),
    #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Opponent.1"}),
    #"Renamed Columns2" = Table.RenameColumns(#"Removed Columns2",{{"Opponent.2", "Opponent"}})
in
    #"Renamed Columns2"
