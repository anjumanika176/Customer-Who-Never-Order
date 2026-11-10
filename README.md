# Customer-Who-Never-Order
Solution in Left Join(MySQL)
# LeetCode 183: Customers Who Never Order

## Problem
Given a `Customers` table and an `Orders` table, find all customers who never placed an order.

**Table: `Customers`**

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

**Table: `Orders`**

| Column Name | Type |
|-------------|------|
| id          | int  |
| customerId  | int  |

- `id` is the primary key in both tables.
- `customerId` is a foreign key referencing `Customers.id`.
- Return the result in any order, with the column name `Customers`.

## Example

**Customers**

| id | name  |
|----|-------|
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |

**Orders**

| id | customerId |
|----|------------|
| 1  | 3          |
| 2  | 1          |

**Output**

| Customers |
|-----------|
| Henry     |
| Max       |

Joe (id 1) and Sam (id 3) have orders. Henry and Max do not.

## Approach
Use a `LEFT JOIN` from `Customers` to `Orders`. A `LEFT JOIN` keeps **every** row from the left table.
When a customer has no matching order, the columns from `Orders` are `NULL`.
Filtering with `WHERE o.id IS NULL` keeps only the customers with no orders.

### Walkthrough

Result of the `LEFT JOIN`:

| c.id | c.name | o.id | o.customerId |
|------|--------|------|--------------|
| 1    | Joe    | 2    | 1            |
| 2    | Henry  | NULL | NULL         |
| 3    | Sam    | 1    | 3            |
| 4    | Max    | NULL | NULL         |

`WHERE o.id IS NULL` removes Joe and Sam, leaving Henry and Max.

### JOIN vs LEFT JOIN

| Join | Behavior | Customer with no order |
|------|----------|------------------------|
| `JOIN` (INNER) | Keeps only matching rows | Row is dropped |
| `LEFT JOIN` | Keeps all rows from the left table | Right-side columns are `NULL` |

An inner `JOIN` cannot be used here, because customers with no orders would never appear.

## Solution (MySQL)

```sql
SELECT c.name AS Customers
FROM Customers c
LEFT JOIN Orders o
  ON c.id = o.customerId
WHERE o.id IS NULL;
```

## Alternative: NOT IN

```sql
SELECT name AS Customers
FROM Customers
WHERE id NOT IN (
  SELECT customerId
  FROM Orders
);
```

This returns customers whose `id` does not appear in the `customerId` list of `Orders`.
It works here because `customerId` has no `NULL` values. If the subquery returned a `NULL`, `NOT IN` would return no rows,
so `LEFT JOIN ... IS NULL` is the safer pattern.

## Concepts Used
- `LEFT JOIN`
- `IS NULL` check (`= NULL` does not work)
- Table aliases
- Subquery with `NOT IN` (alternative)

## Language
MySQL

## Link
https://leetcode.com/problems/customers-who-never-order
