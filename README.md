# Tableau

Project description:
Prepare some insightful data visualizations using Tableau around sales data. This will include a descriptive break down by geography, product category, customer demographics, and time. I will also include an interactive dashboard that drills down on key sales parameters as well as produce a sales forecast and then put the sales managers into the a top 3 group ranking using clustering of sales amounts. 

Source data:
The Maven Bike Shop sales data from the Microsoft PL-300 exam study guide. This is the practice data to use for someone that is studying for the Microsoft PowerBI exam. This data contains the 7 files shown below. These files have been modified from their original content slightly to make this project simpler and the sales data was duplicated many times in these files.  

<img width="203" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/6b58c355-56a4-4914-8e52-c198a359e58d">

The 2019 and 2020 files are the sales transactions files (detail tables) and the rest of these files are master files (fact tables). I used Tableau Prep to put together a data flow in order to connect all of these files and then output a single denormalized table to use as input for Tableau. 

----------------------------------------------------------------------------------------

### Go back to my profile page
[garth-c profile page] (https://github.com/garth-c)

-------------------------------------------------------------------------------------------

# road map for this project
- data import
- data quality check
- create the various dashboards
- link to my Tableau Public page
  
----------------------------------------------------------------------------------------

# data import

Using Tableau Prep, the sales transaction files from Maven Cycles for 2019 and 2020 are unioned together to form a new combined transaction table. Then the rest of the tables are joined (inner join) to the new combined transaction table to form a super set to use for this project. 

![prep_data_flow](https://github.com/garth-c/tableau/assets/138831938/bd086ca2-40f3-4db1-aa75-6de2023abdf7)


The data in Tableau Prep was put into a data flow using a star schema approach. This approach puts a transaction or fact table at the center of the schema and then the other fact tables or master file tables are connected to it through joins. This makes the denormalization process very simple to implement. All of the joins in this project were inner joins and there was no need to set up a snow flake schema as all of the dimension tables would join directly to the fact table. Below is a graphic depiction of a typical star schema:


![image](https://github.com/garth-c/tableau/assets/138831938/e1e2614e-08d7-4e52-b558-307766e77294)


Note that all of the joins in the Tableau Prep data flow were inner joins but other join types are certainly possible such as: left joins, right joins, full joins, self joins, natural joins, etc. Below is a graphic for an inner join.

![image](https://github.com/garth-c/tableau/assets/138831938/030536f8-65f4-488b-8906-edd3b5e76fa8)

Below is a graphic for a snow flake schema. Since there were no subdimension tables for this project, implementing the snow flake schema was not needed.

![image](https://github.com/garth-c/tableau/assets/138831938/125ffdbb-998e-4cd5-aedc-c22be6b1cd7c)


Lastly, Tableau has the option to process initial sql statments and initial query banding. These are sql statements that run before Tableau starts to process the data and they are able to function as data filters, set parameters, and perform many utility type functions betwen Tableau and the data source (typically a data base).

---------------------------------------------------------------------------------------------------------

# data quality check

The first step with this project is to validate that the sales total and the transaction count that was fed into Tableau match that of the .csv file inputs which they do. The first utility tab in the Tableau workbook sums these amounts shown below. 

<img width="152" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/8317d215-0133-445b-8a9b-8600f2c94962">

---------------------------------------------------------------------------------------------------------------------------

# create the various dashboards

The next step is to create individual workbooks in Tableau desktop that illustrate the data visualization needed for the specific view. Then after each workbook has been created, put them into a dashboard in order to compare and contrast visualizations with the end user audience in mind. 

For this specific project, since it was all sales based, I focused on breaking down the sales amounts by fairly standard explanatory variables. The idea would be that management would use this breakdown to better understand:
- where the sales are coming from geographically
  + top viz shows sales by country using a map with the dot size proportional to sales levels.
  + the bottom viz shows sales by country with bars and the dotted line is the average with a shaded part as the 95% confidence interval of the average
    + using the confidence intervals, sales by country that are significantly different are identified
  <img width="492" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/c162e0f0-74cd-4636-8fd6-028a7714c26a">

- what are the largest item groups in these sales
  + the top viz shows a tree plot with accessories category as the largest category by far of sales
  + the bottom viz shows a tree plot with the sub-category sales fairly evenly split among the large categories
<img width="492" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/a3046c47-3f57-4087-802a-3c6870be7eb6">

- what are the demographics of the customers
  + the top viz shows sales by gender
  + the bottom viz shows sales by age group
<img width="491" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/f4dad592-05f0-4213-b6d3-f30eb47ab972">


- what are the likely sales over the next year
- what has been the directional trend of sales over time
- what are the top 3 groups of sales manager by revenue
  + the top viz shows sales over time along with a light blue line for the forecast and the confidence interval for the forecast
  + the top viz shows a dark blue line that is the sales trend over time
  + the bottom viz shows the sales totals clustered into 3 groups based on amounts  
<img width="491" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/00e717d8-ad8a-4f20-bf16-8ce57e1100be">

- what day of the week are the most sales being made
- what month of the year are the most sales being made
  + the top viz shows the month of the year by sales and the dotted line is the average with a shaded part as the 95% confidence interval of the average
    + the confidence intervals provide context around the significance of these differences 
  + the bottom viz shows the day of the week by sales and the dotted line is the average with a shaded part as the 95% confidence interval of the average
    +  the same is true for the confidence intervals on this viz
<img width="491" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/ab34436f-4fe7-4149-b3ae-48d1e0e9bb8f">

Then I produce an interactive dashboard to drill down into key sales paramters to look for trends. 
  + management would be able to drill into sales totals by manager, country, and product to evaluate sales trends by age group, gender, and sub-category
<img width="493" alt="image" src="https://github.com/garth-c/tableau/assets/138831938/c754d557-12c8-4e0b-98c9-ade5eda1e237">

In order to produce the above data viz demonstrations, multiple formulas were needed in Tableau as well as a few data type changes. A few examples of Tableau formulas are below.

This Tableau formula converts the region number to a region name. The region number originally was imported as a numeric data type and I changed it to character data type in order to use the CASE WHEN function shown below.
```
//function for region names
CASE [Region]
    WHEN '1' then 'US Northwest'
    WHEN '2' then 'US Northeast'
    WHEN '3' then 'US Central'
    WHEN '4' then 'US Southwest'
    WHEN '5' then 'US Southeast'
    WHEN '6' then 'Canada'
    WHEN '7' then 'France'
    WHEN '8' then 'Germany'
    WHEN '9' then 'Australia'
    WHEN '10' then 'UK'
END
```

For the data viz around sales on a specific calendar day, a Tableau formula to extract the calendar day of the week was needed. This formula uses the DATENAME function to extract the calendar day of the week from a date typed field.

```
//extract the day of the week for sales
DATENAME ("weekday", [Sale Date])
```
The same idea was applied to the sales by calendar month data viz. This however is a two part process if you want to use the actual calendar month name in a data viz. First I extracted the month number from a date typed field using DATEPART. Then I nested this formula with a conversion function to change it to a character date type using the STR function. From there I used a CASE WHEN function to associate the month number to a calendar month name.

```
//sales month extraction
STR(DATEPART('month',[Sale Date]))
```

This is the CASE WHEN function to assign calendar month names to calendar month numbers.

```
month names
//case for sales month names
CASE [Sales_Month]
    WHEN '1' THEN 'Jan'
    WHEN '1' THEN 'Jan'
    WHEN '2' THEN 'Feb'
    WHEN '3' THEN 'Mar'
    WHEN '4' THEN 'Apr'
    WHEN '5' THEN 'May'
    WHEN '6' THEN 'Jun'
    WHEN '7' THEN 'Jul'
    WHEN '8' THEN 'Aug'
    WHEN '9' THEN 'Sep'
    WHEN '10' THEN 'Oct'
    WHEN '11' THEN 'Nov'
    WHEN '12' THEN 'Dec'
END
```

There were a few other formulas and data type conversions in this project that I didn't document here. However, these examples provide a representative flavor of formulas, calculated fields, and data transformations that are typically done using Tableau. 

---------------------------------------------------------------------------------------

# link to my Tableau Public page
All of these dashboards are available to explore real time on my Tableau Public page. The interactivity within these dashboards is fully functional when filters are used. The link to this site is shown below.

OPEN IN A SEPARATE TAB

[Tableau Public page] (https://public.tableau.com/app/profile/garth.cumpston)


Thanks for reading this
------------------------------------------------------------------------------------

### Go back to my profile page
[garth-c profile page] (https://github.com/garth-c)

--------------------------------------------------------------------------------------

-----------------------------------------------------------------------------------------
