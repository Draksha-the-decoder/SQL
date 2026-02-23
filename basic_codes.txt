-- Structured Query Language --
-- DDL(Data definition language) : create, drop, alter, truncate (all ddl commands are autocommited) -- 
-- DML(Data Manipulation Language) : insert, update, delete --
-- DQL(Data Query Language) : select --
-- TCL(Transaction Control Language) : commit, rollback-- 
-- datatype : int, float, char(remaining space wasted eg:mobile no, pincode(fixed size)), varchar(remaining space is released ie. reused eg: name, address), date --
CREATE DATABASE IF NOT EXISTS college; 
SHOW DATABASES;
USE college;
CREATE TABLE student(
rollno INT PRIMARY KEY, -- primary key = unique key + not null , there can be multiple unique keys but 1 primary key --
name VARCHAR(50),
marks INT(3) NOT NULL,
grade VARCHAR(1),
city VARCHAR(20)
);
SHOW TABLES;


DESC student ; -- display structure of table --


INSERT INTO student (rollno, marks, name, grade,city)
VALUES
(101,78,"anil","C","Pune"),
(102,93,"bhumika","A","Mumbai"),
(103,85,"chetan","B","Mumbai"),
(104,96,"dhruv","A","Pune"),
(105,12,"emanuel","F","Delhi"); 
INSERT INTO student VALUES (106,"farah",82,"B","Delhi");
INSERT INTO student VALUES (105,"rahul",85,"B","Pune"); -- not possible since value of primary key is repeated --
SELECT * FROM student;
INSERT INTO student VALUES (null,"rahul",85,"B","Pune"); -- PK cannot be null --


SELECT name,marks FROM student;
SELECT DISTINCT city FROM students;
SELECT * FROM student WHERE marks+10>80 AND city ="Mumbai";
SELECT * FROM student WHERE marks>80 OR city ="Mumbai";
SELECT * FROM student WHERE marks BETWEEN 80 AND 90; -- inclusive --
SELECT * FROM student WHERE city IN("Mumbai","Pune");
SELECT * FROM student WHERE city NOT IN("Mumbai","Pune");


SET autocommit=0; -- in mysql transactions are commited by default(Setting-> auto_commit_mode ->off) --
COMMIT; -- save the transations --
SELECT * FROM student
ORDER BY marks DESC -- default is asc --
LIMIT 3;

 
SELECT COUNT(name),city FROM student GROUP BY city; -- 3 rows in output (no of student in each city) --
SELECT COUNT(name), city, name FROM student GROUP BY city, name; -- 6 rows in output(no of student of same name in same city) --
SELECT COUNT(name), city, name FROM student GROUP BY city; -- error 1055 name is not in group by --
 
-- avg marks in each city in asc order --
SELECT AVG(marks),city FROM student  GROUP BY city 
ORDER BY AVG(marks);

-- count students in each city --
SELECT COUNT(city),city FROM student  GROUP BY city;

-- frequency of each grade --
SELECT COUNT(name),grade FROM student GROUP BY grade;

-- no of student in each city where max marks cross 90 --
SELECT COUNT(rollno),city FROM student GROUP BY city
HAVING MAX(marks)>90;

SET SQL_SAFE_UPDATES=0;


-- update marks of student to 82 whose rolln no is 101 --
UPDATE student SET marks=80, grade ="B" -- modify existing records
WHERE rollno=101;

UPDATE student SET grade = "B"
WHERE marks BETWEEN 80 AND 90;

-- increase marks of each student by 1 --
UPDATE student SET marks = marks+1; -- without where clause all marks are updated


-- delete student data where marks <33 --
DELETE  FROM student WHERE marks<33; -- without where all rows will be deleted --


-- Alter --
ALTER TABLE student 
ADD age INT DEFAULT 18; -- add columnn + datatype + constraint --

ALTER TABLE student
MODIFY age VARCHAR(2); -- modify datatype + constraint --

ALTER TABLE student
CHANGE age stud_age VARCHAR(3); -- rename columnn + datatype + constraint --

ALTER TABLE student
DROP stud_age;

ALTER TABLE stu
RENAME TO student; -- table rename --

-- change column name to full name --
ALTER TABLE student
CHANGE name  full_name VARCHAR(20);

-- delete student who scored less than 60 --
DELETE FROM student
WHERE marks<60;

-- delete column for grades --
ALTER TABLE student
DROP grade;

ROLLBACK; -- undo uncommited transactions --

-- JOINS INNER, OUTER(LEFT RIGHT FULL) --
CREATE TABLE course(
rollno INT PRIMARY KEY,
subject VARCHAR(10)
);

INSERT INTO course
VALUES
(102,"English"),
(104,"Maths"),
(105,"Computer"),
(106,"Science"),
(107,"Hindi");


-- syntax same as LEFT and RIGHT JOIN --
SELECT * 
FROM student
INNER JOIN course ON student.rollno = course.rollno; 


SELECT * 
FROM student as s -- alias is the alternate name given to a table --
INNER JOIN course as c ON s.rollno = c.rollno; 


-- FULL join's syntax does not exist in MySql, it exists in Oracle --
SELECT * 
FROM student
LEFT JOIN course ON student.rollno = course.rollno 
UNION  
SELECT * 
FROM student
RIGHT JOIN course ON student.rollno = course.rollno; 


-- LEFT EXCLUSIVE join --
SELECT * 
FROM student
LEFT JOIN course ON student.rollno = course.rollno
WHERE course.rollno is null; 
-- = null will not work --


-- RIGHT EXCLUSIVE join --
SELECT * 
FROM student
RIGHT JOIN course ON student.rollno = course.rollno
WHERE student.rollno is null; 


-- only intersection part --
SELECT *
FROM student
INNER JOIN course ON student.rollno = course.rollno
WHERE student.rollno IS NOT NULL AND course .rollno IS NOT NULL; 


SELECT *
FROM student as a
JOIN student as b
WHERE a.rollno = b.rollno;

-- UNION combines result of 2 or more select statements to give unique records -- 
-- every select statement should have same no of column in same order and with same data type --

-- Subqueries students who scored more than average --
SELECT full_name FROM student WHERE marks > AVG(marks); -- incorrect as aggregate function cannot be used with where --
SELECT full_name FROM student WHERE marks > (SELECT AVG(marks) FROM student );

-- students with even roll no --
SELECT full_name, rollno FROM student WHERE (rollno % 2 = 0);
SELECT full_name FROM student WHERE rollno IN (SELECT rollno FROM student WHERE (rollno % 2 = 0));

-- max marks from the students of Mumbai --
SELECT MAX(marks) FROM student WHERE city ="Mumbai";
SELECT MAX(marks) FROM ( SELECT * FROM student WHERE city ="Mumbai" ) AS temp; -- if subquery is used inside from then alias is required --

SELECT (SELECT MAX(marks) FROM student), marks, full_name FROM student; -- with select subquery should return only 1 row --


-- A view is a virtual table based on the result-set of an sql statement --
CREATE VIEW view1 AS -- CREATE OR REPLACE VIEW view1 AS ,we can update without dropping view --
SELECT rollno, full_name, marks FROM student ;
SELECT * FROM view1 WHERE marks>90 ;
DROP VIEW IF EXISTS view1;


TRUNCATE  student; -- deletes the data from table, schema is not deleted , we cannot use rollback unlike in delete--
DROP TABLE student; -- data along with schema is deleted --
DROP DATABASE IF EXISTS college;
