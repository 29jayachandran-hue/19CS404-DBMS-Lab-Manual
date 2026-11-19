# Experiment 10: PL/SQL – Triggers

## AIM
To write and execute PL/SQL trigger programs for automating actions in response to specific table events like INSERT, UPDATE, or DELETE.

---

## THEORY

A **trigger** is a stored PL/SQL block that is automatically executed or fired when a specified event occurs on a table or view. Triggers can be used for enforcing business rules, auditing changes, or automatic updates.

### Types of Triggers:
- **Before Trigger**: Executes before the operation (INSERT, UPDATE, DELETE).
- **After Trigger**: Executes after the operation.
- **Row-level Trigger**: Executes for each affected row.
- **Statement-level Trigger**: Executes once for the triggering statement.

**Basic Syntax:**
```sql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE|AFTER INSERT|UPDATE|DELETE ON table_name
[FOR EACH ROW]
BEGIN
   -- trigger logic
END;
```

## 1. Write a trigger to log every insertion into a table.
**Steps:**
- Create two tables: `employees` (for storing data) and `employee_log` (for logging the inserts).
- Write an **AFTER INSERT** trigger on the `employees` table to log the new data into the `employee_log` table.

## PROGRAM
```
CREATE TABLE employees (
    emp_id NUMBER,
    emp_name VARCHAR2(30),
    salary NUMBER
);

CREATE TABLE employee_log (
    emp_id NUMBER,
    emp_name VARCHAR2(30),
    salary NUMBER
);

CREATE OR REPLACE TRIGGER log_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_log
    VALUES (:NEW.emp_id, :NEW.emp_name, :NEW.salary);
END;

INSERT INTO employees
VALUES (1, 'Ravi', 5000);

SELECT * FROM employee_log;
```

**Expected Output:**
- A new entry is added to the `employee_log` table each time a new record is inserted into the `employees` table.

## OUTPUT
<img width="790" height="228" alt="image" src="https://github.com/user-attachments/assets/d95546b9-faf0-4dc2-847f-0f07d9020c2b" />



---

## 2. Write a trigger to prevent deletion of records from a sensitive table.
**Steps:**
- Write a **BEFORE DELETE** trigger on the `sensitive_data` table.
- Use `RAISE_APPLICATION_ERROR` to prevent deletion and issue a custom error message.

## PROGRAM
```
CREATE TABLE sensitive_data (
    id NUMBER,
    data VARCHAR2(50)
);

INSERT INTO sensitive_data VALUES (1, 'Password');
INSERT INTO sensitive_data VALUES (2, 'Account Details');

CREATE OR REPLACE TRIGGER prevent_delete
BEFORE DELETE ON sensitive_data
FOR EACH ROW
BEGIN
    RAISE_APPLICATION_ERROR(
        -20001,
        'Deletion not allowed on this table.'
    );
END;

DELETE FROM sensitive_data;

SELECT * FROM sensitive_data;

SELECT TRIGGER_NAME, STATUS
FROM USER_TRIGGERS
WHERE TRIGGER_NAME = 'PREVENT_DELETE';
```

**Expected Output:**
- If an attempt is made to delete a record from `sensitive_data`, an error message is raised, e.g., `ERROR: Deletion not allowed on this table.`

## OUTPUT
<img width="813" height="270" alt="image" src="https://github.com/user-attachments/assets/f06cb1d1-eb96-43b1-984c-56f512ce2fff" />


---

## 3. Write a trigger to automatically update a `last_modified` timestamp.
**Steps:**
- Add a `last_modified` column to the `products` table.
- Write a **BEFORE UPDATE** trigger on the `products` table to set the `last_modified` column to the current timestamp whenever an update occurs.

## PROGRAM
```
CREATE TABLE products (
    product_id NUMBER,
    product_name VARCHAR2(30),
    price NUMBER,
    last_modified TIMESTAMP
);

CREATE OR REPLACE TRIGGER update_time
BEFORE UPDATE ON products
FOR EACH ROW
BEGIN
    :NEW.last_modified := SYSTIMESTAMP;
END;

INSERT INTO products
VALUES (1, 'Laptop', 50000, SYSTIMESTAMP);

UPDATE products
SET price = 55000
WHERE product_id = 1;

SELECT * FROM products;
```

**Expected Output:**
- The `last_modified` column in the `products` table is updated automatically to the current date and time when any record is updated.

## OUTPUT
<img width="907" height="265" alt="image" src="https://github.com/user-attachments/assets/c45aeb2f-7134-4165-bb85-f6d8bdbb9cda" />



---

## 4. Write a trigger to keep track of the number of updates made to a table.
**Steps:**
- Create an `audit_log` table with a counter column.
- Write an **AFTER UPDATE** trigger on the `customer_orders` table to increment the counter in the `audit_log` table every time a record is updated.

## PROGRAM
```
CREATE TABLE customer_orders (
    order_id NUMBER,
    amount NUMBER
);

CREATE TABLE audit_log (
    update_count NUMBER
);

INSERT INTO audit_log VALUES (0);

CREATE OR REPLACE TRIGGER count_updates
AFTER UPDATE ON customer_orders
FOR EACH ROW
BEGIN
    UPDATE audit_log
    SET update_count = update_count + 1;
END;

INSERT INTO customer_orders
VALUES (1, 5000);

UPDATE customer_orders
SET amount = 6000
WHERE order_id = 1;

SELECT * FROM audit_log;
```

**Expected Output:**
- The `audit_log` table will maintain a count of how many updates have been made to the `customer_orders` table.

## OUTPUT
<img width="780" height="247" alt="image" src="https://github.com/user-attachments/assets/8408ea9b-ce70-47a7-99ab-e7f613c4b9c6" />



---

## 5. Write a trigger that checks a condition before allowing insertion into a table.
**Steps:**
- Write a **BEFORE INSERT** trigger on the `employees` table to check if the inserted salary meets a specific condition (e.g., salary must be greater than 3000).
- If the condition is not met, raise an error to prevent the insert.

## PROGRAM
```
CREATE OR REPLACE TRIGGER check_salary
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    IF :NEW.salary < 3000 THEN
        RAISE_APPLICATION_ERROR(-20002,
        'Salary below minimum threshold.');
    END IF;
END;

INSERT INTO employees
VALUES (2, 'Arun', 5000);

INSERT INTO employees
VALUES (3, 'Kumar', 2000);

SELECT * FROM employees;

SELECT TRIGGER_NAME, STATUS
FROM USER_TRIGGERS
WHERE TRIGGER_NAME = 'CHECK_SALARY';
```

**Expected Output:**
- If the inserted salary in the `employees` table is below the condition (e.g., salary < 3000), the insert operation is blocked, and an error message is raised, such as: `ERROR: Salary below minimum threshold.`

## OUTPUT
<img width="832" height="270" alt="image" src="https://github.com/user-attachments/assets/50be8268-8c26-410f-ab8f-bc6f6c7d46fe" />


## RESULT
Thus, the PL/SQL trigger programs were written and executed successfully.
