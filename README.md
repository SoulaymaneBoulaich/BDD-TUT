Apologies for the confusion earlier. Below is the **full README.md** that includes all the code from the PDF content you shared:

````markdown
# Real Estate Management Platform

## Project Overview
This project is a complete full-stack real estate management platform designed to handle property listings, buyer-seller interactions, and admin functionalities using real Kaggle housing data, a robust MySQL database, Flask REST API backend, and an interactive web frontend.

### What the Platform Does:
- **Buyers**: Search properties with smart filters, save favorites, schedule appointments, and view their personal dashboard.
- **Sellers**: List new properties, manage existing listings, track status, and monitor sales metrics.
- **Admins**: Manage users and agents, monitor transactions, and generate platform reports.

## Architecture

### Tech Stack
| Component     | Technology      | Version |
| ------------- | --------------- | ------- |
| Backend       | Python + Flask  | 3.10+   |
| Database      | MySQL           | 8.x     |
| Frontend      | HTML/CSS/JS     | ES6+    |
| API           | REST            | JSON    |
| ORM           | flask-mysqldb   | Latest  |
| Data Source   | Kaggle          | USA Real Estate |
| Collaboration | Git + GitHub    | Branches, PRs |
| Testing       | Postman/Insomnia| API Testing |
| Design        | Figma           | UI Prototype |

## Team Roles
1. **Person 1: Database Architect**
   - Design ER diagrams
   - Create tables with constraints
   - Map Kaggle data → SQL schema
   - Write import scripts

2. **Person 2: Backend Engineer**
   - Write SQL stored procedures
   - Build REST API endpoints
   - Implement role-based access
   - Handle business logic

3. **Person 3: Frontend Developer**
   - Design responsive pages
   - Build interactive forms
   - Create buyer & seller dashboards

4. **Person 4: QA & DevOps**
   - Write test plans
   - Test API endpoints
   - Create UI mockups in Figma
   - Write documentation

## Getting Started

### Prerequisites
- MySQL Community Server (Windows/macOS/Linux)
- Python 3.10+
- pip (Python package manager)
- Git
- GitHub account
- MySQL Workbench (GUI)
- VS Code or PyCharm (Editor)
- Postman or Insomnia (API Testing)

### Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/<org-or-user>/real-estate-platform.git
    cd real-estate-platform
    ```

2. Create a virtual environment and activate it:
    ```bash
    python -m venv venv
    source venv/bin/activate  # macOS/Linux
    venv\Scripts\activate     # Windows
    ```

3. Install dependencies:
    ```bash
    pip install flask flask-mysqldb flask-cors requests
    ```

### Database Setup

1. Log into MySQL:
    ```bash
    mysql -u root -p
    ```

2. Create the database:
    ```sql
    CREATE DATABASE real_estate_db;
    USE real_estate_db;
    ```

### Data Import

1. Download Kaggle dataset: [USA Real Estate Dataset](https://www.kaggle.com/datasets) and place it in `data/usa_housing.csv`.

2. Run the import script:
    ```bash
    python scripts/import_data.py
    ```

## User Roles

### Buyer
- Register and login securely.
- Search properties with filters (price range, bedrooms, etc.).
- View property details.
- Save properties to favorites.
- Schedule appointments.
- View their dashboard with saved properties and appointments.

### Seller
- Register and login as a seller.
- Add new property listings with full details.
- Edit or delete existing listings.
- View dashboard with property stats (active, sold, etc.).

### Admin
- Manage user accounts.
- Manage agents and commissions.
- View all transactions.
- Generate platform reports.

## Database & Data Import

### Step 1: Create Tables

```sql
-- Users Table
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    phone VARCHAR(20),
    user_type ENUM('Buyer','Seller','Admin') NOT NULL,
    registration_date DATE DEFAULT CURDATE()
);

-- Agents Table
CREATE TABLE Agents (
    agent_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    phone VARCHAR(20),
    commission_rate DECIMAL(5,2) CHECK (commission_rate >= 0),
    hire_date DATE
);

