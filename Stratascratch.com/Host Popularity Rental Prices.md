# SQL Question - Host Popularity Rental Prices

## Source: 
www.stratescratch.com

## Potential Company: 
Airbnb

## Skill:
CTE, aggragation function, CASE()

## Question:
You’re given a table of rental property searches by users. The table consists of search results and outputs host information for searchers. Find the minimum, average, maximum rental prices for each host’s popularity rating. The host’s popularity rating is defined as below:
    0 reviews: New
    1 to 5 reviews: Rising
    6 to 15 reviews: Trending Up
    16 to 40 reviews: Popular
    more than 40 reviews: Hot

Tip: The `id` column in the table refers to the search ID. You'll need to create your own host_id by concating price, room_type, host_since, zipcode, and number_of_reviews.


## Data Scheme:
airbnb_host_searches

	id 										int
	price 									float
	property_type 							varchar
	room_type								varchar
	amenities								varchar
	accommodates							int
	bathrooms								int
	bed_type								varchar
	cancellation_policy						varchar
	cleaning_fee							bool
	city 									varchar
	host_identity_verified 					varchar
	host_response_rate 						varchar
	host_since 								datetime
	neighbourhood 							varchar
	number_of_reviews 						int
	review_scores_rating 					float
	zipcode 								int
	bedrooms 								int
	beds 									int

## Possible Assumption:
- build the subquery to gather the information we need 
	- output table: host_id | number_of_reviews | price | type_rating
- build the another query to do the aggregation 
	- output table: type_rating | min_price | avg_price | max_price

## Logic:
- host_id by concating price, room_type, host_since, zipcode, and number_of_reviews
 	- concat()

## Query:

```
WITH rating AS (
-- subquery
		SELECT	CONCAT(price, room_type, host_since, zipcode) as host_id,
				number_of_reviews, price,
				CASE 
					WHEN number_of_reviews = 0 THEN 'NEW'
					WHEN number_of_reviews BETWEEN 1 AND 5 THEN 'Rising'
					WHEN number_of_reviews BETWEEN 6 AND 15 THEN 'Trending Up'
					WHEN number_of_reviews BETWEEN 16 AND 40 THEN 'Popular'
					WHEN number_of_reviews > 40 THEN 'Hot'
				END AS type_rating
		FROM airbnb_host_searches
		GROUP BY 1,2,3
)

SELECT type_rating, min(price) AS min_price, avg(price) AS avg_price, max(price) AS max_price
FROM rating
GROUP BY 1
```