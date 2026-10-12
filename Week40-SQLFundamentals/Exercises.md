# Week 40 — Exercises: SQL Fundamentals

> [!IMPORTANT]
> **_How to Complete These Exercises_**
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

## Exercise 1: TrailShop Project Task

This week you'll build the TrailShop database from scratch and practice manipulating data.

### Task 1.1: Create the Database

1. Open your PostgreSQL terminal (psql) or pgAdmin
2. Create a new database called `trailshop`
3. Connect to it

### Task 1.2: Create All Tables

Write and execute the CREATE TABLE statements for all six TrailShop tables in the correct order:

- categories
- customers
- products
- product_categories
- orders
- order_items

**Requirements:**

- Use appropriate data types for each column
- Include all constraints from the theory (NOT NULL, UNIQUE, CHECK, FOREIGN KEY, DEFAULT)
- Use SERIAL for primary keys
- Ensure foreign keys reference the correct parent tables

**Verify** by running `\dt` in psql to list all tables.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
CREATE TABLE IF NOT EXISTS public.categories
(
    category_id serial NOT NULL,
    name character varying(100) NOT NULL,
    description text,
    CONSTRAINT categories_pkey PRIMARY KEY (category_id),
    CONSTRAINT categories_name_key UNIQUE (name)
);

CREATE TABLE IF NOT EXISTS public.customers
(
    customer_id serial NOT NULL,
    first_name character varying(100) NOT NULL,
    last_name character varying(100) NOT NULL,
    email character varying(255) NOT NULL,
    created_at timestamp with time zone NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT customers_pkey PRIMARY KEY (customer_id),
    CONSTRAINT customers_email_key UNIQUE (email)
);  
CREATE TABLE IF NOT EXISTS public.products
(
    product_id serial NOT NULL,
    name character varying(200) NOT NULL,
    description text,
    price numeric(10,2) NOT NULL,
    stock integer NOT NULL DEFAULT 0,
    created_at timestamp with time zone NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT products_pkey PRIMARY KEY (product_id),
    CONSTRAINT products_price_check CHECK (price > 0::numeric),
    CONSTRAINT products_stock_check CHECK (stock >= 0)
);
>
CREATE TABLE IF NOT EXISTS public.product_categories
(
    product_id integer NOT NULL,
    category_id integer NOT NULL,
    CONSTRAINT product_categories_pkey PRIMARY KEY (product_id, category_id),
    CONSTRAINT product_categories_category_id_fkey FOREIGN KEY (category_id)
        REFERENCES public.categories (category_id)
        ON DELETE CASCADE,
    CONSTRAINT product_categories_product_id_fkey FOREIGN KEY (product_id)
        REFERENCES public.products (product_id)
        ON DELETE CASCADE
);
CREATE TABLE IF NOT EXISTS public.orders
(
    order_id serial NOT NULL,
    customer_id integer NOT NULL,
    order_date timestamp with time zone NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status character varying(20) NOT NULL DEFAULT 'pending',
    CONSTRAINT orders_pkey PRIMARY KEY (order_id),
    CONSTRAINT orders_customer_id_fkey FOREIGN KEY (customer_id)
        REFERENCES public.customers (customer_id)
        ON UPDATE NO ACTION
        ON DELETE NO ACTION,
    CONSTRAINT orders_status_check CHECK (
        status IN ('pending', 'shipped', 'delivered', 'cancelled')
    )
);
CREATE TABLE IF NOT EXISTS public.order_items
(
    order_item_id serial NOT NULL,
    order_id integer NOT NULL,
    product_id integer NOT NULL,
    quantity integer NOT NULL,
    unit_price numeric(10,2) NOT NULL,
    CONSTRAINT order_items_pkey PRIMARY KEY (order_item_id),
    CONSTRAINT order_items_order_id_fkey FOREIGN KEY (order_id)
        REFERENCES public.orders (order_id)
        ON UPDATE NO ACTION
        ON DELETE CASCADE,
    CONSTRAINT order_items_product_id_fkey FOREIGN KEY (product_id)
        REFERENCES public.products (product_id)
        ON UPDATE NO ACTION
        ON DELETE NO ACTION,
    CONSTRAINT order_items_quantity_check CHECK (quantity > 0),
    CONSTRAINT order_items_unit_price_check CHECK (unit_price > 0::numeric)
);
### Task 1.3: Insert Sample Data

Insert the following data:

**Categories** (at least 5):

- Footwear, Backpacks, Tents, Clothing, Accessories

> [!NOTE]
> **_Your SQL_**
>
> ```sql

>
>
> ```

**Customers** (at least 5):

- Use easy to write names with realistic email addresses

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

**Products** (at least 10):

