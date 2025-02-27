**ETL Pipeline for Fetching Public API Data and Storing in MySQL**

Project Overview

This project implements an **ETL (Extract, Transform, Load) pipeline** to fetch country-related data from a **public API**, transform it into a structured format, and store it in a **MySQL database**. The pipeline extracts data, converts it into a **Pandas DataFrame**, and inserts it into a MySQL table.



**Technologies Used**

- **Python** for scripting
- **Pandas** for data transformation
- **MySQL** for storing extracted data
- **Requests** for API calls
- **MySQL Connector** for database operations


## **Setup & Installation**

### **1️⃣ Install Dependencies**

Ensure you have Python installed, then install the required packages:

```sh
pip install pandas mysql-connector-python requests
```

 **2️⃣ Configure MySQL Database**

Create a database and table to store the data:

```sql
CREATE DATABASE my_database;
USE my_database;

CREATE TABLE api_data (
    id INT AUTO_INCREMENT PRIMARY KEY,
    code VARCHAR(10),
    iso2code VARCHAR(5),
    name VARCHAR(255)
);
```

### **3️⃣ Implement the ETL Script**

```python
import requests
import pandas as pd
import mysql.connector

# Step 1: Extract Data from API
url = "https://public-api-url.com/data"
response = requests.get(url)
data = response.json()

# Step 2: Transform Data
records = data[1]  # Extract list of country data
df = pd.DataFrame(records)

# Step 3: Load Data into MySQL
db_connection = mysql.connector.connect(
    host="localhost",
    user="root",
    password="",
    database="my_database"
)
cursor = db_connection.cursor()

for _, row in df.iterrows():
    sql = "INSERT INTO api_data (code, iso2code, name) VALUES (%s, %s, %s)"
    values = (row.get('code', 'Unknown'), row.get('iso2code', None), row.get('name', 'No Name'))
    cursor.execute(sql, values)

# Commit and close
db_connection.commit()
cursor.close()
db_connection.close()

print("Data successfully inserted!")
```

---

**Challenges & Solutions**

**1️⃣ Challenge: API Response Structure**

- **Issue:** The API response returned a **nested list** instead of a straightforward JSON object.
- **Solution:** We extracted only the relevant data using `data[1]` before converting it into a Pandas DataFrame.

**2️⃣ Challenge: KeyErrors in Data Extraction**

- **Issue:** Some records were missing expected fields like `code` or `iso2code`, causing KeyErrors.
- **Solution:** Used `.get()` method with default values:
  ```python
  row.get('code', 'Unknown')
  row.get('iso2code', None)
  ```

 **3️⃣ Challenge: Database Connectivity Issues**

- **Issue:** The script initially failed to connect to MySQL due to incorrect credentials.
- **Solution:** Ensured MySQL server was running and used correct credentials in the connection string.

 **4️⃣ Challenge: Duplicate Entries**

- **Issue:** Running the script multiple times led to duplicate records.
- **Solution:**
  - Used **primary key constraints** in MySQL.
  - Alternative approach: Used `INSERT IGNORE` or `ON DUPLICATE KEY UPDATE`.

---

**Next Steps**

- Automate the script using **cron jobs** (Linux) or **Task Scheduler** (Windows).
- Improve error handling using **logging** instead of print statements.
- Implement **API authentication** if required for future enhancements.

---

 **Conclusion**

This ETL pipeline successfully automates data extraction from a public API, processes it into a structured format, and loads it into a MySQL database. The project showcases efficient **data handling, transformation, and storage** while addressing common ETL challenges effectively.


