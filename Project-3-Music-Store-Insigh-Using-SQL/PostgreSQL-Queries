/*Project 3 Queries by Hisham Macaraya*/



/*Query #1*/

SELECT m.name AS media_type, SUM(i.unitprice * i.quantity) AS total_invoice 
FROM mediatype AS m
JOIN track AS t
ON m.mediatypeid = t.mediatypeid
JOIN invoiceline AS i
ON t.trackid = i.trackid 
JOIN invoice AS inv
ON i.invoiceid = inv.invoiceid
GROUP BY 1
ORDER BY 2 DESC;

/*year check for Query 1*/

SELECT MIN(DATE_PART('YEAR', invoicedate)) AS start_year, MAX(DATE_PART('YEAR', invoicedate)) AS latest_year 
FROM invoice;



/*Query #2*/

SELECT (DATE_PART('YEAR', inv.invoicedate)) AS year, SUM(i.unitprice * i.quantity) AS total_invoice
FROM invoice AS inv
JOIN invoiceline AS i
ON i.invoiceid = inv.invoiceid
JOIN track AS t
ON t.trackid = i.trackid 
WHERE inv.invoicedate BETWEEN '2009-01-01' AND '2014-01-01'
GROUP BY 1
ORDER BY 1 DESC;



/*Query #3*/

SELECT CASE 
	WHEN g.name = 'Rock' THEN '1_Rock'
	WHEN g.name = 'Latin' THEN '2_Latin'
	WHEN g.name = 'Metal' THEN '3_Metal'
	WHEN g.name = 'Alternative & Punk' THEN '4_Alternative & Punk'
	WHEN g.name = 'Jazz' THEN '5_Jazz' 
		ELSE '6_Others' END AS top_genre, 
	SUM(inv.total) AS total_invoice
FROM genre AS g
JOIN track AS t
ON g.genreid = t.genreid
JOIN invoiceline AS i
ON t.trackid = i.trackid
JOIN invoice AS inv
ON i.invoicelineid = inv.invoiceid
GROUP BY 1
ORDER BY 1;

/*Alternative to Query #3, notice that SUM invoice of others genre is included on the first query*/

SELECT g.name, SUM(inv.total) AS total_invoice
FROM genre AS g
JOIN track AS t
ON g.genreid = t.genreid
JOIN invoiceline AS i
ON t.trackid = i.trackid
JOIN invoice AS inv
ON i.invoicelineid = inv.invoiceid
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;

/*Distict Genre Count*/
SELECT COUNT(DISTINCT name)
FROM genre;



/*Query #4*/

SELECT g.name, COUNT(DISTINCT c.customerid)
FROM genre AS g
JOIN track AS t
ON g.genreid = t.genreid
JOIN invoiceline AS i
ON t.trackid = i.trackid
JOIN invoice AS inv
ON i.invoicelineid = inv.invoiceid
JOIN customer AS c
ON c.customerid = inv.customerid
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;

/*Alternative Query for #4*/
SELECT g.name, COUNT(DISTINCT c.customerid)
FROM genre AS g
JOIN track AS t
ON g.genreid = t.genreid
JOIN invoiceline AS i
ON t.trackid = i.trackid
JOIN invoice AS inv
ON i.invoicelineid = inv.invoiceid
JOIN customer AS c
ON c.customerid = inv.customerid
WHERE g.name IN('Rock', 'Latin', 'Metal', 'Alternative & Punk', 'Jazz')
GROUP BY 1
ORDER BY 2 DESC;


	/*Distict Genre Count*/
SELECT DISTINCT COUNT(customerid)
FROM customer;




/*END*/