- At least 2 products per category
- At least one product assigned to **two or more** categories
- Prices ranging from €20 to €500
- Various stock levels

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

**Product categories:**

- Insert rows into `product_categories` so every sample product is linked to at least one category

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

**Orders** (at least 5):

- Different customers, different statuses

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

**Order Items** (at least 10):

- Multiple items in some orders, single items in others

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

**Verify** each insert with `SELECT * FROM table_name;`

### Task 1.4: Practice UPDATE

> [!TIP]
> **Recommended practice.** Do Tasks 1.4–1.6. They are not required to finish the TrailShop project. They prepare you for the exams. Task 1.6 renames `stock` to `quantity_in_stock`. Later weeks still use `stock`, so after you practice the rename, change the column name back.

Perform the following updates and verify each one:

1. Increase the price of all products in the Footwear category by 10% (join through `product_categories`)
2. Change customer #3's email to a new address
3. Update the status of order #2 from 'shipped' to 'delivered'
4. Set the stock of 'HydroFlask 1L' to 85
5. Add a description to any product that currently has NULL in description

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your queries here
>
>
> ```

### Task 1.5: Practice DELETE

1. Delete the most recently created order (and observe what happens to its order_items if you used CASCADE)
2. Try to delete a product that appears in `order_items` — what error do you get?
3. Delete a category that has products linked through `product_categories`. The products should remain; only the link rows should disappear. Confirm this.
4. Delete a customer who has no orders

### Task 1.6: Practice ALTER TABLE

1. Add a column `phone VARCHAR(20)` to the customers table
2. Add a column `weight_grams INTEGER` to the products table
3. Add a CHECK constraint to ensure `weight_grams > 0` (allow NULL though — not all products have weight recorded yet)
4. Rename the `stock` column in products to `quantity_in_stock`

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your queries here
>
>
> ```

---

## Exercise 2: Theory Review Questions

Answer the following questions in your own words using the answer fields below:

1. What does SQL stand for, and why was the language designed to look like English?

> [!NOTE]
> **_Your Answer_**
>
SQL stands for Structured Query Language. It was designed to look a bit like English so that it would be easier for people to read and understand. It helps users work with databases without having to use very complicated commands.

2. Explain the difference between DDL and DML. Give two example commands for each.

> [!NOTE]
> **_Your Answer_**
>
DDL is used to create or change the structure of a database. For example, CREATE TABLE and ALTER TABLE are DDL commands. DML is used to work with the data inside the tables. INSERT and UPDATE are examples of DML commands.
3. What is the difference between DCL and TCL? When would you use each?

> [!NOTE]
> **_Your Answer_**
>
DCL is used to control permissions in a database. For example, GRANT gives a user permission and REVOKE removes permission. TCL is used to manage transactions, for example with COMMIT and ROLLBACK. I would use DCL when I need to control who can access something, and TCL when I want to save or undo changes.
4. Why must you create tables in a specific order? What determines that order?

> [!NOTE]
> **_Your Answer_**
>
Tables need to be created in a specific order because some tables depend on other tables. If a table has a foreign key, the table it refers to should normally be created first. So, the relationships between the tables determine the order.

5. What is the difference between a column-level constraint and a table-level constraint? When _must_ you use a table-level constraint?

> [!NOTE]
> **_Your Answer_**
>
A column-level constraint is written with a specific column and applies to that column. A table-level constraint is written separately and can use more than one column. We must use a table-level constraint when the rule depends on two or more columns.

6. Explain the difference between `DELETE FROM products;` and `TRUNCATE TABLE products;`. When would you prefer each?

> [!NOTE]
> **_Your Answer_**
>
DELETE FROM products removes the rows from the table and it can also be used with a WHERE condition to delete selected rows. TRUNCATE removes all rows from the table at once. I would use DELETE when I need more control over which rows to remove, and TRUNCATE when I want to empty the whole table.

7. What does `ON DELETE CASCADE` do on a foreign key? Give a real-world scenario where it's appropriate and one where it would be dangerous.

> [!NOTE]
> **_Your Answer_**
>
ON DELETE CASCADE automatically deletes the related rows when the parent row is deleted. For example, it can be useful for order items when an order is deleted. But it can be dangerous if deleting one important customer also deletes a lot of related data that we wanted to keep.

8. Why should you store `unit_price` in the `order_items` table instead of just looking it up from the `products` table?

> [!NOTE]
> **_Your Answer_**
>
We should store the unit_price in order_items because the product price can change later. The order should keep the price that the customer actually paid at that time. This makes the old order information correct even if the product price changes.

9. What is the difference between SERIAL and GENERATED ALWAYS AS IDENTITY? Which would you use in a new project and why?

