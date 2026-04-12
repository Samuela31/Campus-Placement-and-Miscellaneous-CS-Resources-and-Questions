# 🧠 The Core Idea (THIS will clear 70% confusion)

### 👉 `WHERE` vs `HAVING`

| Clause   | Filters                         | When it runs     |
| -------- | ------------------------------- | ---------------- |
| `WHERE`  | **rows (individual records)**   | BEFORE grouping  |
| `HAVING` | **groups (aggregated results)** | AFTER `GROUP BY` |

---

### 🔑 Golden Rule

* If condition uses **normal columns → `WHERE`**
* If condition uses **aggregate functions (`COUNT`, `SUM`, etc.) → `HAVING`**

---

## 🧩 Mental Model (Very Important)

Think of SQL execution like this:

```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

So:

* `WHERE` = filter raw data
* `GROUP BY` = create groups
* `HAVING` = filter those groups

---

# 🔥 Pattern-Based Approach (What you actually asked for)

Instead of thinking randomly, train your brain with **patterns** 👇

---

# 🟢 Pattern 1: Simple Filtering

### ❓ Question type:

"Get employees with salary > 50k"

### ✅ Template:

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

👉 No grouping → no `HAVING`

---

# 🟡 Pattern 2: Aggregation ONLY (no filtering on aggregate)

### ❓ Question:

"Find total salary per department"

### ✅ Template:

```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY department;
```

👉 No filtering → no `HAVING`

---

# 🔴 Pattern 3: Aggregation + Filter on Aggregate (MOST IMPORTANT)

### ❓ Question:

"Find departments where total salary > 1 lakh"

### ✅ Template:

```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY department
HAVING SUM(salary) > 100000;
```

👉 🚨 This is where people wrongly use `WHERE`

---

# 🔵 Pattern 4: Row Filter + Group Filter (Combination)

### ❓ Question:

"Find departments with more than 3 employees whose salary > 50k"

### ✅ Template:

```sql
SELECT department, COUNT(*)
FROM employees
WHERE salary > 50000   -- row filter first
GROUP BY department
HAVING COUNT(*) > 3;   -- group filter
```

👉 BOTH `WHERE` and `HAVING`

---

# 🟣 Pattern 5: Duplicates Detection (YOU mentioned this!)

### ❓ Question:

"Find duplicate emails"

### 🚫 Wrong instinct:

Using subquery

### ✅ Correct pattern:

```sql
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

👉 🔥 KEY IDEA:
Duplicates = **group + count > 1**

---

# 🟠 Pattern 6: Find records based on group condition (Common trap)

### ❓ Question:

"Find employees who belong to departments with more than 3 people"

### 🚫 Your current approach:

Subquery in `WHERE`

### ✅ Better approach:

```sql
SELECT *
FROM employees
WHERE department IN (
    SELECT department
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 3
);
```

👉 Here subquery is OK, because:

* Outer query = rows
* Inner query = group condition

---

# 🧠 How to Decide (Step-by-step thinking)

When you see a question:

### Step 1: Ask

👉 "Am I grouping data?"

* NO → just use `WHERE`
* YES → go next

---

### Step 2: Ask

👉 "Is condition on aggregated value?"

* YES → use `HAVING`
* NO → use `WHERE`

---

### Step 3: Ask

👉 "Do I need BOTH?"

* Filter rows first → `WHERE`
* Then filter groups → `HAVING`

---

# 🚨 Why you get stuck (honest reason)

You're probably:

* Jumping to subqueries too early
* Not identifying **"is this row-level or group-level?"**

---

# 💡 Quick Cheatsheet (Save this mentally)

| Situation                                 | Use                              |
| ----------------------------------------- | -------------------------------- |
| Filter rows                               | `WHERE`                          |
| Group data                                | `GROUP BY`                       |
| Filter aggregated results                 | `HAVING`                         |
| Find duplicates                           | `GROUP BY + HAVING COUNT(*) > 1` |
| Both row + group filtering                | `WHERE + HAVING`                 |
| Need original rows from grouped condition | Subquery                         |

---

# 🧪 Practice Thought Example

👉 Question:
"Find departments where average salary > 60k"

Your brain should go:

* "average" → aggregation
* "per department" → grouping
* "condition on average" → HAVING

✅ Final:

```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;
```

