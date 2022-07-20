# SQL-Sample-Queries

This is a log of the SQL i use!

AVERAGE TEMPERATURE

SELECT AVG(temperature) 
FROM skillful-coast-340323.demos.weather_nyc 
WHERE date BETWEEN '2020-06-01' AND '2020-06-30' 


ALIAS

Basic format for an AS query: 
SELECT column_name(s) 
FROM table_name AS alias_name; 

  Notice that AS is preceded by the table name and followed by the new nickname. It is a similar approach to aliasing a column:

SELECT column_name AS alias_name 
FROM table_name; 


If using AS results in an error when running a query because the SQL database you are working with doesn't support it, you can leave it out. In the previous examples, the alternate syntax for aliasing a table or column would be: 

FROM table_name alias_name 

SELECT column_name alias_name 

Arithmetic

SQL can perform arithmetic for you. Just select what you want to +, -, *, / . Don’t use spaces 
SELECT 
station_name,
ridership_2013,
ridership_2014,
ridership_2014-ridership_2013 AS change_2014_raw 
FROM bigquery-public-data.new_york_subway.subway_ridership_2013_present 


SELECT 
station_name,
ridership_2013,
ridership_2014,
ridership_2015,
ridership_2016,
(ridership_2013+ridership_2014+ridership_2015+ridership_2016) / 4 AS average 
FROM bigquery-public-data.new_york_subway.subway_ridership_2013_present 



You can also calculate percentages within your data: 
SELECT 
  Region 
Small_bags 
  Total_bags 
(small_bags / total_bags)*100 AS small_bags_percent 
FROM avocado_data.avocado_prices 
WHERE total_bags <> 0 



You can also use != 0 in place of <>0 or use the SAFE_DIVIDE function 

BETWEEN

SELECT   Date, purchase_price FROM customer_data.purchases WHERE   Date BETWEEN ‘2020-12-01’ AND ‘2020-12-20’

CAST

SELECT 
CAST(purchase_price AS FLOAT64) 
FROM customer.data_purchase 
ORDER BY purchase_price DESC

SELECT CAST(date AS date) AS date_only 
FROM customer_data.purchases 
The above statement changes SQL from recognizing the dates as datetime (2020-12-12T0:00:00) to only date (2020-12-12)

CAST


(expression AS typename) Where expression is the data to be converted and typename is the data type to be returned.

Converting a number to a string:

SELECT CAST(MyCount AS String) FROM MyTable 
In the above SQL statement, the following occurs: SELECT indicates that you will be selecting data from a table. CAST indicates that you will be converting the data you select to a different data type. AS comes before and identifies the data type which you are casting to. STRING indicates that you are converting the data to a string. FROM indicates which table you are selecting the data from 

Converting String to a number:

SELECT CAST(MyVarcharCol AS INT) FROM MyTable 
CAST indicates that you will be converting the data you select to a different data type. AS comes before and identifies the data type which you are casting to. INT indicates that you are converting the data to an integer

Convert date to a string:

SELECT CAST(MyDate AS STRING) FROM MyTable 

Converting a date to a datetime: Datetime values have the format of YYYY-MM-DD hh: mm: ss format

SELECT CAST (MyDate AS DATETIME) FROM MyTable

The SAFE_CAST function: Using the CAST function in a query that fails returns an error in BigQuery. It returns null instead of error.

SELECT SAFE_CAST (MyDate AS STRING) FROM MyTable

CONCAT

SELECT 
CONCAT(product_code, product_color) AS new_product_code 
FROM customer_purchase.data 
WHERE 
Product = ‘couch’ 



