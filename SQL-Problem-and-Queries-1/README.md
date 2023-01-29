/* Try using LIMIT yourself below by writing a query that displays all the data in the occurred_at, account_id, and channel columns of the web_events table, and limits the output to only the first 15 rows. */

SELECT occurred_at, account_id, channel
FROM web_events
LIMIT 15;

## SQL Data Cleaning

### Left & Right Functions

#### In the accounts table, there is a column holding the website for each company. The last three digits specify what type of web address they are using. Pull these extensions and provide how many of each website type exist in the accounts table. 

SELECT COUNT(DISTINCT RIGHT(website, 3))
FROM accounts;

#### There is much debate about how much the name (or even the first letter of a company name) matters. Use the accounts table to pull the first letter of each company name to see the distribution of company names that begin with each letter (or number). 
SELECT LEFT(UPPER(name), 1) AS first_letter, COUNT(*) num_companies
FROM accounts
GROUP BY 1
ORDER BY 2 DESC;

OR

SELECT RIGHT(website, 3) AS domain, COUNT(*) num_companies
FROM accounts
GROUP BY 1
ORDER BY 2 DESC;

#### Use the accounts table and a CASE statement to create two groups: one group of company names that start with a number and the second group of those company names that start with a letter. What proportion of company names start with a letter? 
SELECT SUM(num) nums, SUM(letter) letters
FROM (SELECT name, CASE WHEN LEFT(UPPER(name), 1) IN ('0','1','2','3','4','5','6','7','8','9') 
THEN 1 ELSE 0 END AS num, 
CASE WHEN LEFT(UPPER(name), 1) IN ('0','1','2','3','4','5','6','7','8','9') 
THEN 0 ELSE 1 END AS letter
FROM accounts) t1;

#### Consider vowels as a, e, i, o, and u. What proportion of company names start with a vowel, and what percent start with anything else? */

SELECT SUM(vowels) AS vowels, SUM(other) AS other
FROM (SELECT name, CASE WHEN LEFT(UPPER(name), 1) IN ('A','E','I','O','U
THEN 1 ELSE 0 END AS vowels, 

CASE WHEN LEFT(UPPER(name), 1) IN ('A','E','I','O','U') 
  THEN 0 ELSE 1 END AS other
ROM accounts) t1;


### Concat Function

#### Suppose the company wants to assess the performance of all the sales representatives. Each sales representative is assigned to work in a particular region. To make it easier to understand for the HR team, display the concatenated sales_reps.id, ‘_’ (underscore), and region.name as EMP_ID_REGION for each sales representative. 

SELECT s.name, CONCAT(s.id, '_', r.name) AS EMP_ID_REGION
FROM sales_reps AS s
JOIN region AS r
ON r.id = s.region_id
ORDER BY 1;

Or

SELECT CONCAT(SALES_REPS.ID, '_', REGION.NAME) EMP_ID_REGION, SALES_REPS.NAME
FROM SALES_REPS
JOIN REGION
ON SALES_REPS.REGION_ID = REGION_ID;


/*From the accounts table, display the name of the client, the coordinate as concatenated (latitude, longitude), email id of the primary point of contact as <first letter of the primary_poc><last letter of the primary_poc>@<extracted name and domain from the website> */

SELECT NAME, CONCAT(LAT, ', ', LONG) COORDINATE, CONCAT(LEFT(PRIMARY_POC, 1), RIGHT(PRIMARY_POC, 1), '@', SUBSTR(WEBSITE, 5)) EMAIL
FROM ACCOUNTS;


/* From the web_events table, display the concatenated value of account_id, '_' , channel, '_', count of web events of the particular channel. */

SELECT name, CONCAT(lat, long) AS coordinate, CONCAT(LOWER(LEFT(primary_poc, 1)), RIGHT(primary_poc, 1), '@', SUBSTR(website, 4)) AS email_id
FROM accounts;
WITH t1 AS
(
     SELECT account_id, channel, COUNT(*)
     FROM web_events
     GROUP BY 1, 2
     ORDER BY 1
  )
SELECT CONCAT(account_id, '_', channel, '_', COUNT)
FROM t1;

/* OR */

WITH T1 AS (
 SELECT ACCOUNT_ID, CHANNEL, COUNT(*) 
 FROM WEB_EVENTS
 GROUP BY ACCOUNT_ID, CHANNEL
 ORDER BY ACCOUNT_ID
)
SELECT CONCAT(T1.ACCOUNT_ID, '_', T1.CHANNEL, '_', COUNT)
FROM T1;


### POSITION & STRPOS 

Use the accounts table to create first and last name columns that hold the first and last names for the primary_poc. 

SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 ) first_name, 
RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name
FROM accounts;

Now see if you can do the same thing for every rep name in the sales_reps table. Again provide first and last name columns.

SELECT LEFT(name, STRPOS(name, ' ') -1 ) first_name, 
    RIGHT(name, LENGTH(name) - STRPOS(name, ' ')) last_name
FROM sales_reps;


Each company in the accounts table wants to create an email address for each primary_poc. The email address should be the first name of the primary_poc . last name primary_poc @ company name .com.

WITH t1 AS (
 SELECT LEFT(primary_poc,     STRPOS(primary_poc, ' ') -1 ) first_name,  RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name, name
 FROM accounts)
SELECT first_name, last_name, CONCAT(first_name, '.', last_name, '@', name, '.com')
FROM t1;


You may have noticed that in the previous solution some of the company names include spaces, which will certainly not work in an email address. See if you can create an email address that will work by removing all of the spaces in the account name, but otherwise, your solution should be just as in question 1. 

WITH t1 AS (
 SELECT LEFT(primary_poc,     STRPOS(primary_poc, ' ') -1 ) first_name,  RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name, name
 FROM accounts)
SELECT first_name, last_name, CONCAT(first_name, '.', last_name, '@', REPLACE(name, ' ', ''), '.com')
FROM  t1;


We would also like to create an initial password, which they will change after their first log in. The first password will be the first letter of the primary_poc's first name (lowercase), then the last letter of their first name (lowercase), the first letter of their last name (lowercase), the last letter of their last name (lowercase), the number of letters in their first name, the number of letters in their last name, and then the name of the company they are working with, all capitalized with no spaces.

WITH t1 AS (
  SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 ) first_name,  RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name, name
  FROM accounts)
SELECT first_name, last_name, CONCAT(first_name, '.', last_name, '@', name, '.com'), LEFT(LOWER(first_name), 1) || RIGHT(LOWER(first_name), 1) || LEFT(LOWER(last_name), 1) || RIGHT(LOWER(last_name), 1) || LENGTH(first_name) || LENGTH(last_name) || REPLACE(UPPER(name), ' ', '')
FROM t1;
