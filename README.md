# Project Overview : SQL Fundamentals
Introduction

This project is designed to help understand the foundational concepts of SQL, including Data Definition Language (DDL), Data Manipulation Language (DML), and Data Query Language (DQL). It provides practical exposure to how SQL is used in real-world business scenarios.

Additionally, the project offers hands-on experience in automation and testing, enabling learners to apply SQL in end-to-end workflows involving data generation, validation, and comparison.

Project Goal & Objective

This project consists of 10 key tasks that collectively provide a comprehensive understanding of SQL. It  covers the fundamental concepts such as DDL, DML, and DQL and also demonstrates how SQL is applied in real-world business scenarios

Req1     
Generate a random data input files
a) Feed-1  which has 10 columns with 10 rows,
b) Feed-2 which has 15 columns with 15 rows ,
c) Feed-3 which has 20 columns  with 20 rows

Req2
Automate the Req 1 input file generation using SQL  scripts and the parameter will be "Feed name" & Number of Rows to populate Data

Req3
Write SQL script to identify the duplicate (rows) in each of the table Feed-1, 2, 3

Req4
Write the duplicate records in output file - "duplicates"

Req5
Create a script to replace all the duplicates with Unique rows and update back to respective Feed table

Req6
Execute the duplicate script and check the output is zero

Req7
Create SQL script to compare data from Feed-2,3 to Feed-1 and write in output file on the compared results

Req8
Create Test plan with all kinds of manual test cases in order to test this End to End functionality

Req9
Automate the test cases (if possible) using any method but should be automated…

Req 10
Document everything in word with all screen grabs as proper Project Document

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------Below are the codes for the assignment given:-  

  


 


-- Step 1: Create and use the database
CREATE DATABASE IF NOT EXISTS FEED1;
USE FEED1;

-- Step 2: Create Feed1 table
CREATE TABLE IF NOT EXISTS Feed1 (
    ID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(50),
    Age INT,
    Email VARCHAR(100),
    Salary DECIMAL(10,2),
    Dept VARCHAR(20),
    JoiningDate DATE,
    City VARCHAR(50),
    Status CHAR(1),
    Code VARCHAR(10)
);

-- Step 3: Create Feed2 table
CREATE TABLE IF NOT EXISTS Feed2 LIKE Feed1;

-- Step 4: Create Feed3 table
CREATE TABLE IF NOT EXISTS Feed3 LIKE Feed1;

-- Step 5: Insert sample data into Feed1, Feed2, Feed3
INSERT INTO Feed1 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
SELECT 
    SUBSTRING(MD5(RAND()), 1, 8), 
    FLOOR(RAND() * 50) + 20,
    CONCAT(SUBSTRING(MD5(RAND()), 1, 5), '@mail.com'),
    ROUND(RAND() * 100000, 2),
    'IT',
    CURDATE(),
    'CityA',
    'A',
    SUBSTRING(MD5(RAND()), 1, 5)
FROM information_schema.tables
WHERE table_name IS NOT NULL
LIMIT 100;

INSERT INTO Feed2 SELECT * FROM Feed1 LIMIT 100;
INSERT INTO Feed3 SELECT * FROM Feed1 LIMIT 100;


DELIMITER $$

CREATE PROCEDURE GenerateFeedData (
    IN FeedName VARCHAR(20),
    IN RowCount INT
)
BEGIN
    IF FeedName = 'Feed1' THEN
        INSERT INTO Feed1 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
        SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
        FROM Feed2
        ORDER BY JoiningDate DESC
        LIMIT RowCount;
        
    ELSEIF FeedName = 'Feed2' THEN
        INSERT INTO Feed2 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
        SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
        FROM Feed3
        ORDER BY JoiningDate DESC
        LIMIT RowCount;
        
    ELSEIF FeedName = 'Feed3' THEN
        INSERT INTO Feed3 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
        SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
        FROM Feed1
        ORDER BY JoiningDate DESC
        LIMIT RowCount;
        
    ELSE
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Invalid FeedName provided.';
    END IF;
END$$

DELIMITER ;



DELIMITER $$

CREATE PROCEDURE CheckDuplicatesInFeeds()
BEGIN
    -- Feed1 duplicates
    SELECT 'Feed1' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
    FROM Feed1
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1;

    -- Feed2 duplicates
    SELECT 'Feed2' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
    FROM Feed2
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1;

    -- Feed3 duplicates
    SELECT 'Feed3' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
    FROM Feed3
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1;
END$$

DELIMITER ;
CREATE TABLE Duplicates_Feed1 AS
SELECT *
FROM Feed1
WHERE (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code) IN (
    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed1
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1
);





SET SQL_SAFE_UPDATES = 0;
UPDATE FEED1
SET NAME = "RAM"
WHERE AGE = 61;


INSERT INTO Feed1 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
FROM Feed1
LIMIT 10;

SELECT* FROM FEED1
SELECT*FROM FEED2
SET SQL_SAFE_UPDATES = 0;
UPDATE FEED2
SET NAME = "RAM"
WHERE AGE = 50;

SELECT* FROM FEED2
SELECT*FROM FEED3
UPDATE FEED3
SET NAME = "RAM"
WHERE AGE = 55;
-- Identify duplicate rows in Feed1
SELECT 'Feed1' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
FROM Feed1
GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
HAVING COUNT(*) > 1;

-- Identify duplicate rows in Feed2
SELECT 'Feed2' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
FROM Feed2
GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
HAVING COUNT(*) > 1;

-- Identify duplicate rows in Feed3
SELECT 'Feed3' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code, COUNT(*) AS DuplicateCount
FROM Feed3
GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
HAVING COUNT(*) > 1;

CREATE TABLE IF NOT EXISTS duplicates (
    FeedName VARCHAR(10),
    Name VARCHAR(50),
    Age INT,
    Email VARCHAR(100),
    Salary DECIMAL(10,2),
    Dept VARCHAR(20),
    JoiningDate DATE,
    City VARCHAR(50),
    Status CHAR(1),
    Code VARCHAR(10)
);

INSERT INTO duplicates
SELECT 'Feed1' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
FROM Feed1
WHERE (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code) IN (
    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed1
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1
);



INSERT INTO duplicates
SELECT 'Feed3' AS FeedName, Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
FROM Feed3
WHERE (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code) IN (
    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed3
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1
);





-- Insert into Feed3
INSERT INTO Feed3 (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code)
SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
FROM NewFeedData3
WHERE (Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code) NOT IN (
    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code FROM Feed3
);

CALL CheckDuplicatesInFeeds();

CREATE TEMPORARY TABLE temp_feed2 AS
SELECT MIN(ID) AS ID
FROM Feed2
GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code;

DELETE FROM Feed2
WHERE ID NOT IN (SELECT ID FROM temp_feed2);



SELECT COUNT(*) AS TotalDuplicates FROM (
    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed1
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1

    UNION ALL

    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed2
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1

    UNION ALL

    SELECT Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    FROM Feed3
    GROUP BY Name, Age, Email, Salary, Dept, JoiningDate, City, Status, Code
    HAVING COUNT(*) > 1
) AS AllDuplicates;


    