SELECT usertype 
CONCAT(start_station_name, “ to “, end_station_name) AS route, 
COUNT (*) as num_trips, 
ROUND(AVG(cast(tripduration as int64)/60,2) AS duration 
FROM big-query-public-data.ny 
GROUP BY start_station_name, end_station_name, usertype 
ORDER BY num_trips DESC 
LIMIT 10 
Reminder: Make sure to use a backtick (`) instead of an apostrophe (') in the FROM statement. 
About: ROUND(AVG(cast(tripduration as int64)/60,2) AS duration 
Big query stores numbers in a 64-bit memory system, which is why there's a 64 after integer in this case. we'll divide it by the number seconds in a minute (60) and tell it how far we want it to round, two decimal places(2).

COALESCE

SELECT 
COALESCE(product, product_code) AS product_info 
FROM customer_data.purchase 
Return non-null values in a list 

COUNT / COUNT DISTINCT

COUNT returns the number of rows in a specified range. COUNT DISTINCT does the same, but it will not count repeating values. Use it after the SELECT line. 
SELECT 
  COUNT(warehouse.state) AS num_states 
Warehouse.state is the column and num_states is the new column you are creating to return the count. 

CASE

The CASE statement goes through one or more conditions and returns a value as soon as a condition is met 
SELECT 
Customer_id 
CASE 
WHEN first_name = ‘Tnoy’ THEN ‘TONY’ 
ELSE first_name 
END AS cleaned_name 
FROM customer.data

DISTINCT

SELECT DISTINCT fuel_type 
FROM cars.car_info; 
SELECT DISTINCT name 
FROM playlist 
ORDER BY playlist_id 

EXTRACT

This is for if you want to use data from only one part of a column of cells- such as the date from a date format that includes more than the year. This seems like it is useful for if you arent planning on cleaning or manipulating your data before using it.
SELECT 
  EXTRACT (YEAR FROM STARTTIME) AS year,
  COUNT (*) AS number_of_rides 
FROM 
  Address.address 
GROUP BY Year 
ORDER BY year 

SELECT
  ProductId, 
  SUM (Quantity) AS unitssold,
  ROUND (MAX (UnitPrice), 2) AS UnitPrice,
  EXTRACT (YEAR FROM DATE) AS year,
  EXTRACT (MONTH FROM DATE) AS month
FROM skillful-coast-340323.sales.sales 
GROUP BY 
  year, month, ProductId 
ORDER BY 
  year, month, ProductId 
LIMIT 1000 
The ROUND (MAX (..), #) seems to need to be run because you cannot run UnitPrice on its own “SELECT list expression references column UnitPrice which is neither grouped nor aggregated at [4:5]”. Trying to run quantity on its own as a column got “SELECT list expression references column Quantity which is neither grouped nor aggregated at [3:5]”

JOIN

General join syntax: 
SELECT 
--table columns are inserted here 
Table_name1.column_name 
Table_name2.column_name 
FROM 
  Table_name1 
JOIN 
  Table_name2 
ON table_name1.column_name=table_name2.column_name 
(the column name is the key, be it primary key or foreign key that they share in common) 

SELECT 
  Customers.customer_name, 
  Orders.product_id, 
  Orders.ship_date 
FROM 
  Customers 
INNER JOIN 
  Orders 
ON customers.customer_id = orders.customer_id 

SELECT 
  employees.name AS employee_name, 
  employees.role AS employee_role, 
  departments.name AS department_name 
FROM employee_data.employees 
INNER JOIN 
  employee_data.departments ON 
  employees.department_id = departments.department_id 

SELECT 
  employees.name AS employee_name, 
  employees.role AS employee_role, 
  departments.name AS department_name 
FROM employee_data.employees 
FULL OUTER JOIN 
  employee_data.departments ON 
  employees.department_id = departments.department_id 


SELECT 
 bigquery-public-data.world_bank_intl_education.international_education.country_name, 

  bigquery-public-data.world_bank_intl_education.country_summary.country_code, 
  bigquery-public-data.world_bank_intl_education.international_education.value, 
  bigquery-public-data.world_bank_intl_education.country_summary.short_name, 
FROM 
  bigquery-public-data.world_bank_intl_education.international_education 
INNER JOIN 
  bigquery-public-data.world_bank_intl_education.country_summary 
ON bigquery-public-data.world_bank_intl_education.country_summary.country_code = bigquery-public-data.world_bank_intl_education.international_education.country_code 

To use the SAME query but with alias' to clean it up: 


SELECT 
  edu.country_name, 
  summary.country_code, 
  edu.value 
FROM 
  bigquery-public-data.world_bank_intl_education.international_education AS edu 
INNER JOIN 
  bigquery-public-data.world_bank_intl_education.country_summary AS summary 
ON edu.country_code = summary.country_code 


SELECT 
  seasons.market AS university, 
  seasons.name AS team_name, 
  seasons.wins, 
  seasons.losses, 
  seasons.ties, 
  mascots.mascot AS team_mascot 
FROM 
  bigquery-public-data.ncaa_basketball.mbb_historical_teams_seasons AS seasons 
LEFT JOIN 
  bigquery-public-data.ncaa_basketball.mascots AS mascots 
ON 
  seasons.team_id = mascots.id 
WHERE 
  seasons.season = 1984 
AND seasons.division = 1 
ORDER BY 
  seasons.market 

LENGTH

SELECT length (title) AS letters_in_title, album_id 
FROM album 
WHERE letters_in_title < 4 
The function LENGTH(title) < 4 will return any album names that are less than 4 characters long. The complete query is SELECT * FROM album WHERE LENGTH(title) < 4. The LENGTH function counts the number of characters a string contains. 
TRIM

MIN/MAX

SELECT 
MIN(length) AS min_length, 
MAX(length) AS max_length 
FROM cars.car_info; 

Modulo

An operator (%) that returns the remainder when one number is divided by another.

Order By

SELECT * 
FROM movie_data.movies 
ORDER BY Release_date DESC 



SELECT * FROM movies.data 
WHERE Genre = ‘Comedy’ 
AND Revenue > 30000000 
ORDER BY Release_date DESC 



SELECT total 
FROM invoice 
WHERE billing_city = "Chicago" 
ORDER BY total ASC 



SELECT County_of_Residence 
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality 
ORDER BY Births ASC 
LIMIT 10 



SELECT County_of_Residence 
FROM bigquery-public-data.sdoh_cdc_wonder_natality.county_natality 
WHERE year = '2018-01-01' 
ORDER BY Births DESC 
LIMIT 10 
The year had to be in ‘ ‘ for it to work, and since it is in date formate, it would not work as simply 2018. 



SELECT
The meteorologists who you’re working with have asked you to get the temperature, wind speed, and precipitation for stations La Guardia and JFK, for every day in 2020, in descending order by date, and ascending order by Station ID. Use the following query to request this information: 
SELECT stn, date, 
IF(temp=9999.9, NULL, temp) AS temperature, 
IF(wdsp="999.9", NULL, CAST(wdsp AS Float64)) AS wind_speed, 
IF(prcp=99.99, 0, prcp) AS precipitation 
FROM bigquery-public-data.noaa_gsod.gsod2020 
WHERE stn="725030" -- La Guardia 
OR stn="744860" -- JFK 
ORDER BY date DESC, stn ASC 
-- Use the IF function to replace 9999.9 values, which the dataset description explains is the default value when temperature is missing, with NULLs instead. 
-- Use the IF function to replace 999.9 values, which the dataset description explains is the default value when wind speed is missing, with NULLs instead. -- Use the IF function to replace 99.99 values, which the dataset description explains is the default value when precipitation is missing, with NULLs instead. 

SUBSTR

SELECT customer_id, 
SUBSTR(country,1,3) AS new_country 
FROM customer 
ORDER BY country 
The statement SUBSTR(country, 1, 3) AS new_country will retrieve the first 3 letters of each state name and store the result in a new column as new_country. The complete query is SELECT customer_id, SUBSTR(country, 1, 3) AS new_country FROM customer ORDER BY country. The SUBSTR function extracts a substring from a string. This function instructs the database to return 3 characters of each country, starting with the first character. 

SELECT Invoice_id, 
SUBSTR(billing_city,1,4) AS new_city 
FROM invoice 
ORDER BY billing_city 
Billing city= city, 1=starting position in string, 4=how many you return 

UPDATE

UPDATE cars.car_info 
SET num_of_doors = "four" 
WHERE make = "dodge" AND fuel_type = "gas" AND body_style = "sedan"; 

WHERE

SELECT * 
FROM cars.car_info 
WHERE num_of_doors IS NULL; 

SELECT * FROM movies.data 
WHERE Genre = ‘Comedy’ 
Because the genre is a string, you need to put the ‘ ‘ around the string name. Capitalizations matter 


SELECT CustomerId 
FROM invoices 
WHERE BillingCountry = 'Germany' AND Total > 5

WITH

With allows you to create temporary tables and query from them right within your query. You can also use SELECT INTO and CREATE TEMP TABLE 
WITH trips_over_one_hour AS ( 
SELECT * 
FROM bigquery-public-data.new_york_citibike.citibike_trips 
WHERE tripduration >= 60
)
SELECT COUNT (*) AS cnt 
FROM trips_over_one_hour 


In order to get this one to work (as I was writing myself) I had to take the ‘ ‘ from around FROM name,and make sure I had my commas in the right place. It took me a long time.
WITH 
  longest_used_bike AS ( 
   SELECT 
    bikeid,
    SUM(duration_minutes) AS trip_duration
   FROM 
    bigquery-public-data.austin_bikeshare.bikeshare_trips
   GROUP BY 
    bikeid
   ORDER BY 
    trip_duration DESC
   LIMIT 1
  )

##_find station at which longest bike leaves most often SELECT 
  trips.start_station_id, 
  COUNT(*) AS trip_ct
FROM 
  longest_used_bike AS longest
INNER JOIN 
  bigquery-public-data.austin_bikeshare.bikeshare_trips AS trips
ON longest.bikeid = trips.bikeid
GROUP BY 
  trips.start_station_id
ORDER BY
  trip_ct DESC
  LIMIT 1
