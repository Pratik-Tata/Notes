**DBMS Study Guide: Keys, Normalization & Fundamentals**

---

# 🌐 Introduction to DBMS

**DBMS (Database Management System)** is software for creating, managing, and manipulating databases. It allows data to be stored in a structured way and provides tools for insertion, updating, deletion, and querying.

---

# 🔑 Types of Keys

## 1. **Primary Key**

- Uniquely identifies each row in a table
    
- Cannot have NULL values
    
- One per table
    
- Example: `student_id` in `Students`
    

## 2. **Candidate Key**

- A set of attributes that can uniquely identify rows
    
- A table can have multiple candidate keys
    
- One candidate key is chosen as the primary key
    

## 3. **Alternate Key**

- The remaining candidate keys that are not selected as the primary key
    

## 4. **Foreign Key**

- A field in one table that refers to the **primary key** in another table
    
- Establishes a relationship between tables
    
- Can have duplicate and NULL values
    

## 5. **Composite Key**

- A primary key made up of **two or more columns**
    
- Used when no single column can uniquely identify a row
    

## 6. **Super Key**

- Any combination of fields that uniquely identifies a record
    
- Superset of candidate keys
    

---

# 📁 Normalization

Normalization is the process of organizing data to reduce redundancy and improve data integrity.

## Goals:

- Eliminate redundant data
    
- Ensure data dependencies make sense
    

---

# 🏋️ Forms of Normalization

## 1. **1NF (First Normal Form)**

- Atomic values (no multivalued or composite attributes)
    
- Each column must have unique name
    
- Order does not matter
    

## 2. **2NF (Second Normal Form)**

- Must satisfy 1NF
    
- No **partial dependency** (non-key attributes fully dependent on the entire primary key)
    
- Applies only when there is a composite key
    

## 3. **3NF (Third Normal Form)**

- Must satisfy 2NF
    
- No **transitive dependency** (non-key attributes should not depend on other non-key attributes)
    

## 4. **BCNF (Boyce-Codd Normal Form)**

- Stricter version of 3NF
    
- Every **determinant** must be a candidate key
    

## 5. **4NF (Fourth Normal Form)**

- No multi-valued dependencies
    
- Eliminates cases where one attribute in a table uniquely determines another set of attributes
    

## 6. **5NF (Fifth Normal Form)**

- Deals with join dependency
    
- Ensures lossless join decomposition
    

---

# ✏️ Dependencies in DBMS

## Partial Dependency

- When a non-prime attribute depends only on a part of a composite key
    
- Violates 2NF
    

## Transitive Dependency

- When a non-prime attribute depends on another non-prime attribute
    
- Violates 3NF
    

## Functional Dependency

- Attribute A is functionally dependent on B if B uniquely determines A (B ➞ A)
    

## Multivalued Dependency

- When one attribute in a table uniquely determines multiple values of another attribute
    
- Handled in 4NF
    

## Join Dependency

- A complex type of dependency handled in 5NF
    
- Occurs when a relation is decomposed and can be reconstructed with a join
    

---

# ✏️ How to Normalize a Table

### Step 1: 1NF

- Split repeating groups into new rows
    
- Ensure each cell holds a single value
    

### Step 2: 2NF

- If the primary key is composite, move partial dependencies to a new table
    

### Step 3: 3NF

- Eliminate transitive dependencies
    
- Move them into separate tables
    

---

# 🔎 Identifying Violations

|Normal Form|Violation Example|Solution|
|---|---|---|
|1NF|A column contains list of phone numbers|Split into multiple rows|
|2NF|In composite key, non-key depends on part of the key|Move to a separate table|
|3NF|Column depends on another non-key column (e.g., city depends on zipcode)|Move dependent columns to new table|
|BCNF|A non-candidate key determines another column (violates uniqueness of determinant)|Reorganize schema with new relationships|
|4NF|One attribute determines multiple independent attributes|Move to separate table|
|5NF|Join dependency causing data loss during decomposition|Decompose and ensure lossless join|

---

# 📈 Bonus: Denormalization

- Combining tables to reduce joins and improve read performance
    
- Often used in analytics and big data systems
    
- Comes at the cost of data redundancy


### 🔑 **Prime Attribute**

A **prime attribute** is:

> 👉 Any attribute that is **part of a candidate key** (or primary key).

**Example:**

If a table has candidate key:  
`{StudentID, CourseCode}`  
Then:

- `StudentID` → **prime**
    
- `CourseCode` → **prime**
    

---

### 🚫 **Non-Prime Attribute**

A **non-prime attribute** is:

> 👉 Any attribute that is **not part of any candidate key**.

**Example:**

Let’s say your table looks like this:

|StudentID|CourseCode|StudentName|Grade|
|---|---|---|---|
|1|CS101|Alice|A|

If `{StudentID, CourseCode}` is the candidate key:

- `StudentName` and `Grade` → **non-prime** attributes
    

---

### 🤹 Why Does It Matter?

These terms are **critical** when working with **normal forms**, especially:

- **2NF:** No **partial dependency** of non-prime on part of a candidate key
    
- **3NF:** No **transitive dependency** of non-prime on non-prime
    
- **BCNF:** Every determinant must be a **candidate key**
    

So when you’re spotting dependencies, always ask:

> “Is this attribute prime or non-prime?”

That’s your red flag to normalize.
