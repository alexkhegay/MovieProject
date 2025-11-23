# Movie Dashboard
## Table of Content
[Problem Statement](#problem-statement)
[Data Source](#data-source)
[Tools](#tools)
[Dashboard](#dashboard)
[M Code](#m-code)
[Reccomendations](#recommendations)
### Problem Statement
Netflix wants to better understand which movie they should produce next, including the most suitable actors and directors. We have a dataset containing movie budgets, box office performance, actors, directors, and genres.
Your task is to build an Excel dashboard that provides insights into this dataset. The dashboard should help identify:
- The best-performing actors
- The top movies based on box office metrics
- Director performance
- Genre trends
- Seasonal patterns in movie performance
- Any additional insights that can guide future production decisions

The final dashboard should be clear, interactive, and visually compelling, enabling Netflix to make data-driven decisions.
### Data Source 
Movie Data : The primary dataset used for this analysis is the "Movie Data Homework.xlsx" file, containing detailed information about each movie's performance (box office and budget), actors, directors and genres. 
 You can download original datasource here: [Movie Dataset Excel file](https://github.com/user-attachments/files/23698113/Movies_Data_Homework.xlsx)

### Tools
1. Power Query - I used Power Query for Data Cleaning
2. Excel - I used Excel for Data Analysis
3. Pivot Tables - for Creating the dashboard and Visualizations
### Data Cleaning
- Data loading and inspection.
- Handling errors, missing values.
- Data cleaning and formatting. The excel file after the data cleaning & preparation process can be downloaded here - [Movie dashboard](https://github.com/user-attachments/files/23698133/Homework.Nov.10.25.xlsx)
### Dashboard
<img width="789" height="605" alt="image" src="https://github.com/user-attachments/assets/f7459823-112c-4d62-ac2a-2fce38ff1f9e" />

### M Code
```
let
    Source = Excel.Workbook(File.Contents("C:\Users\Виктория\OneDrive\Рабочий стол\Vikin Gospodin\HW\Homework Nov 10,25\Movies_Data_Homework.xlsx"), null, true),
    #"Movie Data_Sheet" = Source{[Item="Movie Data",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(#"Movie Data_Sheet", [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Movie Title", type text}, {"Release Date", type date}, {"Wikipedia URL", type text}, {"Genre_First_ID", Int64.Type}, {"Genre_Second_ID", Int64.Type}, {"Director_First_ID", Int64.Type}, {"Cast_First_ID", Int64.Type}, {"Cast_Second_ID", Int64.Type}, {"Cast_Third_ID", Int64.Type}, {"Cast_Fourth_ID", Int64.Type}, {"Cast_Fifth_ID", Int64.Type}, {"Budget ($)", Int64.Type}, {"Box Office Revenue ($)", type number}, {"Column14", type any}, {"Column15", type any}, {"Column16", type any}, {"Column17", type any}, {"Column18", type any}, {"Column19", type any}, {"Column20", type any}, {"Column21", type any}}),
    #"Merged Queries" = Table.NestedJoin(#"Changed Type", {"Genre_First_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
    #"Expanded Genres" = Table.ExpandTableColumn(#"Merged Queries", "Genres", {"Genre"}, {"Genres.Genre"}),
    #"Removed Columns" = Table.RemoveColumns(#"Expanded Genres",{"Column14", "Column15", "Column16", "Column17", "Column18", "Column19", "Column20", "Column21"}),
    #"Reordered Columns" = Table.ReorderColumns(#"Removed Columns",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres.Genre", "Genre_Second_ID", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries1" = Table.NestedJoin(#"Reordered Columns", {"Genre_Second_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
    #"Expanded Genres1" = Table.ExpandTableColumn(#"Merged Queries1", "Genres", {"Genre"}, {"Genres.Genre.1"}),
    #"Renamed Columns" = Table.RenameColumns(#"Expanded Genres1",{{"Genres.Genre.1", "Genre Second"}}),
    #"Reordered Columns1" = Table.ReorderColumns(#"Renamed Columns",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genres.Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Reordered Columns1",{{"Genres.Genre", "Genre"}}),
    #"Merged Queries2" = Table.NestedJoin(#"Renamed Columns1", {"Director_First_ID"}, Directors, {"ID"}, "Directors", JoinKind.LeftOuter),
    #"Expanded Directors" = Table.ExpandTableColumn(#"Merged Queries2", "Directors", {"Director"}, {"Directors.Director"}),
    #"Renamed Columns2" = Table.RenameColumns(#"Expanded Directors",{{"Directors.Director", "Director"}}),
    #"Reordered Columns2" = Table.ReorderColumns(#"Renamed Columns2",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries3" = Table.NestedJoin(#"Reordered Columns2", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors" = Table.ExpandTableColumn(#"Merged Queries3", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns3" = Table.RenameColumns(#"Expanded Actors",{{"Actors.Actor", "Actor"}}),
    #"Reordered Columns3" = Table.ReorderColumns(#"Renamed Columns3",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries4" = Table.NestedJoin(#"Reordered Columns3", {"Cast_Second_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors1" = Table.ExpandTableColumn(#"Merged Queries4", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns4" = Table.RenameColumns(#"Expanded Actors1",{{"Actors.Actor", "Actor 2"}}),
    #"Reordered Columns4" = Table.ReorderColumns(#"Renamed Columns4",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Actor 2", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries5" = Table.NestedJoin(#"Reordered Columns4", {"Cast_Third_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors2" = Table.ExpandTableColumn(#"Merged Queries5", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns5" = Table.RenameColumns(#"Expanded Actors2",{{"Actors.Actor", "Actor 3"}}),
    #"Reordered Columns5" = Table.ReorderColumns(#"Renamed Columns5",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Actor 2", "Cast_Third_ID", "Actor 3", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries6" = Table.NestedJoin(#"Reordered Columns5", {"Cast_Fourth_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors3" = Table.ExpandTableColumn(#"Merged Queries6", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns6" = Table.RenameColumns(#"Expanded Actors3",{{"Actors.Actor", "Actor 4"}}),
    #"Reordered Columns6" = Table.ReorderColumns(#"Renamed Columns6",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Actor 2", "Cast_Third_ID", "Actor 3", "Cast_Fourth_ID", "Actor 4", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
    #"Merged Queries7" = Table.NestedJoin(#"Reordered Columns6", {"Cast_Fifth_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
    #"Expanded Actors4" = Table.ExpandTableColumn(#"Merged Queries7", "Actors", {"Actor"}, {"Actors.Actor"}),
    #"Renamed Columns7" = Table.RenameColumns(#"Expanded Actors4",{{"Actors.Actor", "Actor 5"}}),
    #"Reordered Columns7" = Table.ReorderColumns(#"Renamed Columns7",{"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Genre Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Actor 2", "Cast_Third_ID", "Actor 3", "Cast_Fourth_ID", "Actor 4", "Cast_Fifth_ID", "Actor 5", "Budget ($)", "Box Office Revenue ($)"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Reordered Columns7",{{"Budget ($)", Currency.Type}, {"Box Office Revenue ($)", Currency.Type}, {"Release Date", type date}}),
    #"Added Custom" = Table.AddColumn(#"Changed Type1", "ROI", each ([#"Box Office Revenue ($)"]-[#"Budget ($)"])/[#"Budget ($)"]),
    #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"ROI", Percentage.Type}}),
    #"Added Custom1" = Table.AddColumn(#"Changed Type2", "ROI.1", each ([#"Box Office Revenue ($)"]-[#"Budget ($)"])/[#"Budget ($)"]),
    #"Removed Columns1" = Table.RemoveColumns(#"Added Custom1",{"ROI"}),
    #"Renamed Columns8" = Table.RenameColumns(#"Removed Columns1",{{"ROI.1", "ROI"}})
in
    #"Renamed Columns8"
```
### Recommendations
Top 5 genres are Action, Comedy etc. I would recommend to Netflix to produce one of these genres as they brought in more in box office revenue based on the data from 2012 to 2016

<img width="486" height="164" alt="image" src="https://github.com/user-attachments/assets/5a1fdc8c-d1fb-4e17-9467-ee7cf9f28e1c" />

Horror is the most succesful genre in terms of ROI

<img width="598" height="161" alt="image" src="https://github.com/user-attachments/assets/02884a83-aa8f-4476-85eb-873bd55ad029" />
