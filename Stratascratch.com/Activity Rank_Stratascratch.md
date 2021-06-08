# SQL Question - Activity Rank

## Source: 
www.stratascratch.com

## Potential Company: 
Google

## Skill:


## Question:
Find the email activity rank for each user. Email activity rank is defined by the total number of emails sent. The user with the highest number of emails sent will have a rank of 1, and so on. Output the user, total emails, and their activity rank. Order records by the total emails in descending order. Sort users with the same rank score in alphabetical order.
In your rankings, return a unique value (i.e., a unique percentile) even if multiple users have the same number of emails.

## Data Scheme:
google_gmail_emails
	id			int
	from_user	varchar
	to_user		varchar
	day			int

## Possible Assumption:
- data is gerenally clean: no null. missing value and dupulicate record


## Logic:
- since we are only focus on total email sent, we will use the from_user column
- use ROW_NUMBER() function --> to give use unqiue values in ranking 
- output table: user, total_emails, activity_rank
- order: total_emails desc, user asc

## Query:

```
SELECT  from_user as user_id, count(from_user) AS total_emails,
        ROW_NUMBER() OVER (ORDER BY count(from_user) DESC) AS activity_rank
FROM google_gmail_emails
GROUP BY 1

```

## Possible Improvement:
Can use ```ntile() function ``` -- distributes rows of an ordered partition into a pre-defined number of roughly equal groups. It assigns each group a number_expression ranging from 1.
Syntax :
```
NTILE(number_expression) OVER (
   [PARTITION BY partition_expression ]
   ORDER BY sort_expression [ASC | DESC]
)
```