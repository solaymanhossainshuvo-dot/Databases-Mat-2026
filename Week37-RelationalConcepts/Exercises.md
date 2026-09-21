# Week 37 — Exercises & Project Task

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 37 Theory material. Complete all sections.

---

## Part 1: TrailShop Project Task

### Task 1: Identify Keys

Using the `products`, `categories`, and `customers` tables shown in Section 2 of this week's Theory material, answer:

1. What is the primary key of the `products` table? Why is it a good choice?

> [!NOTE]
> ***Your Answer***
>
>The primary key of the products table is product_id. It is a good choice because every product can have its own unique ID. It also stays the same even if the product name or other information changes.
>
>
>
>


2. What is the primary key of the `categories` table?

> [!NOTE]
> ***Your Answer***
>
> The primary key of the categories table is category_id. It gives each category a unique ID, so it is easy to identify and connect a category with other tables.
>
>
>
>


3. What is the foreign key in the `products` table? What does it reference?

> [!NOTE]
> ***Your Answer***
>
>The foreign key in the products table is category_id. It references the category_id in the categories table. This makes sure that a product is connected to an existing category.
>
>
>
>


4. Is `name` in `products` a candidate key? Under what assumption? What would make it unsuitable as a primary key?


> [!NOTE]
> ***Your Answer***
>
> The name column could be a candidate key if every product has a unique name and the name can never be NULL. However, it would not be a good primary key if two products could have the same name or if the name could change later.
>
>
>
>
5. Give an example of a **superkey** for the `products` table that is NOT a candidate key. Explain why it's not minimal.

> [!NOTE]
> ***Your Answer***
>
> (product_id, name) is an example of a superkey. It can uniquely identify a product, but it is not a candidate key because product_id alone is already enough to identify the product. The name column is therefore unnecessary.
>
>
>
>

6. Give an example of a **composite key** using a hypothetical `order_items` table. Explain why neither column alone would be sufficient.

> [!NOTE]
> ***Your Answer***
>
> A good example is (order_id, product_id). An order can contain several different products, so order_id alone is not enough. Also, the same product can be included in many different orders, so product_id alone is not enough. Together, they identify a specific product in a specific order.
>
>
>
>

7. Is `email` in `customers` a candidate key? What makes it different from `customer_id` as a PK choice? *(See Section 6.9 on natural vs surrogate keys.)*

> [!NOTE]
> ***Your Answer***
>
> Email can be a candidate key if every customer has a different email address and the email is not NULL. However, customer_id is a better choice for the primary key because it is a simple ID and does not depend on customer information. A customer can change their email, but their customer_id can stay the same.
>
>
>
>

### Task 2: Define Business Rules

List **5 business rules** for TrailShop. For each rule, specify:
- The rule in plain English
- Which constraint type(s) would enforce it
- Which table and column the constraint applies to
- The SQL syntax for the constraint

Example:

| Business Rule | Constraint Type | Table.Column | SQL |
|---|---|---|---|
| Every product must have a price greater than zero | CHECK | products.price | `CHECK (price > 0)` |
| ... | ... | ... | ... |

Think about rules for customers, orders, and categories — not just products.

> [!NOTE]
> ***Your Answer***
>
> | Business Rule | Constraint Type | Table.Column | SQL |
| --- | --- | --- | --- |
| Every product must have a name. | NOT NULL | products.name | `name VARCHAR(100) NOT NULL` |
| The price of a product must be greater than zero. | CHECK | products.price | `CHECK (price > 0)` |
| Every customer must have a unique email address. | UNIQUE | customers.email | `UNIQUE (email)` |
| Every product must belong to an existing category. | NOT NULL + FOREIGN KEY | products.category_id | `category_id INTEGER NOT NULL REFERENCES categories(category_id)` |
| Every order must belong to an existing customer. | NOT NULL + FOREIGN KEY | orders.customer_id | `customer_id INTEGER NOT NULL REFERENCES customers(customer_id)` |
>
>
>
>

