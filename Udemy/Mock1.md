# SQL Question

## Source: 
Udemy.com - Mock 1 with Tina Huang

## Potential Company: 
Twitter

## Skill:
Full mock interview including possible follow up questions 

## Question:
1. What information would you like to start off by pulling to get an overall understanding of the post feature
a, what are some features/metrics that you would like to understand  --> success rate
b, if we are told that the success rates are low, how can we diagnose the issue?
c, what could be a problem if we're aggregating on success_rate?
d, how to fit that 

## Data Scheme:
post_events
	user_id		int
	created_at	datetime
	event_name	varchar

		- in event_name: enter, post, cancel
			- enter: each action for all posting has an enter vent
			- post: if post is successful
			- cancel: if post is unsuccessful 


## Possible Assumption:
- Enter is the pending status; once the post is successful, the status will change to 'post', else, 'cancel'

## Answer to the Question:
1a, Looking at the overall health e.g. total number of post (Number of enters), posts made by date/month, success rate. cancel rate

## Logic:
1a, Success Rate by Date
- define success rate: number of success posts/ number of enter
- return table: date | success_rate

1b, Find pattern of the success rate
- check to see if there is a pattern from a certain time frame e.g. 

## Query:
1a, 
```
SELECT 	created_at,
		ROUND((COUNT (CASE WHEN event_name = 'post' then 1 else 0 ) * 1.00 / COUNT(event_name) * 1.00) AS success_rate
FROM 	post_events
GROUP BY	1
ORDER BY 	1
```

1b, 

```
WITH event_success AS (
SELECT 	created_at,
		ROUND((COUNT (CASE WHEN event_name = 'post' then 1 else 0 ) * 1.00 / COUNT(event_name) * 1.00) AS success_rate
FROM 	post_events
GROUP BY	1
ORDER BY 	1
)

SELECT 	EXTRACT (dow from created_at) as dow, 
		AVG(success_rate)
FROM event_success
GROUP BY 1
ORDER BY 2 asc
```

1c, 
i.e. what is a probelm for averaging on 2 success_rate that are already in percentage 
The problem is that we wouldn't really be taking into account the despiote the actual distribution of the underlying distribution because then you're just saying each one is weighted the same 

1d,
```
WITH event_success AS (
SELECT 	created_at,
		ROUND((COUNT (CASE WHEN event_name = 'post' then 1 else 0 ) * 1.00 / COUNT(event_name) * 1.00) AS success_rate
FROM 	post_events
GROUP BY	1
ORDER BY 	1
)

SELECT 	EXTRACT(dow FROM created_at) AS dow, 
		ROUND((COUNT (CASE WHEN event_name = 'post' then 1 else 0 ) * 1.00 / COUNT(event_name) * 1.00) AS success_rate
FROM event_success
GROUP BY 1
ORDER BY 2 asc
```

