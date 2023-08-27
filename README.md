# Maven_Market_PowerBi_Project

# PART 1: Connecting & Shaping the Data

 Connect all the csv file like customers, Products, Stores, calender, Return etc.
Added some new columns,Create a conditional column, Add a calculated column,  Replace "null" values, etc.


# PART 2: Creating the Data Model

In the MODEL view, arrange  tables with the lookup tables above the data tables

Connect Transaction_Data to Customers, Products, and Stores using valid primary/foreign keys 

Connect Stores to Regions as a "snowflake" schema

All relationships follow one-to-many cardinality, with primary keys (1) on the lookup side and foreign keys (*) on the data side

Filters are all one-way (no two-way filters)

Filter context flows "downstream" from lookup tables to data tables

In the DATA view, complete the following:

Update all date fields (across all tables) to the "M/d/yyyy" format using the formatting tools in the Modeling tab

Update "product_retail_price", "product_cost", and "discount_price" to Currency ($ English) format

In the Customers table, categorize "customer_city" as City, "customer_postal_code" as Postal Code, and "customer_country" as Country/Region

In the Stores table, categorize "store_city" as City, "store_state" as State or Province, "store_country" as Country/Region, and "full_address" as Address 


# PART 3: Adding DAX Measures

1) In the DATA view, add the following calculated columns:

1.	In the Calendar table, add a column named "Weekend"
Equals "Y" for Saturdays or Sundays (otherwise "N")
Weekend = IF ('Calendar'[Day Name] = "Saturday" || 'Calendar'[Day Name] = "Sunday", "Y", "N")


2.	In the Customers table, add a column named "Current Age"
Calculates current customer ages using the "birthdate" column and the TODAY () function
Current_Age = DATEDIFF(Customers[birthdate],TODAY(),YEAR)

3.	In the Customers table, add a column named "Priority"
Equals "High" for customers who own homes and have Golden membership cards (otherwise "Standard")
Priority = IF(Customers[member_card] = "Golden" && Customers[homeowner] = "Y","High","Standard")


4.	In the Customers table, add a column named "House Number"
Extracts all characters/numbers before the first space in the "customer_address" column 
House_Number = LEFT(Customers[customer_address], SEARCH(" ", Customers[customer_address])-1)

5.	In the Products table, add a column named "Price_Tier"
Equals "High" if the retail price is >$3, "Mid" if the retail price is >$1, and "Low" otherwise
Price_Tier = IF(Products[product_retail_price] >3, "High", IF(Products [product_retail_price] >1, "Mid","Low"))

6.	In the Stores table, add a column named "Years_Since_Remodel"
Calculates the number of years between the current date (TODAY()) and the last remodel date
Years_Since_Remodel = DATEDIFF(Stores[last_remodel_date],Today(),YEAR)






B) In the REPORT view

Add the following measures 
1.	Create new measures named "Quantity Sold" and "Quantity Returned" to calculate the sum of quantity from each data table
Quantity Sold = Sum (Transaction_Data[quantity])
Quantity Returned = SUM(Return_Data[quantity])

2.	Create new measures named "Total Transactions" and "Total Returns" to calculate the count of rows from each data table
Total Transactions = COUNT(Transaction_Data[quantity])
Total Returns = COUNT(Return_Data[quantity])

3.	Create a new measure named "Return Rate" to calculate the ratio of quantity returned to quantity sold (format as %)
Spot check: You should see an overall return rate of 0.99%
Return Rate = [Quantity Returned] / [Quantity Sold]

4.	Create new measures named "All Transactions" and "All Returns" to calculate grand total transactions and returns (regardless of filter context)
Spot check: You should see 269,720 transactions and 7,087 returns across all rows (test with product_brand on rows)
All Transactions = CALCULATE([Total Transactions], ALL(Transaction_Data))
All Returns = CALCULATE([Total Returns],ALL(Return_Data))   

5.	Create a new measure to calculate "Total Revenue" based on transaction quantity and product retail price, and format as $ (hint: you'll need an iterator)
Spot check: You should see a total revenue of $1,764,546
Total Revenue = SUMX(Transaction_Data,Transaction_Data[quantity] * RELATED(Products[product_retail_price]))


6.	Create a new measure named "60-Day Revenue" to calculate a running revenue total over a 60-day period, and format as $
Spot check: Create a matrix with "date" on rows; you should see $97,570 in 60-Day Revenue on 4/14/1997
60-Day Revenue = CALCULATE([Total Revenue], DATESINPERIOD('Calendar'[date], MAX('Calendar'[date]),-60,DAY))

7.	Create new measures named  "Last Month Transactions", "Last Month Revenue", "Last Month Profit", and "Last Month Returns"
Spot check: Create a matrix with "Start of Month" on rows to confirm accuracy
Last Month Profit = CALCULATE([Total Profit], DATEADD('Calendar'[date], -1,MONTH))
Last Month Transaction = CALCULATE([Total Transactions], DATEADD('Calendar'[date],-1,MONTH))
Last Month Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1,MONTH))
Last Month Return = CALCULATE([Total Returns], DATEADD('Calendar'[date], -1,MONTH))





















