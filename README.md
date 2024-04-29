# Project_PowerBI_HR_Analysis_AtliqSystems
This is a Power_BI Dashboard showing the metrics for Human Resources (HR) Presence Analysis based on Data provided by Atliq Systems

Dates in attendance sheets of employees were in first row. Different sheet for each month was created. One workbook for one quarter. 4 workbooks for a year. The data is to be transformed in powerbi such that the all the dates are in one column, one below another in proper sequence such that the data is visually understandable for user.

CREATING A TEMPLATE
We have Dates in multiple columns so we can’t work with that therefore bringing the dates in one column is always the way to go.
Used Power Query editor. Created a duplicate of available quarterly workbook to create a “Template” Sheet. This template was used to transform data by arranging the dates in sequence. 
Removed unnecessary sheets in Template. Removed all columns except for “Table” Column
Set the first row as column header. Renamed column 1 as “Employee Code” and column 2 as “Name”.
Selected columns “Employee Code” and “Name”  selected “Transform” Tab  dropdown “Unpivot Columns”  select “Unpivot Other Columns”
By the last step, the first row with all the dates will be transformed into a single column with all the dates and the other columns will adjust accordingly. 
Changed type of “Date” column to Date and last column i.e. “Value” to Text. From dropdown in “Date” column select “Remove Errors” to remove any text values in date column.

CREATING A PARAMETER
Parameter is a dynamic way of filtering the data. 
Home Tab Dropdown “Manage Parameter”  New parameter “Worksheet”.
Select “Filtered Rows” from “Applied Steps”  select gear icon in front of it  Filter rows by Parameter(Worksheet) instead of “Apr 2022”  OK.

CREATING A FUNCTION
We have done multiple steps of transformation on the data and doing this over and over again multiple times on multiple sheets is time consuming. Therefore, the FUNCTION is a fixed steps of procedure that we have already done on one sheet to be used on multiple sheets with a few clicks i.e. its similar to a Macro in excel or a reusable code. Instead of doing same steps over and over on multiple data sets that are similar we can use a function to implements within a few steps.
Right click on “Template”  Create Function  name as “GetData” 
Now add this function as a Column  Add Column Tab  select “Invoke Custom Function”  columns name = GetData  custom function = GetData
You will see column named GetData in the “Attendance Sheet”  Expand the column using the icon beside column name
Rename columns and change type to text except for date column

LOADING DATA TO POWER BI
Rename sheet “Attendance Sheet 2022” to “Final Data”
Disable “Enable Load” option for “Template”. Because loading it in powerbi is not necessary. Only the data required for creating visualization is to be taken into power bi.
Finally Close and Load data.

KEY TAKEAWAYS 
Google what you need
Transform data in a dynamic way so it works with new data as well
Digest the concepts in small quantities



CREATING VISUALIZATIONS IN POWER BI
Creating Metrics i.e. Measures using DAX Functions
Click on ENTER DATA under Home Tab  Create Table named “Measure Table”

Total Working Days
Right click on “Measure Table”  New Measure; this new measure is for Total Working Days
In the measure exclude Weekly off and Holiday Off
•	Create a variable for Total working days
•	Create a variable for Non Work Days
•	Return (Total working days - Non Work Days)

Total Working Days = 
      VAR totaldays = COUNT('Final Data'[Value])
      VAR nonworkdays = CALCULATE(COUNT('Final Data'[Value]),'Final Data'[Value] in {"WO", "HO"})
      RETURN
      totaldays-nonworkdays

Drag the measure to the visual screen  it will automatically create a Card displaying a KPI of TOTAL WORKING DAYS 

Present Days
Problem statement is such that
•	“P” is present for full day
•	“WFH” is present for full day
•	“HWFH” is present for Half day
•	Everything else is absent
Create a new column in table to assign value of 1 to WFH and 0.5 to HWFH.
      WFH Count = SWITCH(TRUE(),
      'Final Data'[Value] = "WFH", 1,
      'Final Data'[Value] = "HWFH", 0.5,
      0)
Create a new measure called “WFH Count”
	    WFH Count = SUM('Final Data'[WFH Count])
Use this measure in the measure “present Days” created for the KPI
      Present Days = 
      VAR PresentDays = CALCULATE(COUNT('Final Data'[Value]),'Final Data'[Value]="P")
      RETURN PresentDays + [WFH Count]

Presence %
      Presence % = DIVIDE([Present Days], 'Measure Table'[Total Working Days],0)

WFH %
	    WFH % = DIVIDE([WFH Count],[Present Days],0)

WFH Count
	    WFH Count = SUM('Final Data'[WFH Count])

SL Count (Sick Leave)
      SL Count = SWITCH(TRUE(),
      'Final Data'[Value] = "SL", 1,
      'Final Data'[Value] = "HSL", 0.5,
      0)

SL %
      SL % = DIVIDE([SL Count],[Total Working Days],0)

Slicer for Date
Create a Date(Monthwise) column in table named “Month”.
New Column under table view  enter measure for column “ Month = STARTOFMONTH('Final Data'[Date]) ”  make format of date as “mmm yy”  output is “Apr 22”
You can also use the format option in column tools to change the date format as you like.

KEY TAKEAWAYS
Group you measures under one place like here “measure” table was created in the beginning.
Understanding calculate function in DAX is very important.
You can add additional column to your data using dax.
Sick leave KPI can be looked at 2 ways: out of all the leaves what % of leaves are sick leaves; what percent of employees are taking sick leave out of total working days.



CREATING DASHBOARD
Most important info in the dashboard like KPIs should be kept on left upper side because in india we read from left to right.
Through the dashboard we can see which employees were present 0% or 100% and what their metrics are w.r.t WFH (Work from Home) or SL (Sick Leave)

Which day of the week are most people taking day off/WFH? For this created a column in the table view using following DAX, converting the date value format to “ddd” i.e. “Mon”, “Tue” …
	Day of Week = FORMAT('Final Data'[Date],"ddd")

KEY TAKEAWAYS
Good insights report must enable the stakeholder to ask more why Qs.

Copy paste visuals to speed up dashboarding.


AUTOMATION FOR UPDATING DATA
Create a Folder on Sharepoint and “Get Data” from there to create a dashboard instead of an offline Excel Workbook. That way data is updated into the dashboard every hour or so and appropriate changes are done in it automatically.
One can also connect to google sheets. Whenever the data is updated there, the dashboard will be updated as well.
