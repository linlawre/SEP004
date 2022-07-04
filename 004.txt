--1.

CREATE VIEW view_product_order_Lin AS
SELECT *
FROM Products

SELECT ProductName, UnitsOnOrder
FROM view_product_order_Lin

--2.

CREATE PROCEDURE sp_product_order_quantity_Lin
@a int,
@b int OUT
AS
Begin
	Print @a
	SELECT @b = SUM(Quantity)
	FROM [Order Details]
	WHERE ProductID = @a
	GROUP BY ProductID
	ORDER BY ProductID
End


Begin
declare @d int
Exec sp_product_order_quantity_Lin 77, @d OUT
SELECT @d
End

--3.

CREATE PROCEDURE sp_product_order_city_Lin
@a varchar(255)
AS
Begin
WITH 
ddd(pn, pid, odoi, odq ) AS
(
	SELECT p.ProductName ,p.ProductID, od.orderId , od.Quantity
	FROM Products p
	Join [Order Details] od ON p.ProductID = od.ProductID
	),
ccc (oid, sc) AS
(
	SELECT OrderID, ShipCity
	FROM Orders
)
SELECT pn, sc, SUM(odq) [q]
FROM ddd d join ccc c On d.odoi = c.oid
WHERE pn = @a
GROUP BY pn, sc
ORDER BY SUM(odq) DESC
End 

CREATE TABLE #vv (
	pn varchar(255),
	sc varchar(255),
	q int
);

INSERT INTO #vv
EXEC  sp_product_order_city_Lin 'tofu'

SELECT *
FROM #vv

--4.

CREATE TABLE people_lin (id int, City varchar(255))
CREATE TABLE city_lin (id int,Name varchar(255), City int)

INSERT INTO people_lin
VALUES (1,'Seattle'),(2, 'Green Bay')

INSERT INTO city_lin
VALUES (1, 'Aaron Rodgers',  2),(2, 'Russell Wilson', 1), (3, 'Jody Nelson', 2)

UPDATE people_lin
SET City = 'Madison'
WHERE City = 'Seattle'

SELECT *
FROM people_lin

CREATE VIEW vvvv AS
SELECT Name
FROM city_lin
WHERE City = 2;

SELECT *
FROM vvvv


--5.

CREATE PROC sp_birthday_employees_lin
AS
BEGIN
	CREATE TABLE #birthday_employees_lin (cele DATE)

	INSERT INTO #birthday_employees_lin
	SELECT BirthDate
	FROM Employees
	WHERE MONTH(BirthDate) = 2

	SELECT *
	FROM #birthday_employees_lin
END

exec sp_birthday_employees_lin

--6.

-- CHECKSUM TABLE original_table, backup_table;