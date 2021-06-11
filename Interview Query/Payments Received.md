# SQL Question - Payments Received

## Source: 
interviewquery.com

## Skill:
CTE, COUNT(), left join()

## Question:
You're given two tables, payments and users. The payments table holds all payments between users with the payment_state column consisting of either 'success' or 'failed'. 

How many customers that signed up in January 2020 had a combined (successful) sending and receiving volume greater than $100 in their first 30 days?

Note: The sender_id and recipient_id both represent the user_id

## Data Scheme:
payments table
	
	payment_id			integer
	sender_id			integer
	recipient_id		integer
	created_at			datetime
	payment_state		string
	amount_cents		integer
 

users table

	id					integer
	created_at			datetime


## Possible Assumption:
### For the users table: 
- there might be duplicate record; same user id might sign up more than once 

### For the payments table:
- each payment_state only contains: success or failed
- amount in cent 
- both sender and recipient need to sign up in Jan 
	transaction can be done later than 30; in 1st 30days of the sender/recipient 
	Sample case: sender A and recipient B
	SA signed up on 1/1/2020; RB signed up on 1/30/2020
	Transaction date (Success): 1/30/2020 --> both ok; if 1/31/2020: only count RB
--> so sender and recipient should be count seperately 

## Logic:

### 2 CTEs -- setting up the requirements
- To get: customers who siged up in Jan 2020 -> users.id; created_at like "%2020-01%"
	- return: id | sign_up_date

- To get: a transaction amount that is higher than 100
	- amount_cent -> change to dollar: X/100 and >100
	- payment_state 'success'
	- return: sender_id | recipient_id | trans_date | trans_dollar

### Main queries
- To get: sender_id and recipient_id that meet the requirement above 
	- left join the 2 CTEs - send_id, recipient_id = u.id
	- trans_date needs to be between sign_up_date and sign_up_date +30 day
	- UNION ALL sender and recipient 
	- return: customer

- To get: the total count of customers
	- count 
	- return: num_customers 


## Query:

```
WITH jan_create AS (

	SELECT distinct id, created_at AS sign_up_date
	FROM users
	WHERE created_at like "%2020-01%"
	GROUP BY id
	HAVING min(created_at)

	), 

	payment_100_over AS (

	SELECT sender_id, recipient_id, created_at AS trans_date, ROUND(amount_cents/100) AS trans_dollar
	FROM payments
	WHERE payment_state = 'success'
	GROUP BY payment_id 
	HAVING (trans_dollar) > 100

		)

SELECT COUNT(customer) as num_customers
FROM (

	SELECT		distinct sender_id AS customer
	FROM 		payment_100_over p
	LEFT JOIN 	jan_create j
	ON 			id = sender_id
	WHERE		(sender_id in (SELECT ID FROM jan_create) )
	 			AND
	 			(trans_date BETWEEN j.sign_up_date AND  j.sign_up_date + INTERVAL 30 day)
	UNION ALL
	SELECT		distinct recipient_id AS customer
	FROM 		payment_100_over p
	LEFT JOIN 	jan_create j
	ON 			id = sender_id
	WHERE		(recipient_id in (SELECT ID FROM jan_create) )
	 			AND
	 			(trans_date BETWEEN j.sign_up_date AND j.sign_up_date + INTERVAL 30 day)

	 ) t
	```
