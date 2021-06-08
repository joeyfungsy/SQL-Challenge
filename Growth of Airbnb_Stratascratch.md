# SQL Question

## Source: 
www.stratascratch.com

## Potential Company: 
Airbnb

## Skill:
- CTE, SELF JOIN 

## Question:
### Growth of Airbnb
Estimate the growth of Airbnb each year using the number of hosts registered as the growth metric. The rate of growth is calculated by taking ((number of hosts registered in the current year - number of hosts registered in the previous year) / the number of hosts registered in the previous year) * 100.
Output the year, number of hosts in the current year, number of hosts in the previous year, and the rate of growth. Round the rate of growth to the nearest percent and order the result in the ascending order based on the year.
Assume that the dataset consists only of unique hosts, meaning there are no duplicate hosts listed.

## Data Scheme:
airbnb_search_details
	id 							int
	price						float
	property_type				varchar
	room_type					varchar
	amenities					varchar
	accommodates				int
	bathrooms					int
	bed_type					varchar
	cancellation_policy			varchar
	cleaning_fee				bool
	city						varchar
	host_identity_verified		varchar
	host_response_rate			varchar
	host_since					datetime
	neighbourhood				varchar
	number_of_reviews			int
	review_scores_rating		float
	zipcode						int
	bedrooms					int
	beds						int


## Possible Assumption:
- only consists of unique hosts
- the rate of growth of the first year will be null

## Logic:
- count how many host we have in total each year
	- return: year, num_host
- find "current year" and "previous year"
	- left self join: prev_year = curr_year - 1
- calculate rate_of_growth: ((num_host_curr - num_host_prev) * 1.00 / num_host_prev) * 100
- output table:
year, num_host_current, num_host_past, rate_of_growth
* rate_of_growth: round 0
- year asc 


## Query:

```
WITH host_num AS (
SELECT EXTRACT(year FROM host_since) AS year, COUNT(id) AS num_host
FROM airbnb_search_details
GROUP BY 1
)

SELECT  curr.year,
        curr.num_host AS num_host_curr,
        prev.num_host AS num_host_prev,
        ROUND((curr.num_host - prev.num_host *1.00) / prev.num_host * 100) AS rate_of_growth
FROM    host_num curr
LEFT JOIN host_num prev
    on prev.year = curr.year - 1
ORDER BY 1 asc
```

## Possible Improvement:
Using windown function: lag()

```
WITH host_num AS (
SELECT  EXTRACT(year FROM host_since) AS year,
        COUNT(id) AS curr_num_host,
        LAG(COUNT(*)) over (ORDER BY extract(year FROM host_since) ) AS prev_num_host
FROM airbnb_search_details prev
GROUP BY 1
)

SELECT *, ROUND ((curr_num_host - prev_num_host * 1.0) / prev_num_host * 100) as rate_of_growth
FROM host_num
ORDER BY year
```
