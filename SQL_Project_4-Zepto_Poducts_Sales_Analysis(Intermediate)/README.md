# 🛒 Zepto Data Cleaning & Analysis (MySQL)

This project demonstrates a complete **SQL-based Data Cleaning and Business Analysis workflow** on Zepto grocery product data using MySQL.  
It includes table setup, data type modifications, data cleaning, and business-driven analytical queries.

---

## 🧱 1. Initial Setup — Staging Table

We create a working table from the original source and add a primary key.
```sql
CREATE TABLE zepto LIKE zepto_v2;

INSERT INTO zepto
SELECT * FROM zepto_v2;
```

---

## 🔑 2. Add Primary Key Column
```sql
ALTER TABLE zepto
ADD COLUMN sku_id INT AUTO_INCREMENT PRIMARY KEY FIRST;
```

---

## 🔧 3. Modify Column Data Types

We standardize all column data types for consistency and accuracy.
```sql
ALTER TABLE zepto
MODIFY COLUMN category VARCHAR(120),
MODIFY COLUMN name VARCHAR(150) NOT NULL,
MODIFY COLUMN mrp DECIMAL(8,2),
MODIFY COLUMN discountPercent DECIMAL(5,2),
MODIFY COLUMN availableQuantity INT,
MODIFY COLUMN discountedSellingPrice DECIMAL(8,2),
MODIFY COLUMN weightInGms INT,
MODIFY COLUMN quantity INT;

SELECT * FROM zepto;
```

---

## 🧹 4. Data Cleaning

### 4.1 Check for NULL Quantities
```sql
SELECT * FROM zepto
WHERE quantity IS NULL;
```

### 4.2 Explore Distinct Categories
```sql
SELECT DISTINCT category
FROM zepto
ORDER BY 1;
```

### 4.3 Check Stock Status Distribution
```sql
SELECT outOfStock, COUNT(sku_id)
FROM zepto
GROUP BY outOfStock;
```

### 4.4 Find Duplicate Product Names
```sql
SELECT name, COUNT(sku_id) AS number_of_repetitions
FROM zepto
GROUP BY name
HAVING number_of_repetitions > 1
ORDER BY number_of_repetitions DESC;
```

### 4.5 Identify Zero-Price Products
```sql
SELECT * FROM zepto
WHERE mrp = 0 OR discountedSellingPrice = 0;
```

### 4.6 Delete Invalid Record
```sql
DELETE FROM zepto
WHERE sku_id = 3116;
```

### 4.7 Fix Price Values (Convert Paise to Rupees)
```sql
UPDATE zepto
SET mrp = mrp / 100.0,
    discountedSellingPrice = discountedSellingPrice / 100.0;

SELECT mrp, discountedSellingPrice FROM zepto;
```

---

## 📊 5. Business Analysis Queries

### Q1. Top 10 Best Value Products by Discount Percentage
```sql
SELECT DISTINCT name, mrp, discountPercent
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;
```

👉 Finds the top 10 products offering the highest discount to customers.

---

### Q2. High MRP Products That Are Out of Stock
```sql
SELECT DISTINCT name, mrp, outOfStock
FROM zepto
WHERE outOfStock = "TRUE"
ORDER BY mrp DESC;
```

👉 Identifies premium products that are currently unavailable.

---

### Q3. Estimated Revenue by Category
```sql
SELECT category, SUM(discountedSellingPrice) AS Estimated_Revenue
FROM zepto
GROUP BY category
ORDER BY SUM(discountedSellingPrice) DESC;
```

👉 Calculates potential revenue contribution from each product category.

---

### Q4. High MRP Products with Low Discount (MRP > ₹500 & Discount < 10%)
```sql
SELECT DISTINCT name, mrp, discountPercent
FROM zepto
WHERE mrp > 500.00 AND discountPercent < 10.00
ORDER BY mrp DESC, discountPercent DESC;
```

👉 Finds expensive products that offer very little discount.

---

### Q5. Top 5 Categories with Highest Average Discount
```sql
SELECT category, ROUND(AVG(discountPercent), 2) AS Average_Discount_Percentage
FROM zepto
GROUP BY category
ORDER BY Average_Discount_Percentage DESC
LIMIT 5;
```

👉 Identifies which categories provide the best deals on average.

---

### Q6. Price per Gram for Products Above 100g (Best Value First)
```sql
SELECT DISTINCT name, discountedSellingPrice,
    weightInGms,
    ROUND(discountedSellingPrice / weightInGms, 2) AS Price_per_gram
FROM zepto
WHERE weightInGms >= 100
ORDER BY Price_per_gram ASC;
```

👉 Helps customers find the best value products based on weight.

---

### Q7. Product Weight Categorization (Low / Medium / Bulk)
```sql
SELECT name, weightInGms,
    CASE
        WHEN weightInGms <= 1000 THEN "Low"
        WHEN weightInGms BETWEEN 1000 AND 5000 THEN "Medium"
        ELSE "Bulk"
    END AS Weight_Category
FROM zepto;
```

👉 Groups products into weight-based segments for better inventory planning.

---

### Q8. Total Inventory Weight per Category
```sql
SELECT category,
    SUM(weightInGms * availableQuantity) AS Weight_per_category
FROM zepto
GROUP BY category
ORDER BY Weight_per_category DESC;
```

👉 Calculates the total physical inventory weight held per category.

---

## 🚀 Conclusion

This project demonstrates:

- Staging table creation and primary key setup
- Data type standardization and column modifications
- Data cleaning including NULL handling, duplicate detection, and price corrections
- Business analysis using aggregations, filtering, CASE statements, and sorting

It can be extended further by connecting to **Power BI** or **Tableau** for interactive dashboards.
