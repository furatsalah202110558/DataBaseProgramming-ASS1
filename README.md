- - Q1 A)
CREATE TABLE member (
memb_no INT PRIMARY KEY,
name VARCHAR(50) NOT NULL);

CREATE TABLE book (
isbn VARCHAR(20) PRIMARY KEY,
title VARCHAR(100) NOT NULL,
authors VARCHAR(100) NOT NULL,
publisher VARCHAR(100) NOT NULL);

CREATE TABLE borrowed (
memb_no INT,
isbn VARCHAR(20),
date DATE NOT NULL,
PRIMARY KEY (memb_no, isbn, date),
FOREIGN KEY (memb_no) REFERENCES member(memb_no),
FOREIGN KEY (isbn) REFERENCES book(isbn));

- - Q1 B)
- - B)-a
SELECT DISTINCT m.memb_no, m.name
FROM member m, borrowed b, book bk
WHERE m.memb_no = b.memb_no
AND b.isbn = bk.isbn
AND bk.publisher = 'McGraw-Hill';

- - B)-b
SELECT m.memb_no, m.name
FROM member m
WHERE NOT EXISTS 
(SELECT bk.isbn FROM book bk WHERE bk.publisher = 'McGraw-Hill'
EXCEPT 
SELECT b.isbn FROM borrowed b WHERE b.memb_no =m.memb_no);

- - B)-c
SELECT bk.publisher, m.memb_no, m.name
FROM member m, borrowed b, book bk
WHERE m.memb_no = b.memb_no
AND b.isbn = bk.isbn
GROUP BY bk.publisher, m.memb_no, m.name HAVING COUNT(*) > 5;

- - B)-d
SELECT AVG (coalesce(book_count,0))
FROM (SELECT m.memb_no, COUNT(b.isbn) as book_count
FROM member m LEFT JOIN borrowed b ON m.memb_no =b.memb_no  
GROUP BY m.memb_no) ;

- - Q2 A)

SELECT day, qty, SUM(qty) OVER (order by day) AS cumQty
FROM demand;

- - Q2 B)

WITH ranked_days AS (
SELECT product, day, qty, 
ROW_NUMBER() OVER (PARTITION BY product ORDER BY qty) AS rn
FROM demand)
SELECT product, day, qty, rn 
FROM ranked_days 
WHERE rn <= 2;