-- Properties Table (Main!)
CREATE TABLE Properties (
    property_id INT PRIMARY KEY AUTO_INCREMENT,
    address VARCHAR(200) NOT NULL,
    city VARCHAR(100),
    state VARCHAR(50),
    zipcode VARCHAR(20),
    price DECIMAL(12,2) CHECK (price >= 0),
    bedrooms INT CHECK (bedrooms >= 0),
    bathrooms INT CHECK (bathrooms >= 0),
    area_sqft INT CHECK (area_sqft >= 0),
    property_type ENUM('House', 'Apartment', 'Condo', 'Villa'),
    status ENUM('Available', 'Pending', 'Sold') DEFAULT 'Available',
    listing_date DATE DEFAULT CURDATE(),
    seller_id INT,
    agent_id INT,
    CONSTRAINT fk_properties_seller FOREIGN KEY (seller_id) REFERENCES Users(user_id)
    ON UPDATE CASCADE ON DELETE SET NULL,
    CONSTRAINT fk_properties_agent FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
    ON UPDATE CASCADE ON DELETE SET NULL
);

-- Property Images Table
CREATE TABLE Property_Images (
    image_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    image_url VARCHAR(300) NOT NULL,
    CONSTRAINT fk_images_property FOREIGN KEY (property_id) REFERENCES Properties(property_id) ON DELETE CASCADE
);

-- Transactions Table
CREATE TABLE Transactions (
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    buyer_id INT NOT NULL,
    seller_id INT NOT NULL,
    agent_id INT NOT NULL,
    sale_price DECIMAL(12,2) CHECK (sale_price >= 0),
    sale_date DATE DEFAULT CURDATE(),
    CONSTRAINT fk_tx_property FOREIGN KEY (property_id) REFERENCES Properties(property_id),
    CONSTRAINT fk_tx_buyer FOREIGN KEY (buyer_id) REFERENCES Users(user_id),
    CONSTRAINT fk_tx_seller FOREIGN KEY (seller_id) REFERENCES Users(user_id),
    CONSTRAINT fk_tx_agent FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
);

-- Appointments Table
CREATE TABLE Appointments (
    appointment_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    client_id INT NOT NULL,
    agent_id INT NOT NULL,
    appointment_date DATETIME NOT NULL,
    status ENUM('Scheduled','Completed','Cancelled') DEFAULT 'Scheduled',
    CONSTRAINT fk_appt_property FOREIGN KEY (property_id) REFERENCES Properties(property_id),
    CONSTRAINT fk_appt_client FOREIGN KEY (client_id) REFERENCES Users(user_id),
    CONSTRAINT fk_appt_agent FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
);

-- Favorites Table
CREATE TABLE Favorites (
    user_id INT NOT NULL,
    property_id INT NOT NULL,
    saved_date DATETIME DEFAULT NOW(),
    PRIMARY KEY (user_id, property_id),
    CONSTRAINT fk_fav_user FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    CONSTRAINT fk_fav_property FOREIGN KEY (property_id) REFERENCES Properties(property_id) ON DELETE CASCADE
);

-- Price History Table (Track Changes!)
CREATE TABLE Price_History (
    history_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    old_price DECIMAL(12,2),
    new_price DECIMAL(12,2),
    change_date DATETIME DEFAULT NOW(),
    CONSTRAINT fk_ph_property FOREIGN KEY (property_id) REFERENCES Properties(property_id) ON DELETE CASCADE
);
````

### Step 2: Import Data

```python
import csv
import mysql.connector

CSV_PATH = 'data/usa_housing.csv'

conn = mysql.connector.connect(
    host='localhost',
    user='root',
    password='your_root_password',
    database='real_estate_db'
)
cur = conn.cursor()

# Sample agent and seller insertion
cur.execute("""
    INSERT INTO Agents (name, email, phone, commission_rate, hire_date)
    VALUES ('Import Agent', 'import@realty.com', '000-0000', 3.0, '2024-01-01')
""")
cur.execute("""
    INSERT INTO Users (name, email, phone, user_type)
    VALUES ('Sample Seller', 'seller@example.com', '555-1111', 'Seller')
""")
agent_id = cur.lastrowid
cur.execute("SELECT user_id FROM Users WHERE email = 'seller@example.com'")
seller_id = cur.fetchone()[0]
conn.commit()

# Import CSV Data
with open(CSV_PATH, newline='', encoding='utf-8') as f:
    reader = csv.DictReader(f)
    for row in reader:
        address = row.get('address') or row.get('Address')
        city = row.get('city') or row.get('City')
        state = row.get('state') or row.get('State')
        zipcode = row.get('zipcode') or row.get('Zipcode')
        price = row.get('price') or row.get('Price')
        bedrooms = row.get('bedrooms') or row.get('Bedrooms')
        bathrooms = row.get('bathrooms') or row.get('Bathrooms')
        area = row.get('sqft') or row.get('sqft_living') or row.get('Area')

        cur.execute("""
            INSERT INTO Properties
            (address, city, state, zipcode, price, bedrooms, bathrooms,
            area_sqft, property_type, status, seller_id, agent_id)
            VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s)
        """, (address, city, state, zipcode, price, bedrooms, bathrooms, area, 'House', 'Available', seller_id, agent_id))

conn.commit()
cur.close()
conn.close()

print("✅ Import completed successfully!")
```

