**Schema (PostgreSQL v15)**

    CREATE TABLE customers (
        customer_id integer PRIMARY KEY,
        first_name varchar(100),
        last_name varchar(100),
        email varchar(100)
    );
    
    CREATE TABLE products (
        product_id integer PRIMARY KEY,
        product_name varchar(100),
        price decimal
    );
    
    CREATE TABLE orders (
        order_id integer PRIMARY KEY,
        customer_id integer,
        order_date date
    );
    
    CREATE TABLE order_items (
        order_id integer,
        product_id integer,
        quantity integer
    );
    
    INSERT INTO customers (customer_id, first_name, last_name, email) VALUES
    (1, 'John', 'Doe', 'johndoe@email.com'),
    (2, 'Jane', 'Smith', 'janesmith@email.com'),
    (3, 'Bob', 'Johnson', 'bobjohnson@email.com'),
    (4, 'Alice', 'Brown', 'alicebrown@email.com'),
    (5, 'Charlie', 'Davis', 'charliedavis@email.com'),
    (6, 'Eva', 'Fisher', 'evafisher@email.com'),
    (7, 'George', 'Harris', 'georgeharris@email.com'),
    (8, 'Ivy', 'Jones', 'ivyjones@email.com'),
    (9, 'Kevin', 'Miller', 'kevinmiller@email.com'),
    (10, 'Lily', 'Nelson', 'lilynelson@email.com'),
    (11, 'Oliver', 'Patterson', 'oliverpatterson@email.com'),
    (12, 'Quinn', 'Roberts', 'quinnroberts@email.com'),
    (13, 'Sophia', 'Thomas', 'sophiathomas@email.com');
    
    INSERT INTO products (product_id, product_name, price) VALUES
    (1, 'Product A', 10.00),
    (2, 'Product B', 15.00),
    (3, 'Product C', 20.00),
    (4, 'Product D', 25.00),
    (5, 'Product E', 30.00),
    (6, 'Product F', 35.00),
    (7, 'Product G', 40.00),
    (8, 'Product H', 45.00),
    (9, 'Product I', 50.00),
    (10, 'Product J', 55.00),
    (11, 'Product K', 60.00),
    (12, 'Product L', 65.00),
    (13, 'Product M', 70.00);
    
    INSERT INTO orders (order_id, customer_id, order_date) VALUES
    (1, 1, '2023-05-01'),
    (2, 2, '2023-05-02'),
    (3, 3, '2023-05-03'),
    (4, 1, '2023-05-04'),
    (5, 2, '2023-05-05'),
    (6, 3, '2023-05-06'),
    (7, 4, '2023-05-07'),
    (8, 5, '2023-05-08'),
    (9, 6, '2023-05-09'),
    (10, 7, '2023-05-10'),
    (11, 8, '2023-05-11'),
    (12, 9, '2023-05-12'),
    (13, 10, '2023-05-13'),
    (14, 11, '2023-05-14'),
    (15, 12, '2023-05-15'),
    (16, 13, '2023-05-16');
    
    INSERT INTO order_items (order_id, product_id, quantity) VALUES
    (1, 1, 2),
    (1, 2, 1),
    (2, 2, 1),
    (2, 3, 3),
    (3, 1, 1),
    (3, 3, 2),
    (4, 2, 4),
    (4, 3, 1),
    (5, 1, 1),
    (5, 3, 2),
    (6, 2, 3),
    (6, 1, 1),
    (7, 4, 1),
    (7, 5, 2),
    (8, 6, 3),
    (8, 7, 1),
    (9, 8, 2),
    (9, 9, 1),
    (10, 10, 3),
    (10, 11, 2),
    (11, 12, 1),
    (11, 13, 3),
    (12, 4, 2),
    (12, 5, 1),
    (13, 6, 3),
    (13, 7, 2),
    (14, 8, 1),
    (14, 9, 2),
    (15, 10, 3),
    (15, 11, 1),
    (16, 12, 2),
    (16, 13, 3);
---
**Query #1 Which product has the highest price?**

    SELECT product_name
    FROM products
    ORDER BY price DESC
    LIMIT 1;

| product_name |
| ------------ |
| Product M    |

---

**Query #2 Which customer has made the most orders?**

    SELECT first_name, last_name, count(order_id) AS orders
    FROM customers AS c
    JOIN orders AS o
    ON c.customer_id = o.customer_id
    GROUP BY first_name, last_name
    ORDER BY orders DESC
    LIMIT 5;

| first_name | last_name | orders |
| ---------- | --------- | ------ |
| Jane       | Smith     | 2      |
| Bob        | Johnson   | 2      |
| John       | Doe       | 2      |
| Eva        | Fisher    | 1      |
| Sophia     | Thomas    | 1      |

---

**Query #3 What’s the total revenue per product?**

    SELECT product_name, SUM(price * quantity) AS Revenue
    FROM products AS p
    JOIN order_items AS i
    ON p.product_id = i.product_id
    GROUP BY product_name
    ORDER BY product_name;

| product_name | revenue |
| ------------ | ------- |
| Product A    | 50.00   |
| Product B    | 135.00  |
| Product C    | 160.00  |
| Product D    | 75.00   |
| Product E    | 90.00   |
| Product F    | 210.00  |
| Product G    | 120.00  |
| Product H    | 135.00  |
| Product I    | 150.00  |
| Product J    | 330.00  |
| Product K    | 180.00  |
| Product L    | 195.00  |
| Product M    | 420.00  |

---

**Query #4 Find the day with the highest revenue.**

    SELECT to_char(order_date,'dd') AS day, SUM(price * quantity) AS revenue
    FROM products AS p
    JOIN order_items AS i
    ON p.product_id = i.product_id
    JOIN orders AS o
    ON o.order_id = i.order_id
    GROUP BY day
    ORDER BY revenue DESC
    LIMIT 1;

