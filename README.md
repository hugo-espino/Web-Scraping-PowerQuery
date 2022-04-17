# Web-Scraping-PowerQuery
In this example, I extracted data from www.statmuse.com in order to obtain all the statistics of an NFL QB player. The user can extract data of any NFL QB player available on the website, and I selected Ben Roethlisberger.

I used the beta Power BI Google Sheet Connector, you can find the workbook 
[Here](https://docs.google.com/spreadsheets/d/1Mtc1WRy_iWseqzTeJYzx1ja_1Vlr1OJ2mz0AgxBOTsA/edit#gid=2086890584). 
![GoogleSheet](https://drive.google.com/uc?export=view&id=1K7Y4GMFW6g8ZrYNA4fWhPXHmYXtpFa12)

In this table the final user is capable to add the QB that have been selected (or Qbs), it is important to find the name and id player that the website has in the database in order to add as a parameter in the Power Query function. The column "B" receives the year you want to extract the data. You need to add a new row for each year that you need to analyze.  

## Power Query
I created a function in Power Query that receives two parameters, player ID and year. The behavior is described below.
1. Power Query receives two parameters and creates a temporal table. 
2. In order to build the master calendar automatically, I selected the maximum and minimum date that the function received
3. Once we have the maximum and minimum year, Power Query creates the master calendar ( I attached the file in the repository section).
4. To build a dashboard in Power BI, I created a second table containing the description of the NFL teams to use as a dimension.  

## Power BI Dashboard

I built a simple report. I tried to cover the most of Power BI capabilities as:
1. Bookmarks to show and hide filters.
2. Custom tooltips in cards
3. Dynamic slicers. The final user can select which measure they want to visualize in the graph. 
4. Drill Through to analyze the performance of the QB selected versus the NFL team.

All the images and teams logos are 100% dynamic, and only colors will not change if the final user selects another QB. 

## Dax Parent-child hierarchies pattern
The matrix changes the minimum and maximum Rate background color based on the hierarchy level selected by the user.


```Highlighting Min and Max Rate = 
VAR __Team = CALCULATETABLE(
 							ADDCOLUMNS(
 								SUMMARIZE(Teams, 
                                          Teams[Conference],
                                          Teams[Division],
                                          Teams[Team]
                                          ),
 										  "@Value",_Measures[%Rate]
 									  ),
 								ALLSELECTED()
 									)
VAR __Division =CALCULATETABLE(
 							ADDCOLUMNS(
 								SUMMARIZE(Teams, 
                                          Teams[Conference],
                                          Teams[Division]
                                          ),
 										  "@Value",_Measures[%Rate]
 									  ),
 								ALLSELECTED()
 									)
VAR __Conference = CALCULATETABLE(
 							ADDCOLUMNS(
 								SUMMARIZE(Teams, 
                                          Teams[Conference]
                                          ),
 										  "@Value",_Measures[%Rate]
 									  ),
 								ALLSELECTED()
 									)
VAR __MinVal = SWITCH(
                           [Team Level],
                           "Team", MINX(__Team,[@Value]),
                           "Division", MINX(__Division,[@Value]),
                           "Conference", MINX(__Conference,[@Value])
                            )
VAR __MaxVal = SWITCH(
                           [Team Level],
                           "Team", MAXX(__Team,[@Value]),
                           "Division", MAXX(__Division,[@Value]),
                           "Conference", MAXX(__Conference,[@Value])
                            )
VAR __CurrentValue = _Measures[%Rate]
VAR __Result = 
 			SWITCH(
 			       TRUE(),
 			       __CurrentValue = __MinVal,"#EB0F00",
 			       __CurrentValue = __MaxVal,"#50D130"
 			       )
RETURN 
__Result
```