## Backend API

The backend is developed using Flask and exposes several REST API endpoints. Here are some examples:

### Sample API Code (Flask)

```python
from flask import Flask, request, jsonify
from flask_mysqldb import MySQL
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

app.config['MYSQL_HOST'] = 'localhost'
app.config['MYSQL_USER'] = 'root'
app.config['MYSQL_PASSWORD'] = 'your_root_password'
app.config['MYSQL_DB'] = 'real_estate_db'
app.config['MYSQL_CURSORCLASS'] = 'DictCursor'

mysql = MySQL(app)

@app.route('/api/properties', methods=['GET'])
def list_properties():
    city = request.args.get('city')
    min_price = request.args.get('min_price', 0)
    max_price = request.args.get('max_price', 999999999)
    
    cur = mysql.connection.cursor()
    sql = "SELECT * FROM Properties WHERE price BETWEEN %s AND %s AND status = 'Available'"
    params = [min_price, max_price]
    
    if city:
        sql += " AND city = %s"
        params.append(city)
    
    cur.execute(sql, params)
    rows = cur.fetchall()
    cur.close()
    
    return jsonify(rows)

if __name__ == "__main__":
    app.run(debug=True)
```

## Frontend

The frontend of this project is built using HTML, CSS, and JavaScript with Flask templating. Here is an example of a simple page for viewing properties:

### Frontend Code (Flask Templating)

```python
from flask import Flask, render_template, request, redirect, url_for, session
import requests

app = Flask(__name__)
app.secret_key = 'your_secret_key_here'

API_BASE = 'http://127.0.0.1:5000/api'

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/buyer/search', methods=['GET','POST'])
def buyer_search():
    if request.method == 'POST':
        city = request.form.get('city')
        min_price = request.form.get('min_price', 0)
        max_price = request.form.get('max_price', 999999999)
        params = {'city': city, 'min_price': min_price, 'max_price': max_price}
        resp = requests.get(f'{API_BASE}/properties', params=params)
        properties = resp.json() if resp.status_code == 200 else []
        return render_template('buyer_results.html', properties=properties)
    
    return render_template('buyer_search.html')

if __name__ == '__main__':
    app.run(debug=True)
```

## Folder Structure

```
real-estate-platform/
├─ backend/
│   ├─ app.py
│   ├─ requirements.txt
│
├─ frontend/
│   ├─ app_frontend.py
│   ├─ templates/
│   └─ static/
│
└─ scripts/
    ├─ import_data.py
    └─ data/
        └─ usa_housing.csv
```

## GitHub Workflow

1. Clone the repository and create feature branches:

   ```bash
   git clone https://github.com/<org-or-user>/real-estate-platform.git
   git checkout -b feature/person1-database
   ```

2. Commit and push changes:

   ```bash
   git add .
   git commit -m "Add database schema for properties"
   git push origin feature/person1-database
   ```

3. Create a pull request (PR) for code review:

   * Open PR on GitHub and get reviewed by teammates.
   * Merge to the `main` branch once reviewed.

## Testing & Documentation

### Testing:

* **Backend**: Test API endpoints using Postman or Insomnia.

  * Ensure all endpoints are working, including invalid inputs.
  * Test role-based access control (Buyer vs Seller).
* **Frontend**: Test UI components like property search and listing management.

### Documentation:

* API documentation for all endpoints.
* User manual with screenshots and step-by-step guides.
* Final report covering architecture, database schema, API features, and testing results.

## Checklist Before Launch

* ✅ Database created with all tables
* ✅ Kaggle data imported successfully
* ✅ Backend API running on port 5000
* ✅ Frontend running on port 5001
* ✅ API endpoints tested with Postman

---

Your team is about to create something amazing! Let's build and learn together. ✨

```

This **README.md** is structured to provide all the essential details and code from your project, formatted for ease of use by your development team. Let me know if you need further adjustments!
```
