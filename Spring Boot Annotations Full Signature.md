@Column(
    name = "",               // Custom column name
    unique = false,          // Adds UNIQUE constraint
    nullable = true,         // Adds NOT NULL constraint
    insertable = true,       // Included in INSERT SQL
    updatable = true,        // Included in UPDATE SQL
    length = 255,            // For VARCHAR columns
    precision = 0,           // For DECIMAL (total digits)
    scale = 0,               // For DECIMAL (digits after .)
    columnDefinition = "",   // Raw SQL column type override
    table = ""               // For secondary table mappings
)

@Table(
    name = "",                          // Table name in DB
    catalog = "",                       // For DB catalog (rare)
    schema = "",                        // For schema (Postgres, Oracle)
    uniqueConstraints = {},             // UniqueConstraint[] array
    indexes = {}                        // Index[] array
)


**JPA life-cycle Annotations**

| Annotation     | Trigger         | Description                                               |
| -------------- | --------------- | --------------------------------------------------------- |
| `@PrePersist`  | Before `INSERT` | When entity is first persisted (saved for the first time) |
| `@PostPersist` | After `INSERT`  | Right after it’s saved to DB                              |
| `@PreUpdate`   | Before `UPDATE` | Before Hibernate flushes updated fields                   |
| `@PostUpdate`  | After `UPDATE`  | After update SQL is executed                              |
| `@PreRemove`   | Before `DELETE` | Before entity is removed                                  |
| `@PostRemove`  | After `DELETE`  | After deletion from DB                                    |
| `@PostLoad`    | After loading   | When entity is loaded from DB into memory                 |

---

# 📘 **1️⃣ `@Version` — Optimistic Locking (Full Notes + Signature)**

`@Version` is a JPA annotation placed on a field in your entity to enable **optimistic concurrency control**.

It tells Hibernate:

> “This row must only be updated if its version matches the version I originally loaded.”

---

## ✔ **Full Official Annotation Signature**

```java
@Target({METHOD, FIELD})
@Retention(RUNTIME)
public @interface Version {
}
```

### 🔹 Attributes

`@Version` has **no attributes**.  
It only marks a field as the versioning field.

---

## ✔ **Supported Field Types**

The version field must be one of:

- `int` / `Integer`
    
- `long` / `Long`
    
- `short` / `Short`
    
- `java.sql.Timestamp`
    
- `java.time.Instant`
    

---

## ✔ **Example Usage**

### Entity:

```java
@Entity
public class UserProfile {

    @Id
    private Long id;

    private long karma;

    @Version
    private Long version;  // Hibernate auto-increments this
}
```

**What happens under the hood:**

When saving:

```sql
UPDATE user_profile
SET karma = ?, version = version + 1
WHERE id = ? AND version = ?
```

If no rows are updated → **OptimisticLockException** thrown.

---

## ✔ **What Exceptions Does It Throw?**

- `OptimisticLockException` (JPA)
    
- `ObjectOptimisticLockingFailureException` (Spring Data wrapper)
    
- `StaleObjectStateException` (Hibernate)
    

---

## ✔ **When to Use `@Version`**

Use optimistic locking when:

- Multiple fields need consistency
    
- Updates involve more than a simple increment
    
- Business logic must not lose any write
    
- Writes are moderate (not counters)
    

**Typical examples:**

- Updating a profile
    
- Updating an order
    
- Editing an entity with multiple fields
    
- Inventory reservation
    
- Money transfers
    

---

## ❌ **When NOT to Use `@Version`**

Do **NOT** use it for:

❌ counters  
❌ likes  
❌ karma  
❌ page views  
❌ follower counts  
❌ anything updated hundreds/thousands of times per second

Because it will cause **tons of OptimisticLockException retries**.

---

# 📘 **2️⃣ `@Modifying` — For UPDATE/DELETE Queries (Full Notes + Signature)**

`@Modifying` is Spring Data's annotation to mark repository queries as write operations.

---

## ✔ **Full Official Annotation Signature**

```java
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Modifying {

    boolean flushAutomatically() default false;

    boolean clearAutomatically() default false;

    boolean ignoreWarnings() default false;
}
```

### ✔ Attributes Explained

|Attribute|Default|Meaning|
|---|---|---|
|`flushAutomatically()`|`false`|Flush persistence context before running the query|
|`clearAutomatically()`|`false`|Clear persistence context after running the query|
|`ignoreWarnings()`|`false`|Ignore SQL warnings (rarely used)|

---

## ✔ **Example Usage (Atomic Increment)**

```java
public interface UserProfileRepository extends JpaRepository<UserProfile, Long> {

    @Modifying
    @Query("UPDATE UserProfile u SET u.karma = u.karma + 1 WHERE u.id = :id")
    int incrementKarma(@Param("id") Long id);
}
```

### 👀 Why this is safe?

The DB does:

```sql
UPDATE user_profile SET karma = karma + 1 WHERE id = ?
```

This is **atomic** even under 1000 simultaneous updates.

---

## ✔ **Example with clear + flush**

Useful when updating fields without loading entity:

```java
@Modifying(flushAutomatically = true, clearAutomatically = true)
@Query("UPDATE UserProfile u SET u.status = 'ACTIVE' WHERE u.id = :id")
void activateUser(@Param("id") Long id);
```

---

## ✔ **What Exceptions Does It Throw?**

Depends on DB:

- ConstraintViolationException
    
- DataIntegrityViolationException
    
- SQLIntegrityConstraintViolationException
    
- TransactionSystemException
    

---

## ✔ **When to Use `@Modifying`**

Perfect for **high-frequency writes**:

- Karma increment
    
- Likes increment
    
- View count increment
    
- Reduce inventory
    
- Bulk updates
    
- Soft deletes
    
- Flag updates
    
- Status changes
    

Why?

Because it avoids:

- loading entity
    
- checking version
    
- dirty checking
    
- unnecessary locking
    
- retry loops
    

---

# ⚔️ **3️⃣ `@Version` vs `@Modifying` (Final Comparison Table)**

|Feature|`@Version`|`@Modifying`|
|---|---|---|
|Purpose|Protect entire entity|Perform atomic DB update|
|Concurrency style|Optimistic locking|Database-level atomicity|
|Clash behavior|Throws exception|No conflict|
|Requires retry?|Yes|No|
|Best for|Full aggregates|Counters & simple fields|
|Performance|Medium|Extremely fast|
|DB load|High under contention|Minimal|
|Domain involvement|High|None (pure SQL)|

---

# 🎯 **4️⃣ Which one YOU should use (BloomConnect karma case)**

For your **karma** example:

**Use @Modifying atomic update.**

Because:

- high-frequency
    
- small field update
    
- correctness needed
    
- no need for reading-modify-write pattern
    
- perfect for SQL atomic operations
    

`@Version` would cause:

- collisions
    
- retries
    
- reduced throughput
    

Not worth it.


---

