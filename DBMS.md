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




# Database Indexes, Sharding, and Partitioning — Notes

## 1. Database Index Types

### **1.0 Geospatial / Spatial Indexes (R-Tree, GiST, SP-GiST)**

- Used for geographic, geometric, map/location data.
    
- Support queries like:
    
    - "find points within radius"
        
    - "find all stores near me"
        
    - "intersects", "contains", "overlaps" (shapes)
        
- Common structures:
    
    - **R-Tree** (classic spatial index, used in many geospatial systems)
        
    - **GiST** (Postgres general index that implements R-tree-like behavior)
        
    - **SP-GiST** (space-partitioned index; good for quadtree, k-d trees)
        
- Used in PostGIS (Postgres extension) for:
    
    - lat/long
        
    - polygons
        
    - lines
        
    - geometric shapes
        

#### When to use:

- Any location-based app (Uber, Swiggy, Google Maps-like feature)
    
- Distance, bounding box, nearest-neighbor queries
    
- Fast geofencing checks
    

### **1.1 B-tree Index****

- Most common index type.
    
- Good for: exact match, range queries (`=`, `<`, `>`, `BETWEEN`, `LIKE 'abc%'`).
    
- Balanced tree → search complexity ~ O(log n).
    
- Default index type in Postgres/MySQL.
    

### **1.2 Hash Index**

- Uses hashing, not tree.
    
- Fast for exact equality lookups (`=`).
    
- Terrible for range queries.
    
- Rarely used because B-tree is good enough.
    

### **1.3 Bitmap Index**

- Mostly used in data warehouses.
    
- Good for columns with low cardinality (few unique values), like gender, status.
    
- Great for combining multiple filters.
    
- Not common in OLTP databases.
    

### **1.4 GiST Index (Generalized Search Tree)**

- For complex data types.
    
- Used in Postgres for: geometric types, full-text search, ranges.
    
- Example: searching within polygons.
    

### **1.5 GIN Index (Generalized Inverted Index)**

- Best for array, JSONB, full-text search.
    
- Supports `@>`, containment queries.
    
- Good for: tags, json fields, text search.
    

### **1.6 BRIN Index (Block Range Index)**

- Very lightweight.
    
- Good for huge tables where data is physically ordered.
    
- Perfect for time-series tables.
    
- Example: logs, events, audit tables.
    

### **When to use Indexes**

- Use B-tree for everyday queries.
    
- Use GIN/GiST for JSONB, text search, geometric.
    
- Use BRIN for massive append-only tables.
    
- Avoid indexing columns with extremely low selectivity unless needed.
    

## 2. Partitioning

### **What is Partitioning?**

Splitting a **table inside one database** into smaller, more manageable pieces.

### Types:

#### **2.1 Range Partitioning**

- Used for time-series or numeric ranges.
    
- Example: `orders_2024_01`, `orders_2024_02`.
    
- Best for: logs, events, time-based data.
    

#### **2.2 Hash Partitioning**

- Evenly distributes rows using hashing.
    
- Good for load balancing large tables.
    

#### **2.3 List Partitioning**

- Partition by category.
    
- Example: region IN ("IN", "US", "EU").
    

#### **Benefits of Partitioning**

- Partition pruning → faster queries.
    
- Easier maintenance (drop old partitions).
    
- Improved index size & performance.
    
- No change to application code.
    

#### **When to Use Partitioning**

- Tables > 50–100 million rows.
    
- Time-based data.
    
- Fast deletion/archival needed.
    
- Improve insert/query performance on very large tables.
    

## 3. Sharding

### **What is Sharding?**

Splitting data **across multiple database servers**.

### Why?

- When DB is too large for a single machine.
    
- To increase write throughput.
    
- To distribute load.
    

### Sharding Strategies

#### **3.1 Range Sharding**

- Example: `user_id 1–1M → DB1`, `1M–2M → DB2`.
    
- Easy to reason about.
    
- Risk: hotspots if user distribution is uneven.
    

#### **3.2 Hash Sharding**

- `shard = hash(user_id) % N`
    
- Most balanced.
    
- Harder to migrate when increasing shard count.
    

#### **3.3 Directory / Lookup Table**

- Central service that tells which shard contains which key.
    
- Example: Facebook-like directory service.
    
- Flexible but requires extra infra.
    

### Downsides of Sharding

- Cross-shard queries = painful.
    
- Cannot rely on DB joins.
    
- Transactions across shards are complex.
    
- Migrations require data movement.
    

### When to Use Sharding

- When a single DB server cannot:
    
    - store your data
        
    - handle your traffic
        
    - scale vertically
        
- When partitioning is not enough.
    
- Typically at > 500GB–1TB per table or very high TPS.
    

## 4. Partitioning vs Sharding vs Indexing — When to Use What

### **Indexing**

Use when:

- Queries are slow due to full table scans.
    
- Table fits on one machine.
    
- High-read frequency on specific columns.
    

### **Partitioning**

Use when:

- Table is huge (millions to billions).
    
- Queries target ranges (dates, IDs).
    
- Need fast archival.
    
- One machine still can hold all data.
    

### **Sharding**

Use when:

- Single database server can’t handle load or size.
    
- You need horizontal scaling.
    
- Data exceeds CPU/RAM/IO limits of one machine.
    

### A simple rule:

- Start with **indexes**.
    
- When table becomes huge → use **partitioning**.
    
- When the whole DB becomes too big → use **sharding**.
    

## Summary Table

|Feature|Indexing|Partitioning|Sharding|
|---|---|---|---|
|Scale Level|Small → Medium DB|Medium → Large Tables|Very Large DB / High Traffic|
|Servers|1|1|Many|
|Purpose|Speed up queries|Manage huge tables|Horizontal scaling across machines|
|App Changes|None|None|Required|
|Complexity|Low|Medium|High|
|Cross Queries|Easy|Easy|Hard|
|Use Case|Slow queries|Time-series, logs, big tables|Social networks, e-commerce, SaaS scale|