### Task 3: Integrity Violations

For each SQL statement below, predict whether it will **succeed** or **fail**. If it fails, explain which integrity rule or constraint is violated and what error message you'd expect. Assume the schema from Section 9.8 of the Theory material.

```sql
-- Statement A
INSERT INTO categories (category_id, category_name)
VALUES (NULL, 'Cycling');

-- Statement B
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (109, 'AeroLite Tent', 279.00, 10, 2);

-- Statement C
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (110, 'BudgetBoots', -5.00, 25, 1);

-- Statement D
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (103, 'Duplicate Shoes', 99.99, 5, 3);

-- Statement E
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (111, 'CloudWalker Sandals', 65.00, 40, 10);

-- Statement F
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (112, NULL, 89.99, 20, 1);

-- Statement G
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (113, 'LightStep Shoes', 149.00, -3, 1);

-- Statement H
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (1001, 101, 0, 189.50);
```

> [!NOTE]
> ***Your Answer***
>
> A. FAIL — category_id is the primary key of the categories table, so it cannot be NULL. This violates the NOT NULL requirement of the primary key.

B. SUCCESS — The category_id 2 exists in the categories table, the price is positive, the stock quantity is not negative, and the product_id is new.

C. FAIL — The price is -5.00, but the products table has a CHECK constraint that requires price to be greater than 0.

D. FAIL — Product_id 103 already exists, so this insert violates the primary key uniqueness constraint.

E. FAIL — Category_id 10 does not exist in the categories table. This violates the foreign key constraint on products.category_id.

F. FAIL — The name column in products is NOT NULL, so the NULL value violates the NOT NULL constraint.

G. FAIL — The stock quantity is -3, but the products table has a CHECK constraint that requires stock_quantity to be 0 or greater.

H. FAIL — The quantity is 0, but the order_items table has a CHECK constraint requiring quantity to be greater than 0.
>
>
>
>

### Task 4: Foreign Key Actions

Consider the following scenario using the schema from Theory Section 9.8:

1. You want to delete category 2 ("Camping") from the `categories` table. Products 102 and 106 reference this category. What happens with:
   - `ON DELETE RESTRICT`?
   - `ON DELETE CASCADE`?
   - `ON DELETE SET NULL`? (Assume `category_id` in `products` allows NULL for this question)

2. Which foreign key action would you recommend for the TrailShop `products.category_id` → `categories.category_id` relationship? Justify your choice in 2–3 sentences.

> [!NOTE]
> ***Your Answer***
>
> *1. If we try to delete category 2 (Camping):

ON DELETE RESTRICT:
The delete will fail because products 102 and 106 are still using category 2. The database will prevent the category from being deleted and give a foreign key constraint error.

ON DELETE CASCADE:
The category will be deleted, and products 102 and 106 will also be deleted automatically because they reference category 2.

ON DELETE SET NULL:
The category will be deleted, but products 102 and 106 will stay in the database. Their category_id will be changed to NULL. This only works if category_id allows NULL values.

2. I would use ON DELETE RESTRICT for the TrailShop products.category_id relationship. It prevents a category from being deleted accidentally while products are still using it. This is safer because deleting a category should not automatically delete the products that belong to it.
>
>
>
>




---

## Part 2: Theory Review Questions

Answer each question in 2–4 sentences unless otherwise specified. Reference the Theory material sections as needed.

### Short-Answer Questions

**Q1.** Define the following terms in your own words: relation, tuple, attribute, domain. Give one TrailShop example for each.

> [!NOTE]
> ***Your Answer***
>
> A relation is basically a table in a database. For example, the products table is a relation in TrailShop. A tuple is one row, such as the row for product 101. An attribute is a column, such as price, and a domain is the set of valid values for an attribute, such as positive numbers for price.
>
>
>
>

