# Music-Management-Data-Analysis
Music Management Data Analysis: Explore insights from a comprehensive dataset covering albums, artists, customers, and sales transactions. SQL queries unveil patterns and trends in the world of music management.

Q1 What is the total number of albums in the database?

SELECT COUNT(*) AS total_album
FROM album;

Q2 How many unique artist are there?

SELECT COUNT(DISTINCT artist_id) AS unique_artists
FROM artist;

Q4 What is the total number of customers ?

SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM customer;

Q5 Who are the top 5 artists with the most albums?

SELECT a.name AS artist_name, COUNT(b.album_id) AS album_count
FROM artist a
JOIN album b
ON a.artist_id = b.artist_id
GROUP BY artist_name
ORDER BY album_count DESC
LIMIT 5;

Q6 Which artist has the highest number of tracks?

SELECT a.name as artist_name, COUNT(c.track_id) AS track_count
FROM artist a 
JOIN album b 
ON a.artist_id = b.artist_id
JOIN track c 
ON b.album_id = c.album_id
GROUP BY artist_name
ORDER BY track_count DESC;

Q7 What is the distribution of artists across different genres?

SELECT g.Name AS genre_name, COUNT(DISTINCT a.artist_id) AS artist_count
FROM Genre g
LEFT JOIN Track t ON g.genre_Id = t.genre_Id
LEFT JOIN Album alb ON t.album_Id = alb.album_id
LEFT JOIN Artist a ON alb.artist_id = a.artist_id
GROUP BY g.Name
ORDER BY artist_count DESC;

Q8 How many customers are there from each country?

SELECT DISTINCT country, COUNT(customer_id) as customer_count
FROM customer
GROUP BY country
ORDER BY customer_count DESC;

Q9 Who are the top 5 customers with the highest total invoice amounts?

SELECT a.customer_id, a.first_name, a.last_name, SUM(b.total) AS total_invoice_amount
FROM customer a
JOIN invoice b ON a.customer_id = b.customer_id
GROUP BY a.customer_id, a.first_name, a.last_name
ORDER BY total_invoice_amount DESC
LIMIT 5;

Q10 How does the sales distribution vary over different months or years?

SELECT 
	EXTRACT(YEAR FROM invoice_date) AS sales_year, 
	SUM(total) AS total_sales
FROM invoice
GROUP BY sales_year
ORDER BY sales_year DESC;
	
Q11 What are the top 5 tracks based on their lengths (duration)?

SELECT name AS track_name, milliseconds,
RANK() OVER(ORDER BY milliseconds DESC) AS track_rank
FROM track
LIMIT 5;

Q12 Find the top customer (based on total sales) in each country.

WITH CountryRankedCustomers AS (
    SELECT
        customer_id,
        first_name,
        last_name,
        country,
        total,
        RANK() OVER (PARTITION BY country ORDER BY total DESC) AS country_rank
    FROM (
        SELECT
            c.customer_id,
            c.first_name,
            c.last_name,
            c.country, 
            SUM(i.total) AS total
        FROM
            customer c
            JOIN invoice i ON c.customer_id = i.customer_id
        GROUP BY
            c.customer_id,
            c.first_name,
            c.last_name,
            c.country
    ) AS country_totals
)

SELECT
    customer_id,
    first_name,
    last_name,
    country,
    total
FROM CountryRankedCustomers
WHERE country_rank = 1
ORDER BY total DESC;
