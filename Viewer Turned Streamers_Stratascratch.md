# SQL Question

## Source: 
www.stratascratch.com

## Potential Company: 
Twitch (Streaming industry)

## Skill:
CTE, aggregate function, window function 

## Question:
Viewers Turned Streamers
From users who had their first session as a viewer, how many streamer sessions have they had? Return the user id and number of sessions in descending order. In case there are users with the same number of sessions, order them by ascending user id.

## Data Scheme:
twitch_sessions
	user_id			int
	session_start	datetime
	session_end		datetime
	session_id		int
	session_type	varchar

## Possible Assumption:
1. Time frame: 1 week/month/quarter/year
2. session_type: define if user is a streamer/viewer --> 2 values: streamer/viewer; no other values
3. Every row is an unqiue session --> unqiue seesion_id
4. Data is generally clean --> each session has a session start and end date/time

## Logic:
- to identify users that had their 1st session as a viewer --> for counting the sessions
	- rank by session start time --> using window function (dense rank function)
	- return: user_id, session_type and rank
- to filter by the 1st session and by "viewer" == the user was a viewer in their 1st session 
	- return: user_id
- count session by user
- order by no. of sessions DESC , user_id ASC 


## Query:

```
SELECT	user_id,
		COUNT(*) AS no_sessions
FROM	twitch_sessions
WHERE user_id IN 
	(
	SELECT	user_id
	FROM	
		(	
		SELECT 	user_id, 
				session_type,
				DENSE RANK() OVER (PARTITION BY user_id ORDER BY session_start) AS streams_order
		FROM	twitch_sessions
		) t1
	WHERE streams_order = 1 AND session_type = 'viewer'
	)
	AND session_type = 'streamer'
GROUP BY 1
ORDER by no_sessions DESC , user_id ASC
```