*(See Sections 2 and 3 of this week's Theory material.)*

**Q2.** What makes a candidate key different from a primary key? Can a table have more than one candidate key?


> [!NOTE]
> ***Your Answer***
>
> A candidate key is a column or group of columns that can uniquely identify a row and is also minimal. A primary key is the candidate key that we choose to be the main identifier for the table. Yes, a table can have more than one candidate key, but only one of them is selected as the primary key.
>
>
>
>

*(See Section 6 of this week's Theory material.)*

**Q3.** Explain entity integrity in your own words. Why can't a primary key be NULL?


> [!NOTE]
> ***Your Answer***
>
>Entity integrity means that every table should have a primary key and the primary key cannot be NULL. A primary key cannot be NULL because every row needs a clear and unique identity. Otherwise, it would be difficult to identify or reference that row correctly.
>
>
>
>

*(See Section 8.1 of this week's Theory material.)*

**Q4.** What happens when referential integrity is violated? Give a concrete TrailShop example — show the SQL statement and the expected error.

> [!NOTE]
> ***Your Answer***
>
> Referential integrity means that a foreign key must refer to an existing primary key, unless NULL is allowed. For example, this would fail if category 99 does not exist:

INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (109, 'Ghost Product', 59.99, 5, 99);

The expected error is a foreign key constraint violation because category_id 99 does not exist in the categories table.
>
>
>
>

*(See Section 8.2 of this week's Theory material.)*

**Q5.** Explain the difference between a surrogate key and a natural key. Give an example of each for a `books` table in a library database.

> [!NOTE]
> ***Your Answer***
>
> A surrogate key is an ID created by the database and does not have any real-world meaning. A natural key comes from real-world data and has a meaning outside the database. For a books table, book_id could be a surrogate key, while ISBN could be a natural key because it identifies a real book.
>
>
>
>

*(See Section 6.8–6.9 of this week's Theory material.)*

**Q6.** What is a NULL value? Why is `WHERE price = NULL` wrong? What should you write instead?


> [!NOTE]
> ***Your Answer***
>
> NULL means that a value is unknown or not applicable. We should not use WHERE price = NULL because comparing something with NULL does not return TRUE. Instead, we should use WHERE price IS NULL to find rows where price has no value.
>
>
>
>

*(See Section 7 of this week's Theory material.)*

**Q7.** What is a junction table? When is it needed? Give an example.

> [!NOTE]
> ***Your Answer***
>
> A junction table is used to connect two tables that have a many-to-many relationship. It normally contains foreign keys from both tables. For example, order_items can connect orders and products because one order can contain many products and the same product can appear in many orders.
>
>
>
>

*(See Section 12.3 of this week's Theory material.)*

**Q8.** Describe the three types of relationships (1:1, 1:N, M:N). For each, give one TrailShop example.

> [!NOTE]
> ***Your Answer***
>
>A 1:1 relationship means one row is connected to one row, such as a product and its product_details. A 1:N relationship means one row can be connected to many rows, such as one category having many products. An M:N relationship means many rows can be connected to many rows, such as products and tags, which need a junction table.
>
>
>
>

*(See Section 12 of this week's Theory material.)*

**Q9.** What is the difference between `ON DELETE CASCADE` and `ON DELETE RESTRICT`? When would you use each?


> [!NOTE]
> ***Your Answer***
>
>ON DELETE CASCADE deletes the related rows automatically when the referenced row is deleted. ON DELETE RESTRICT prevents the deletion if related rows still exist. I would use RESTRICT when I want to protect related data, and CASCADE when deleting the parent should also remove the dependent data.
>
>
>
>

*(See Section 10 of this week's Theory material.)*

**Q10.** Explain what "atomic entries" means in the context of relation properties. Give an example of a violation.

> [!NOTE]
> ***Your Answer***
>
> Atomic entries means that each cell should contain only one value, not a list of values. For example, storing "Footwear, Hiking" in one categories cell would not be atomic. The values should be stored separately, using another table or a junction table if necessary.
>
>
>
>

*(See Section 5.3 of this week's Theory material.)*

### True/False

For each statement, write **True** or **False** and correct any false statements.

1. False — A superkey is not always a candidate key. A candidate key must also be minimal.

2. True — A primary key can consist of more than one column. This is called a composite primary key.

3. False — NULL = NULL does not evaluate to TRUE in SQL. It evaluates to UNKNOWN. To check for NULL, we use IS NULL.

4. False — A foreign key does not always have to be NOT NULL. It can be NULL if the relationship is optional and the column allows NULL values.

5. True — Referential integrity ensures that a foreign key value matches an existing primary key value, or is NULL when NULL is allowed.

6. False — The degree of a relation is the number of columns. The number of rows is called the cardinality.
### Matching Exercise

Match each term (1–12) with its definition (A–L).

| # | Term |
|---|---|
| 1 | Superkey |
| 2 | Candidate key |
| 3 | Composite key |
| 4 | Foreign key |
| 5 | Alternate key |
| 6 | Surrogate key |
| 7 | Natural key |
| 8 | Orphan record |
| 9 | Domain |
| 10 | Junction table |
| 11 | Cardinality |
| 12 | COALESCE |

| Letter | Definition |
|---|---|
| A | The set of all permitted values for an attribute |
| B | A key composed of two or more attributes |
| C | A row whose FK references a non-existent PK — forbidden by referential integrity |
| D | An artificial key with no business meaning (e.g., auto-generated ID) |
| E | A candidate key not chosen as the primary key |
| F | Any set of attributes that uniquely identifies every tuple |
| G | A minimal superkey — no attribute can be removed without losing uniqueness |
| H | A column that references the primary key of another table |
| I | The number of tuples (rows) in a relation |
| J | A key drawn from real-world data with business meaning |
| K | A table implementing a many-to-many relationship |
| L | A SQL function that returns the first non-NULL argument |


> [!NOTE]
> ***Your Answers***
>
> | # | Your Match |
|---|---|
| 1 | F |
| 2 | G |
| 3 | B |
| 4 | H |
| 5 | E |
| 6 | D |
| 7 | J |
| 8 | C |
| 9 | A |
| 10 | K |
| 11 | I |
| 12 | L |
>

---

## Part 3: SQL Practice — Constraints in Action

These exercises test your understanding of constraints. You do NOT need to run these in PostgreSQL (but you may if you'd like to verify your answers).

### Exercise 3.1: Predict the Outcome

Given the following table definitions:

```sql
CREATE TABLE departments (
    dept_id   INTEGER      PRIMARY KEY,
    dept_name VARCHAR(50)  NOT NULL UNIQUE
);

CREATE TABLE employees (
    emp_id    INTEGER       PRIMARY KEY,
    name      VARCHAR(100)  NOT NULL,
    salary    NUMERIC(10,2) NOT NULL CHECK (salary >= 0),
    dept_id   INTEGER       NOT NULL REFERENCES departments(dept_id)
);
```

Assume these rows already exist:

```sql
INSERT INTO departments VALUES (1, 'Engineering');
INSERT INTO departments VALUES (2, 'Marketing');
INSERT INTO employees VALUES (100, 'Alice', 75000, 1);
INSERT INTO employees VALUES (101, 'Bob', 65000, 2);
```

For each statement below, predict: **SUCCESS** or **FAIL**? If fail, name the violated constraint.

```sql
-- 1
INSERT INTO employees VALUES (102, 'Carol', 70000, 1);

-- 2
INSERT INTO employees VALUES (103, 'Dan', -5000, 1);

-- 3
INSERT INTO employees VALUES (100, 'Eve', 80000, 2);

-- 4
INSERT INTO employees VALUES (104, 'Frank', 60000, 5);

-- 5
INSERT INTO departments VALUES (3, 'Engineering');

-- 6
INSERT INTO employees VALUES (105, NULL, 55000, 2);

-- 7
DELETE FROM departments WHERE dept_id = 1;

-- 8
INSERT INTO employees VALUES (106, 'Grace', 0, 2);
```
### Your Answers

1. SUCCESS — The employee has a new emp_id, the name is not NULL, the salary is positive, and department 1 exists.

2. FAIL — The salary is -5000, which violates the CHECK constraint that salary must be greater than or equal to 0.

3. FAIL — The emp_id 100 already exists, so this violates the PRIMARY KEY constraint.

4. FAIL — Department 5 does not exist, so this violates the FOREIGN KEY constraint on dept_id.

5. FAIL — The department name 'Engineering' already exists, so this violates the UNIQUE constraint on dept_name.

6. FAIL — The employee name is NULL, but the name column has a NOT NULL constraint.

7. FAIL — Department 1 is still referenced by employee Alice, so the department cannot be deleted because of the FOREIGN KEY constraint.

8. SUCCESS — The salary is 0, which satisfies the CHECK constraint salary >= 0, and department 2 exists.

### Exercise 3.2: Write the Constraints

Given these business rules for a **bookstore database**, write the `CREATE TABLE` statements with appropriate constraints:

1. Every book has a unique ISBN (13 characters), a title (required), a price (must be positive), and a publication year.
2. Every author has an ID, a first name (required), and a last name (required).
3. A book can have multiple authors, and an author can write multiple books.
4. Every book belongs to exactly one genre. Genres have an ID and a unique name.
5. Publication year must be between 1450 and the current year.

*(Hint: you'll need at least 4 tables, including a junction table for the M:N relationship.)*

---### Exercise 3.2 — My Answer

CREATE TABLE genres (
    genre_id INTEGER PRIMARY KEY,
    genre_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE authors (
    author_id INTEGER PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL
);

CREATE TABLE books (
    isbn CHAR(13) PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    price NUMERIC(10,2) NOT NULL CHECK (price > 0),
    publication_year INTEGER NOT NULL
        CHECK (publication_year BETWEEN 1450 AND EXTRACT(YEAR FROM CURRENT_DATE)),
    genre_id INTEGER NOT NULL
        REFERENCES genres(genre_id)
);

CREATE TABLE book_authors (
    isbn CHAR(13) NOT NULL REFERENCES books(isbn),
    author_id INTEGER NOT NULL REFERENCES authors(author_id),
    PRIMARY KEY (isbn, author_id)
);

## Part 4: Design Exercise — Library System

A small public library needs a database. Here is a description of their requirements:

> The library has a collection of **books**. Each book has an ISBN, a title, a publication year, and belongs to one genre (Fiction, Non-Fiction, Science, History, etc.). The library may own multiple **copies** of the same book — each copy has a unique barcode sticker.
>
> The library has registered **members**. Each member has a member number, name, email, and phone. Members can **borrow** copies. Each borrowing records which member borrowed which copy, the borrow date, the due date, and the return date (NULL if not yet returned).
>
> **Rules:**
> - A member can borrow at most 5 copies at any given time.
> - The due date is always 14 days after the borrow date.
> - A copy cannot be borrowed if it's currently not returned (return_date IS NULL).

### Your Tasks

1. **Identify the tables** you would need (list them with their columns).
2. **Identify the primary key** for each table. Are they surrogate or natural keys? Justify your choices.
3. **Identify all foreign keys** and the tables they reference.
4. **Identify any candidate keys** beyond the primary key (alternate keys).
5. **List the business rules** from the description and map each to a constraint type. Which rules cannot be enforced by simple constraints?


> [!NOTE]
> ***Your Answer***
>
> ### 1. Tables and columns

I would use the following tables:

- genres: genre_id, genre_name
- books: isbn, title, publication_year, genre_id
- copies: barcode, isbn
- members: member_number, name, email, phone
- borrowings: borrowing_id, member_number, barcode, borrow_date, due_date, return_date

The books table stores information about the different books. The copies table stores the physical copies of each book. The borrowings table records when a member borrows a specific copy.


### 2. Primary keys

- genres: genre_id — surrogate key
- books: isbn — natural key
- copies: barcode — natural key
- members: member_number — natural key
- borrowings: borrowing_id — surrogate key

I would use genre_id as a surrogate key because the genre name is not a good unique identifier. ISBN is a natural key because it identifies a real book. Barcode is a natural key because each physical copy has its own barcode. Member_number is the identifier given to a library member. Borrowing_id is a surrogate key because it gives each borrowing transaction a simple unique ID.


### 3. Foreign keys

- books.genre_id references genres.genre_id
- copies.isbn references books.isbn
- borrowings.member_number references members.member_number
- borrowings.barcode references copies.barcode

These foreign keys make sure that books, copies, members and borrowings are connected to existing records.


### 4. Candidate keys / alternate keys

The members.email column could be an alternate key if every member must have a unique email address. In that case, it can have a UNIQUE constraint.

The books.isbn and copies.barcode are already primary keys, so they are not alternate keys. Other columns such as book title or member name should not be candidate keys because they do not have to be unique.


### 5. Business rules and constraints

1. Every book must have a unique ISBN.
   - Constraint: PRIMARY KEY
   - Table: books.isbn

2. Every book must belong to one genre.
   - Constraint: NOT NULL + FOREIGN KEY
   - Table: books.genre_id

3. Every physical copy must have a unique barcode.
   - Constraint: PRIMARY KEY
   - Table: copies.barcode

4. A member can borrow at most 5 copies at one time.
   - This needs more than a simple CHECK constraint because we need to count the member's active borrowings. A trigger or application logic would be needed.

5. The due date must be exactly 14 days after the borrow date.
   - Constraint: CHECK
   - Example: CHECK (due_date = borrow_date + 14)

6. A copy cannot be borrowed when it already has an active borrowing.
   - This cannot be handled by a simple CHECK constraint because it requires checking other rows. A partial unique index or trigger can be used to enforce it.

7. The return_date can be NULL when the book has not been returned yet.
   - Constraint: NULL is allowed for return_date.


### 6. CREATE TABLE statements

```sql
CREATE TABLE genres (
    genre_id INTEGER PRIMARY KEY,
    genre_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE books (
    isbn CHAR(13) PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    publication_year INTEGER NOT NULL,
    genre_id INTEGER NOT NULL,
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id),
    CHECK (
        publication_year BETWEEN 1450
        AND EXTRACT(YEAR FROM CURRENT_DATE)
    )
);

CREATE TABLE copies (
    barcode VARCHAR(50) PRIMARY KEY,
    isbn CHAR(13) NOT NULL,
    FOREIGN KEY (isbn) REFERENCES books(isbn)
);

CREATE TABLE members (
    member_number INTEGER PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(30) NOT NULL
);

CREATE TABLE borrowings (
    borrowing_id INTEGER PRIMARY KEY,
    member_number INTEGER NOT NULL,
    barcode VARCHAR(50) NOT NULL,
    borrow_date DATE NOT NULL,
    due_date DATE NOT NULL,
    return_date DATE NULL,
    FOREIGN KEY (member_number) REFERENCES members(member_number),
    FOREIGN KEY (barcode) REFERENCES copies(barcode),
    CHECK (due_date = borrow_date + 14)
);
 CREATE UNIQUE INDEX one_active_borrowing_per_copy
ON borrowings (barcode)
WHERE return_date IS NULL;
>
>
>
>
6. **Write the CREATE TABLE statements** for at least the `books`, `copies`, and `borrowings` tables with full constraints.

---

## Submission Checklist

- [x] Task 1: Key identification answers (Part 1)
- [x] Task 2: Business rules table with 5 rules (Part 1)
- [x] Task 3: Integrity violation predictions with explanations (Part 1)
- [x] Task 4: Foreign key action analysis (Part 1)
- [x] Theory Review Questions answered (Part 2)
- [x] SQL Practice — constraint predictions and bookstore CREATE TABLE (Part 3)
- [x] Library System design exercise (Part 4)