| day | revenue |
| --- | ------- |
| 16  | 340.00  |

---

**Query #5 Find the first order (by date) for each customer.**

      SELECT customer_id, min(order_date)
      FROM orders
      GROUP BY customer_id
      ORDER BY customer_id;

| customer_id | min                      |
| ----------- | ------------------------ |
| 1           | 2023-05-01T00:00:00.000Z |
| 2           | 2023-05-02T00:00:00.000Z |
| 3           | 2023-05-03T00:00:00.000Z |
| 4           | 2023-05-07T00:00:00.000Z |
| 5           | 2023-05-08T00:00:00.000Z |
| 6           | 2023-05-09T00:00:00.000Z |
| 7           | 2023-05-10T00:00:00.000Z |
| 8           | 2023-05-11T00:00:00.000Z |
| 9           | 2023-05-12T00:00:00.000Z |
| 10          | 2023-05-13T00:00:00.000Z |
| 11          | 2023-05-14T00:00:00.000Z |
| 12          | 2023-05-15T00:00:00.000Z |
| 13          | 2023-05-16T00:00:00.000Z |

---

**Query #6 Find the top 3 customers who have ordered the most distinct products**

    SELECT first_name, last_name, COUNT(DISTINCT(product_id)) AS distinct_products
    FROM orders AS o
    JOIN order_items AS i
    ON o.order_id = i.order_id
    JOIN customers AS c
    ON c.customer_id = o.customer_id
    GROUP BY first_name, last_name, c.customer_id
    ORDER BY distinct_products DESC
    LIMIT 3;

| first_name | last_name | distinct_products |
| ---------- | --------- | ----------------- |
| Jane       | Smith     | 3                 |
| Bob        | Johnson   | 3                 |
| John       | Doe       | 3                 |

---

**Query #7 Which product has been bought the least in terms of quantity?**

    SELECT product_name, SUM(quantity) AS total
    FROM products AS p
    JOin order_items AS i
    ON p.product_id = i.product_id
    GROUP BY product_name
    ORDER BY total ASC;

| product_name | total |
| ------------ | ----- |
| Product L    | 3     |
| Product I    | 3     |
| Product H    | 3     |
| Product E    | 3     |
| Product D    | 3     |
| Product G    | 3     |
| Product K    | 3     |
| Product A    | 5     |
| Product M    | 6     |
| Product F    | 6     |
| Product J    | 6     |
| Product C    | 8     |
| Product B    | 9     |

---

**Query #8 What is the median order total?**

    WITH pricelist AS(SELECT order_id, SUM(quantity*price) AS total
    FROM products AS p
    JOIN order_items AS i
    ON p.product_id = i.product_id
    GROUP BY order_id) 
    
    SELECT percentile_cont(0.5)WITHIN GROUP (ORDER BY total)
    FROM pricelist;

| percentile_cont |
| --------------- |
| 112.5           |

--- 

**Query #9 For each order, determine if it was ‘Expensive’ (total over 300), ‘Affordable’ (total over 100), or ‘Cheap’.**

    WITH pricelist AS(SELECT order_id, quantity, price
    FROM products AS p
    JOIN order_items AS i
    ON p.product_id = i.product_id) 
    
    SELECT o.order_id, SUM(quantity*price) AS total,
    CASE WHEN SUM(quantity*price) > 300 THEN 'Expensive'
    	 WHEN SUM(quantity*price) > 100 THEN 'Affordable'
         ELSE 'Cheap' END AS total_condition
    FROM orders AS o
    JOIN pricelist AS p
    ON o.order_id = p.order_id
    GROUP BY o.order_id
    ORDER BY o.order_id;

| order_id | total  | total_condition |
| -------- | ------ | --------------- |
| 1        | 35.00  | Cheap           |
| 2        | 75.00  | Cheap           |
| 3        | 50.00  | Cheap           |
| 4        | 80.00  | Cheap           |
| 5        | 50.00  | Cheap           |
| 6        | 55.00  | Cheap           |
| 7        | 85.00  | Cheap           |
| 8        | 145.00 | Affordable      |
| 9        | 140.00 | Affordable      |
| 10       | 285.00 | Affordable      |
| 11       | 275.00 | Affordable      |
| 12       | 80.00  | Cheap           |
| 13       | 185.00 | Affordable      |
| 14       | 145.00 | Affordable      |
| 15       | 225.00 | Affordable      |
| 16       | 340.00 | Expensive       |

---

**Query #10 Find customers who have ordered the product with the highest price.**

    WITH customerslist AS(SELECT first_name, last_name, order_id
    FROM customers AS c
    JOIN orders AS o
    On c.customer_id = o.customer_id
    ORDER BY c.customer_id)
    
    , pricelist AS(SELECT p.product_id, price, order_id
    FROM products AS p
    JOIN order_items AS i
    ON p.product_id = i.product_id) 
    
    SELECT first_name, last_name, p.product_id, price
    FROM customerslist AS c, pricelist AS p
    WHERE c.order_id = p.order_id
    AND price = (SELECT MAX(Price) 
                 FROM products);

| first_name | last_name | product_id | price |
| ---------- | --------- | ---------- | ----- |
| Ivy        | Jones     | 13         | 70.00 |
| Sophia     | Thomas    | 13         | 70.00 |

---

**Some insights from the analysis:**
- Product M has the highest price and revenue
- Jane Smith, Bob Johnson, and John Doe have made the most orders and ordered the most distinct products.
- IVY Jones and Sophia Thomas have ordered the Product with the highest price which is Product M
- The median order total is 112.5
