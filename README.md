-- Create Stores Table
CREATE TABLE Stores (
    Store_ID INT PRIMARY KEY,
    Store_Location VARCHAR(100)
);

-- Create Products Table
CREATE TABLE Products (
    Product_ID INT PRIMARY KEY,
    Product_Name VARCHAR(100),
    Category VARCHAR(50),
    Price DECIMAL(10, 2)
);

-- Create Sales Table
CREATE TABLE Sales (
    Sale_ID INT PRIMARY KEY,
    Store_ID INT,
    Product_ID INT,
    Sale_Date DATE,
    Quantity_Sold INT,
    Revenue DECIMAL(15, 2),
    FOREIGN KEY (Store_ID) REFERENCES Stores(Store_ID),
    FOREIGN KEY (Product_ID) REFERENCES Products(Product_ID)
);

-- Create Weather Table
CREATE TABLE Weather (
    Weather_ID INT PRIMARY KEY,
    Date DATE,
    Temperature DECIMAL(5, 2),
    Precipitation DECIMAL(5, 2)
);

-- Create Holidays Table
CREATE TABLE Holidays (
    Holiday_ID INT PRIMARY KEY,
    Date DATE,
    Holiday_Name VARCHAR(50),
    Holiday_Type VARCHAR(50)
);

-- Create Macroeconomic Table
CREATE TABLE Macroeconomic (
    Date DATE PRIMARY KEY,
    GDP DECIMAL(15, 2),
    CPI DECIMAL(10, 2),
    Cotton_Production DECIMAL(15, 2),
    Unemployment_Rate DECIMAL(5, 2)
);

-- Insert Sample Data into Stores Table
INSERT INTO Stores (Store_ID, Store_Location) VALUES 
(1, 'New York'),
(2, 'Los Angeles'),
(3, 'Chicago');

-- Insert Sample Data into Products Table
INSERT INTO Products (Product_ID, Product_Name, Category, Price) VALUES 
(1, 'Product A', 'Category 1', 10.99),
(2, 'Product B', 'Category 1', 12.99),
(3, 'Product C', 'Category 2', 9.99);

-- Insert Sample Data into Sales Table
INSERT INTO Sales (Sale_ID, Store_ID, Product_ID, Sale_Date, Quantity_Sold, Revenue) VALUES 
(1, 1, 1, '2013-01-01', 100, 1099.00),
(2, 1, 2, '2013-01-01', 150, 1948.50),
(3, 2, 3, '2013-02-01', 200, 1998.00);

-- Insert Sample Data into Weather Table
INSERT INTO Weather (Weather_ID, Date, Temperature, Precipitation) VALUES 
(1, '2013-01-01', 30.5, 0.1),
(2, '2013-02-01', 32.1, 0.0);

-- Insert Sample Data into Holidays Table
INSERT INTO Holidays (Holiday_ID, Date, Holiday_Name, Holiday_Type) VALUES 
(1, '2013-01-01', 'New Year', 'National Holiday'),
(2, '2013-12-25', 'Christmas', 'Religious Holiday');

-- Insert Sample Data into Macroeconomic Table
INSERT INTO Macroeconomic (Date, GDP, CPI, Cotton_Production, Unemployment_Rate) VALUES 
('2013-01-01', 15000.00, 220.5, 10000.00, 5.5),
('2013-02-01', 15200.00, 221.0, 10200.00, 5.4);

-- Analysis Queries

-- Query 1: Which year had the highest sales?
SELECT YEAR(Sale_Date) AS Year, SUM(Revenue) AS Total_Sales
FROM Sales
GROUP BY YEAR(Sale_Date)
ORDER BY Total_Sales DESC
LIMIT 1;

-- Query 2: How was the weather during the year of the highest sales?
SELECT W.Weather_ID, W.Temperature, W.Precipitation, W.Date
FROM Sales S
JOIN Weather W ON S.Sale_Date = W.Date
WHERE YEAR(S.Sale_Date) = (
    SELECT YEAR(Sale_Date)
    FROM Sales
    GROUP BY YEAR(Sale_Date)
    ORDER BY SUM(Revenue) DESC
    LIMIT 1
)
GROUP BY W.Weather_ID, W.Temperature, W.Precipitation, W.Date;

-- Query 3: Conclude whether the weather has an essential impact on sales.
SELECT W.Temperature, W.Precipitation, SUM(S.Revenue) AS Total_Sales
FROM Sales S
JOIN Weather W ON S.Sale_Date = W.Date
GROUP BY W.Temperature, W.Precipitation
ORDER BY Total_Sales DESC;

-- Query 4: Do the sales always rise near the holiday season for all the years?
SELECT YEAR(S.Sale_Date) AS Year, H.Holiday_Name, SUM(S.Revenue) AS Total_Sales
FROM Sales S
JOIN Holidays H ON S.Sale_Date = H.Date
GROUP BY YEAR(S.Sale_Date), H.Holiday_Name
ORDER BY Year, Total_Sales DESC;

-- Query 5: Analyze the relationship between sales and the different macroeconomic variables in the dataset.
SELECT M.GDP, M.CPI, M.Cotton_Production, M.Unemployment_Rate, SUM(S.Revenue) AS Total_Sales
FROM Sales S
JOIN Macroeconomic M ON S.Sale_Date = M.Date
GROUP BY M.GDP, M.CPI, M.Cotton_Production, M.Unemployment_Rate
ORDER BY Total_Sales DESC;