> [!NOTE]
> **_Your Answer_**
>
Both SERIAL and GENERATED ALWAYS AS IDENTITY can create automatic numbers for IDs. SERIAL is an older PostgreSQL way of doing this, while IDENTITY is the newer and more standard way. For a new project, I would use GENERATED ALWAYS AS IDENTITY because it is the more modern option.
10. Explain why `UPDATE products SET price = 9.99;` is dangerous. What steps should you take before running any UPDATE statement?

> [!NOTE]
> **_Your Answer_**
>
This UPDATE is dangerous because it changes the price of every product to 9.99. Before running an UPDATE, I should first check the data with a SELECT query and make sure I know which rows I want to change. I should also use a correct WHERE condition and check the result after the update.

---

## Exercise 3: SQL Writing Exercises (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Write the SQL statements for each task in the **Your SQL** fields below. Verify by running them when ready.

### 3.1 CREATE TABLE

Write a CREATE TABLE statement for a `suppliers` table with the following columns:

- supplier_id (auto-incrementing primary key)
- company_name (required, max 200 characters, must be unique)
- contact_name (max 150 characters)
- email (max 255 characters, required, unique)
- phone (max 20 characters)
- country (max 100 characters, required, default 'Finland')

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.2 CREATE TABLE with Foreign Key

Write a CREATE TABLE statement for a `product_reviews` table:

- review_id (auto-incrementing primary key)
- product_id (required, references products)
- customer_id (required, references customers)
- rating (required integer, must be between 1 and 5 inclusive)
- review_text (optional, unlimited length)
- created_at (required, defaults to current timestamp)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.3 INSERT — Single Row

Write an INSERT statement to add a new category called 'Electronics' with description 'GPS devices, solar chargers, and tech gear'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.4 INSERT — Multiple Rows

Write a single INSERT statement that adds three new customers:

- Eero Lahtinen, eero.l@email.com
- Maria Salminen, maria.s@email.com
- Petri Kallio, petri.k@email.com

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.5 INSERT with RETURNING

Write an INSERT statement that adds a new product called 'NorthStar GPS' priced at €229.99 with stock of 12, then assign it to category 'Electronics' (assume `category_id = 6`) using `product_categories`. Return the product_id and created_at.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.6 UPDATE — Simple

Write an UPDATE statement that changes the email of the customer with customer_id = 2 to 'mikko.korhonen@newmail.com'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.7 UPDATE — Expression

Write an UPDATE statement that reduces the stock of all products by 1 where the stock is currently greater than 0.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.8 UPDATE — Multiple Columns

Write an UPDATE statement that changes order #3 to status 'cancelled' and sets a (hypothetical) cancelled_at timestamp to the current time. (Assume you've already added a cancelled_at column.)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.9 DELETE — With Condition

Write a DELETE statement that removes all orders with status 'cancelled'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.10 ALTER TABLE

Write the ALTER TABLE statements to:
a) Add a `discount_percent NUMERIC(5,2) DEFAULT 0 CHECK (discount_percent >= 0 AND discount_percent <= 100)` column to products
b) Drop the `description` column from categories
c) Add a composite unique constraint on (customer_id, product_id) in the product_reviews table (preventing a customer from reviewing the same product twice)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

---

## Exercise 4: Error Diagnosis (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Each of the following SQL statements contains one or more errors. Identify the error(s) and write the corrected version.

### 4.1

```sql
CREATE TABLE warehouses
    warehouse_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    city VARCHAR(100
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.2

```sql
INSERT INTO products (name, price, stock)
VALUES ("Alpine Sleeping Bag", 89.99, 20);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.3

```sql
CREATE TABLE shipments (
    shipment_id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(order_id)
    shipped_date DATE NOT NULL,
    carrier VARCHAR(100)
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.4

```sql
UPDATE products
SET price = price * 0.9
SET stock = stock + 10
WHERE product_id = 3;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.5

```sql
CREATE TABLE wishlists (
    wishlist_id SERIAL PRIMARY KEY,
    customer_id INTEGER NOT NULL REFERENCES customers(customer_id),
    product_id INTEGER NOT NULL REFERENCES products(product_id),
    added_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (customer_id, product_id)
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

---

## Submission Checklist

**Required**

- [ ] All 6 TrailShop tables created successfully
- [ ] Sample data inserted (at least 5 categories, 5 customers, 10 products, product_categories links, 5 orders, 10 order items)
- [ ] Theory review questions answered

**Recommended practice**

- [ ] UPDATE exercises completed and verified
- [ ] DELETE exercises completed and verified
- [ ] ALTER TABLE exercises completed, then `quantity_in_stock` renamed back to `stock`
- [ ] SQL writing exercises completed
- [ ] Error diagnosis completed with corrections
