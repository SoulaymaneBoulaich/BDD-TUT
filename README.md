# 🏠 Real Estate Platform – Full‑Stack, Real Data

![Header](https://img.shields.io/badge/Stack-Python%2B%2BFlask%2B%2BMySQL-blue?style=for-the-badge)
![Team](https://img.shields.io/badge/Team%20Size-4%20Developers-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active%20Development-orange?style=for-the-badge)

> 🚀 A complete full‑stack real estate management platform built with **real Kaggle housing data**, a robust **MySQL** database, a **Flask REST API** backend, and an interactive web frontend. Perfect for a 4‑person team learning full‑stack development with **GitHub** collaboration!

---

## 📑 Table of Contents

- [🎯 Project Overview](#-project-overview)
- [🏗️ Architecture](#️-architecture)
- [🛠️ Tech Stack](#️-tech-stack)
- [👥 Team Roles](#-team-roles)
- [🚀 Getting Started](#-getting-started-everyone)
- [👤 User Roles](#-user-roles)
- [🗄️ Database & Data Import](#️-database--data-import)
- [⚙️ Backend API](#️-backend-api)
- [🎨 Frontend](#-frontend)
- [💻 GitHub Workflow](#-github-workflow)
- [✅ Testing & Documentation](#-testing--documentation)
- [📡 API Overview](#-api-overview)
- [🎭 Figma Prototype Brief](#-figma-prototype-brief)

---

## 🎯 Project Overview

Build a **modern real estate management platform** that transforms how buyers, sellers, and admins interact with property listings!

### What Your Users Can Do:

**🔍 Buyers**
- Search properties with intelligent filtering (city, price, bedrooms)
- Browse detailed listings with high-res images
- Save favorite properties for later
- Schedule appointments instantly

**📝 Sellers**
- List properties with comprehensive details
- Update listings in real-time
- Manage their dashboard with sales metrics
- Track property status (Available → Pending → Sold)

**🛡️ Admins**
- Monitor all users and agents
- View transaction analytics
- Generate platform reports

All powered by **real Kaggle real estate data**, not mock data! 📊

---

## 🏗️ Architecture

┌─────────────────────────────────────────────────────────────┐
│                    🌐 YOUR TECH STACK                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Frontend (Port 5001)          Backend (Port 5000)           │
│  ┌─────────────────────┐      ┌──────────────────────┐      │
│  │  Flask Templates    │      │  Flask REST API      │      │
│  │  HTML/CSS/JS        │◄────►│  Role-Based Routes   │      │
│  │  Bootstrap          │      │  Business Logic      │      │
│  └─────────────────────┘      └──────────────────────┘      │
│                                         ▲                     │
│                                         │                     │
│                              ┌──────────▼──────────┐         │
│                              │  🗄️  MySQL Database │         │
│                              │  8 Tables           │         │
│                              │  Stored Procedures  │         │
│                              │  Triggers           │         │
│                              └─────────────────────┘         │
│                                                               │
└─────────────────────────────────────────────────────────────┘

### Database Schema
**8 Powerhouse Tables** 📊
- `Users` - Buyers, Sellers, Admins
- `Agents` - Real estate agents
- `Properties` - All listings (loaded with Kaggle data)
- `Property_Images` - Multiple photos per property
- `Appointments` - Booking system
- `Favorites` - User wishlists
- `Transactions` - Sale records
- `Price_History` - Price change tracking

---

## 🛠️ Tech Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| **Backend** | Python + Flask | 3.10+ |
| **Database** | MySQL | 8.x |
| **Frontend** | HTML/CSS/JS | ES6+ |
| **API** | REST | JSON |
| **ORM** | flask-mysqldb | Latest |
| **Data Source** | Kaggle | USA Real Estate |
| **Collaboration** | Git + GitHub | Branches, PRs |
| **Testing** | Postman/Insomnia | API Testing |
| **Design** | Figma | UI Prototype |

---

## 👥 Team Roles

Divide and conquer! Each person owns one pillar:

### 👤 Person 1: Database Architect 🗄️
**Your Superpowers:**
- ✅ Design ER diagrams
- ✅ Create tables with constraints
- ✅ Map Kaggle data → SQL schema
- ✅ Write import scripts

**Deliverables:** Schema, indices, data import script

---

### 👤 Person 2: Backend Engineer ⚙️
**Your Superpowers:**
- ✅ Write SQL stored procedures
- ✅ Build REST API endpoints
- ✅ Implement role-based access
- ✅ Handle business logic

**Deliverables:** Flask API, 15+ endpoints, auth routes

---

### 👤 Person 3: Frontend Developer 🎨
**Your Superpowers:**
- ✅ Design responsive pages
- ✅ Build interactive forms
- ✅ Call REST APIs from JS
- ✅ Create buyer & seller dashboards

**Deliverables:** 8+ pages, API integration, auth UI

---

### 👤 Person 4: QA & DevOps 🚀
**Your Superpowers:**
- ✅ Write test plans
- ✅ Test API endpoints
- ✅ Create UI mockups in Figma
- ✅ Write comprehensive docs

**Deliverables:** Test report, API tests, UI prototype, README, user manual

---

## 🚀 Getting Started (Everyone)

### 📋 Prerequisites

Install these first:

# Database
📥 MySQL Community Server (Windows/macOS/Linux)

# Programming
📥 Python 3.10+
📥 pip (Python package manager)

# Version Control
📥 Git
📥 GitHub account

# Optional Tools
📥 MySQL Workbench (GUI)
📥 VS Code or PyCharm (Editor)
📥 Postman or Insomnia (API Testing)

### 🔄 Clone the Repository

git clone https://github.com/<org-or-user>/real-estate-platform.git
cd real-estate-platform

### 🐍 Create Virtual Environment

python -m venv venv

# Activate it:
# Windows:
venv\Scripts\activate

# macOS/Linux:
source venv/bin/activate

# Install dependencies:
pip install flask flask-mysqldb flask-cors requests

**✨ Boom! You're ready to go!**

---

## 👤 User Roles

### 🔍 Buyer

> *"I want to find my dream home easily!"*

**What They Can Do:**
- 🔐 Register & login securely
- 🏠 Search properties with smart filters
- 📊 Apply advanced filters (price range slider, bedrooms, property type)
- 👁️ View gorgeous property detail pages with agent info
- ❤️ Save properties to favorites for later
- 📅 Schedule appointments with 1-click
- 📱 View personal dashboard with favorites & appointments
- ⚡ Sort by price, newest, most popular

**Key API Endpoints:**
GET    /api/properties              → Search & filter
GET    /api/properties/<id>         → Property details
POST   /api/favorites               → Save property
GET    /api/buyer/<id>/favorites    → View saved
POST   /api/appointments            → Schedule visit
GET    /api/buyer/<id>/dashboard    → My dashboard

---

### 📝 Seller

> *"I need to manage my listings and reach buyers!"*

**What They Can Do:**
- 🔐 Register & login as seller
- ➕ Add new property listings with full details
- ✏️ Edit existing listings (price, photos, description)
- 🗑️ Remove listings they own
- 📊 View seller dashboard with stats
- 📈 Track available, pending, and sold properties
- 👀 Monitor who's interested in their properties
- 📸 Upload multiple photos per property

**Key API Endpoints:**
POST   /api/seller/properties           → Create listing
GET    /api/seller/<id>/properties      → My listings
PUT    /api/seller/properties/<id>      → Update listing
DELETE /api/seller/properties/<id>      → Remove listing
GET    /api/seller/<id>/dashboard       → My dashboard

---

### 🛡️ Admin

> *"I keep this platform running smoothly!"*

**What They Can Do:**
- 👥 Manage user accounts
- 🤝 Manage agents & commissions
- 💰 View all transactions
- 📊 Generate platform reports
- 🔍 Monitor platform health

---

## 🗄️ Database & Data Import

### Step 1: Install & Start MySQL

mysql -u root -p

### Step 2: Create Database

CREATE DATABASE real_estate_db;
USE real_estate_db;

### Step 3: Create Tables

Run this complete schema:

-- 👥 Users Table
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    phone VARCHAR(20),
    user_type ENUM('Buyer','Seller','Admin') NOT NULL,
    registration_date DATE DEFAULT CURDATE()
);

-- 🤝 Agents Table
CREATE TABLE Agents (
    agent_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    phone VARCHAR(20),
    commission_rate DECIMAL(5,2) CHECK (commission_rate >= 0),
    hire_date DATE
);

-- 🏠 Properties Table (Main!)
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
    CONSTRAINT fk_properties_seller
        FOREIGN KEY (seller_id) REFERENCES Users(user_id)
        ON UPDATE CASCADE
        ON DELETE SET NULL,
    CONSTRAINT fk_properties_agent
        FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
        ON UPDATE CASCADE
        ON DELETE SET NULL
);

-- 📸 Property Images Table
CREATE TABLE Property_Images (
    image_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    image_url VARCHAR(300) NOT NULL,
    CONSTRAINT fk_images_property
        FOREIGN KEY (property_id) REFERENCES Properties(property_id)
        ON DELETE CASCADE
);

-- 💰 Transactions Table
CREATE TABLE Transactions (
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    buyer_id INT NOT NULL,
    seller_id INT NOT NULL,
    agent_id INT NOT NULL,
    sale_price DECIMAL(12,2) CHECK (sale_price >= 0),
    sale_date DATE DEFAULT CURDATE(),
    CONSTRAINT fk_tx_property
        FOREIGN KEY (property_id) REFERENCES Properties(property_id),
    CONSTRAINT fk_tx_buyer
        FOREIGN KEY (buyer_id) REFERENCES Users(user_id),
    CONSTRAINT fk_tx_seller
        FOREIGN KEY (seller_id) REFERENCES Users(user_id),
    CONSTRAINT fk_tx_agent
        FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
);

-- 📅 Appointments Table
CREATE TABLE Appointments (
    appointment_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    client_id INT NOT NULL,
    agent_id INT NOT NULL,
    appointment_date DATETIME NOT NULL,
    status ENUM('Scheduled','Completed','Cancelled') DEFAULT 'Scheduled',
    CONSTRAINT fk_appt_property
        FOREIGN KEY (property_id) REFERENCES Properties(property_id),
    CONSTRAINT fk_appt_client
        FOREIGN KEY (client_id) REFERENCES Users(user_id),
    CONSTRAINT fk_appt_agent
        FOREIGN KEY (agent_id) REFERENCES Agents(agent_id)
);

-- ❤️ Favorites Table
CREATE TABLE Favorites (
    user_id INT NOT NULL,
    property_id INT NOT NULL,
    saved_date DATETIME DEFAULT NOW(),
    PRIMARY KEY (user_id, property_id),
    CONSTRAINT fk_fav_user
        FOREIGN KEY (user_id) REFERENCES Users(user_id)
        ON DELETE CASCADE,
    CONSTRAINT fk_fav_property
        FOREIGN KEY (property_id) REFERENCES Properties(property_id)
        ON DELETE CASCADE
);

-- 📊 Price History Table (Track Changes!)
CREATE TABLE Price_History (
    history_id INT PRIMARY KEY AUTO_INCREMENT,
    property_id INT NOT NULL,
    old_price DECIMAL(12,2),
    new_price DECIMAL(12,2),
    change_date DATETIME DEFAULT NOW(),
    CONSTRAINT fk_ph_property
        FOREIGN KEY (property_id) REFERENCES Properties(property_id)
        ON DELETE CASCADE
);

### Step 4: Create Indices 🚀

CREATE INDEX idx_properties_price ON Properties(price);
CREATE INDEX idx_properties_city ON Properties(city);
CREATE INDEX idx_properties_status ON Properties(status);
CREATE INDEX idx_properties_seller ON Properties(seller_id);
CREATE INDEX idx_appt_date ON Appointments(appointment_date);

**✨ Your database is turbocharged!**

### Step 5: Download Kaggle Dataset

1. Go to **Kaggle.com** and search for "USA Real Estate Dataset" or "USA House Sales Data"
2. Download the CSV file
3. Place it in `data/usa_housing.csv`

### Step 6: Import Data

Create `scripts/import_data.py`:

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

# Insert sample agent
cur.execute("""
    INSERT INTO Agents (name, email, phone, commission_rate, hire_date)
    VALUES ('Import Agent', 'import@realty.com', '000-0000', 3.0, '2024-01-01')
""")

# Insert sample seller
cur.execute("""
    INSERT INTO Users (name, email, phone, user_type)
    VALUES ('Sample Seller', 'seller@example.com', '555-1111', 'Seller')
""")

agent_id = cur.lastrowid
cur.execute("SELECT user_id FROM Users WHERE email = 'seller@example.com'")
seller_id = cur.fetchone()[0]
conn.commit()

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
        """, (
            address, city, state, zipcode,
            price, bedrooms, bathrooms, area,
            'House', 'Available', seller_id, agent_id
        ))

conn.commit()
cur.close()
conn.close()
print("✅ Import completed successfully!")

Run it:

python scripts/import_data.py

**🎉 Your database is now ALIVE with real data!**

---

## ⚙️ Backend API

> **Person 2's Domain** - Where the magic happens! ✨

### Setup Flask App

Create `backend/app.py`:

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

### SQL Superpowers 🔥

Run these stored procedures in MySQL:

DELIMITER //

-- 🔍 Search with smart filters
CREATE PROCEDURE SearchProperties(
    IN min_price DECIMAL(12,2),
    IN max_price DECIMAL(12,2),
    IN min_beds INT,
    IN property_city VARCHAR(100)
)
BEGIN
    SELECT * FROM Properties
    WHERE price BETWEEN min_price AND max_price
      AND bedrooms >= min_beds
      AND (property_city IS NULL OR city = property_city)
      AND status = 'Available';
END //

-- 💰 Record a sale with auto-updates
CREATE PROCEDURE RecordSale(
    IN prop_id INT,
    IN buyer_id INT,
    IN seller_id INT,
    IN agent_id INT,
    IN in_sale_price DECIMAL(12,2)
)
BEGIN
    START TRANSACTION;
    INSERT INTO Transactions 
    (property_id, buyer_id, seller_id, agent_id, sale_price, sale_date)
    VALUES (prop_id, buyer_id, seller_id, agent_id, in_sale_price, CURDATE());
    
    UPDATE Properties SET status = 'Sold' WHERE property_id = prop_id;
    COMMIT;
END //

-- 🪝 Auto-update status when transaction created
CREATE TRIGGER after_transaction_insert
AFTER INSERT ON Transactions
FOR EACH ROW
BEGIN
    UPDATE Properties SET status = 'Sold'
    WHERE property_id = NEW.property_id;
END //

-- 📈 Auto-track price changes
CREATE TRIGGER log_price_change
BEFORE UPDATE ON Properties
FOR EACH ROW
BEGIN
    IF OLD.price <> NEW.price THEN
        INSERT INTO Price_History 
        (property_id, old_price, new_price, change_date)
        VALUES (NEW.property_id, OLD.price, NEW.price, NOW());
    END IF;
END //

DELIMITER ;

### API Endpoints (Buyer Routes) 🔍

# Search with filters
@app.route('/api/properties', methods=['GET'])
def list_properties():
    city = request.args.get('city')
    min_price = request.args.get('min_price', 0)
    max_price = request.args.get('max_price', 999999999)
    
    cur = mysql.connection.cursor()
    sql = """
        SELECT * FROM Properties
        WHERE price BETWEEN %s AND %s AND status = 'Available'
    """
    params = [min_price, max_price]
    
    if city:
        sql += " AND city = %s"
        params.append(city)
    
    cur.execute(sql, params)
    rows = cur.fetchall()
    cur.close()
    return jsonify(rows)

# Get property details
@app.route('/api/properties/<int:property_id>', methods=['GET'])
def get_property(property_id):
    cur = mysql.connection.cursor()
    cur.execute("""
        SELECT p.*, a.name AS agent_name, a.phone AS agent_phone
        FROM Properties p
        JOIN Agents a ON p.agent_id = a.agent_id
        WHERE p.property_id = %s
    """, (property_id,))
    row = cur.fetchone()
    cur.close()
    return jsonify(row) if row else jsonify({'error': 'Not found'}), 404

# Save favorite ❤️
@app.route('/api/favorites', methods=['POST'])
def add_favorite():
    data = request.get_json()
    cur = mysql.connection.cursor()
    cur.execute("""
        INSERT INTO Favorites (user_id, property_id)
        VALUES (%s, %s)
    """, (data['user_id'], data['property_id']))
    mysql.connection.commit()
    cur.close()
    return jsonify({'status': 'ok'}), 201

# Buyer dashboard 📊
@app.route('/api/buyer/<int:user_id>/dashboard', methods=['GET'])
def buyer_dashboard(user_id):
    cur = mysql.connection.cursor()
    
    cur.execute("""
        SELECT p.* FROM Properties p
        JOIN Favorites f ON p.property_id = f.property_id
        WHERE f.user_id = %s
    """, (user_id,))
    favorites = cur.fetchall()
    
    cur.execute("""
        SELECT a.*, p.address, p.city
        FROM Appointments a
        JOIN Properties p ON a.property_id = p.property_id
        WHERE a.client_id = %s
        ORDER BY a.appointment_date DESC
    """, (user_id,))
    appointments = cur.fetchall()
    
    cur.close()
    return jsonify({'favorites': favorites, 'appointments': appointments})

### API Endpoints (Seller Routes) 📝

# Create property ➕
@app.route('/api/seller/properties', methods=['POST'])
def create_property():
    data = request.get_json()
    seller_id = data.get('seller_id')
    
    cur = mysql.connection.cursor()
    cur.execute("""
        INSERT INTO Properties
        (address, city, state, zipcode, price, bedrooms, bathrooms, 
         area_sqft, property_type, status, seller_id, agent_id)
        VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s)
    """, (
        data['address'], data.get('city'), data.get('state'), 
        data.get('zipcode'), data['price'], data['bedrooms'], 
        data['bathrooms'], data['area_sqft'], data.get('property_type', 'House'),
        data.get('status', 'Available'), seller_id, data.get('agent_id')
    ))
    mysql.connection.commit()
    new_id = cur.lastrowid
    cur.close()
    return jsonify({'property_id': new_id}), 201

# Update property ✏️
@app.route('/api/seller/properties/<int:property_id>', methods=['PUT'])
def update_property(property_id):
    data = request.get_json()
    cur = mysql.connection.cursor()
    
    # Verify ownership
    cur.execute("SELECT seller_id FROM Properties WHERE property_id = %s", 
                (property_id,))
    row = cur.fetchone()
    if not row or row['seller_id'] != data.get('seller_id'):
        cur.close()
        return jsonify({'error': 'Unauthorized'}), 403
    
    update_fields = []
    params = []
    for field in ['price', 'bedrooms', 'bathrooms', 'status']:
        if field in data:
            update_fields.append(f"{field} = %s")
            params.append(data[field])
    
    params.append(property_id)
    sql = f"UPDATE Properties SET {', '.join(update_fields)} WHERE property_id = %s"
    cur.execute(sql, params)
    mysql.connection.commit()
    cur.close()
    return jsonify({'status': 'updated'}), 200

# Delete property 🗑️
@app.route('/api/seller/properties/<int:property_id>', methods=['DELETE'])
def delete_property(property_id):
    seller_id = request.args.get('seller_id')
    cur = mysql.connection.cursor()
    
    cur.execute("SELECT seller_id FROM Properties WHERE property_id = %s", 
                (property_id,))
    row = cur.fetchone()
    if not row or row['seller_id'] != int(seller_id):
        cur.close()
        return jsonify({'error': 'Unauthorized'}), 403
    
    cur.execute("DELETE FROM Properties WHERE property_id = %s", (property_id,))
    mysql.connection.commit()
    cur.close()
    return jsonify({'status': 'deleted'}), 200

# Seller dashboard 📊
@app.route('/api/seller/<int:seller_id>/dashboard', methods=['GET'])
def seller_dashboard(seller_id):
    cur = mysql.connection.cursor()
    
    cur.execute("""
        SELECT COUNT(*) AS active_count FROM Properties
        WHERE seller_id = %s AND status = 'Available'
    """, (seller_id,))
    active = cur.fetchone()
    
    cur.execute("""
        SELECT COUNT(*) AS sold_count FROM Properties
        WHERE seller_id = %s AND status = 'Sold'
    """, (seller_id,))
    sold = cur.fetchone()
    
    cur.execute("""
        SELECT * FROM Properties WHERE seller_id = %s 
        ORDER BY listing_date DESC
    """, (seller_id,))
    properties = cur.fetchall()
    
    cur.close()
    return jsonify({
        'active_listings': active['active_count'],
        'sold_listings': sold['sold_count'],
        'properties': properties
    })

### Run Backend 🚀

cd backend
python app.py

**API Lives at:** `http://127.0.0.1:5000/api/`

Test with Postman! 📮

---

## 🎨 Frontend

> **Person 3's Domain** - Where beauty meets function! 🌈

### Folder Structure

frontend/
├─ templates/
│  ├─ base.html                  ← Base template
│  ├─ home.html                  ← Landing page
│  ├─ buyer_search.html          ← Search page
│  ├─ buyer_results.html         ← Results grid
│  ├─ property_detail.html       ← Property showcase
│  ├─ buyer_dashboard.html       ← Buyer dashboard
│  ├─ seller_dashboard.html      ← Seller dashboard
│  ├─ seller_add_property.html   ← Add new property
│  ├─ seller_edit_property.html  ← Edit property
│  ├─ login.html                 ← Auth page
│  └─ register.html              ← Sign up page
├─ static/
│  └─ style.css                  ← Beautiful styles!
└─ app_frontend.py               ← Flask frontend app

### Frontend App

Create `frontend/app_frontend.py`:

from flask import Flask, render_template, request, redirect, url_for, session
import requests

API_BASE = 'http://127.0.0.1:5000/api'
app = Flask(__name__)
app.secret_key = 'your_secret_key_here'

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

@app.route('/property/<int:property_id>')
def property_detail(property_id):
    resp = requests.get(f'{API_BASE}/properties/{property_id}')
    return render_template('property_detail.html', 
                          property=resp.json()) if resp.status_code == 200 else "Not found", 404

@app.route('/buyer/dashboard')
def buyer_dashboard():
    user_id = session.get('user_id')
    if not user_id:
        return redirect(url_for('login'))
    
    resp = requests.get(f'{API_BASE}/buyer/{user_id}/dashboard')
    data = resp.json() if resp.status_code == 200 else {'favorites': [], 'appointments': []}
    return render_template('buyer_dashboard.html', data=data)

@app.route('/seller/dashboard')
def seller_dashboard():
    seller_id = session.get('user_id')
    if not seller_id:
        return redirect(url_for('login'))
    
    resp = requests.get(f'{API_BASE}/seller/{seller_id}/dashboard')
    data = resp.json() if resp.status_code == 200 else {}
    return render_template('seller_dashboard.html', data=data)

@app.route('/seller/add_property', methods=['GET','POST'])
def seller_add_property():
    seller_id = session.get('user_id')
    if not seller_id:
        return redirect(url_for('login'))
    
    if request.method == 'POST':
        payload = {
            'address': request.form.get('address'),
            'city': request.form.get('city'),
            'price': request.form.get('price'),
            'bedrooms': request.form.get('bedrooms'),
            'bathrooms': request.form.get('bathrooms'),
            'area_sqft': request.form.get('area_sqft'),
            'property_type': request.form.get('property_type'),
            'seller_id': seller_id,
            'agent_id': 1
        }
        resp = requests.post(f'{API_BASE}/seller/properties', json=payload)
        return redirect(url_for('seller_dashboard')) if resp.status_code == 201 else None
    
    return render_template('seller_add_property.html')

@app.route('/login', methods=['GET','POST'])
def login():
    if request.method == 'POST':
        email = request.form.get('email')
        user_type = request.form.get('user_type')
        session['user_id'] = 1
        session['user_type'] = user_type
        
        return redirect(url_for('buyer_search')) if user_type == 'Buyer' else redirect(url_for('seller_dashboard'))
    
    return render_template('login.html')

if __name__ == '__main__':
    app.run(port=5001, debug=True)

### Run Frontend 🎨

cd frontend
python app_frontend.py

**Frontend Lives at:** `http://127.0.0.1:5001/`

---

## 💻 GitHub Workflow

> **Everyone participates in version control!** 🤝

### 1️⃣ Create Repo

Create on GitHub: `real-estate-platform`

### 2️⃣ Clone & Branch

git clone https://github.com/<org>/real-estate-platform.git
cd real-estate-platform

# Each person creates their own feature branch
git checkout -b feature/person1-database
git checkout -b feature/person2-backend
git checkout -b feature/person3-frontend
git checkout -b feature/person4-testing

### 3️⃣ Work & Commit

git add .
git commit -m "✨ Add database schema for Properties table"
git push origin feature/person1-database

### 4️⃣ Pull Request & Review

- Open PR on GitHub
- Get reviewed by teammates
- Merge to main

### 5️⃣ Track with Issues 📋

Create Issues for:
- ✅ Tasks
- 🐛 Bugs
- 📚 Documentation

Assign to team members!

---

## ✅ Testing & Documentation

### 🧪 Test Plan

**Database Tests:**
- ✅ Try inserting invalid data → constraints should catch
- ✅ Test foreign key relationships
- ✅ Verify triggers work (price changes logged)
- ✅ Test stored procedures

**API Tests:**
- ✅ Use Postman to test all 15+ endpoints
- ✅ Test with invalid IDs → should return 404
- ✅ Test role-based access (Buyer vs Seller)
- ✅ Test filters (price range, city)

**Frontend Tests:**
- ✅ Buyer search → results → property detail flow
- ✅ Seller add/edit/delete property
- ✅ Login & role routing
- ✅ Responsive design on mobile

### 📖 Deliverables

1. **ER Diagram** - Visual representation of database
2. **API Documentation** - All endpoints with examples
3. **User Manual** - Screenshots + step-by-step guides
4. **Final Report** (15-20 pages):
   - Problem statement
   - Architecture diagram
   - ER model with normalization
   - SQL features (stored procs, triggers)
   - Application walkthrough
   - Testing results
   - Conclusion & future improvements

---

## 📡 API Overview

| Endpoint | Method | Role | Purpose |
|----------|--------|------|---------|
| `/api/properties` | GET | Buyer | 🔍 Search available properties |
| `/api/properties/<id>` | GET | Buyer | 👁️ Property details |
| `/api/favorites` | POST | Buyer | ❤️ Save to favorites |
| `/api/buyer/<id>/favorites` | GET | Buyer | 📋 View saved properties |
| `/api/appointments` | POST | Buyer | 📅 Schedule appointment |
| `/api/buyer/<id>/dashboard` | GET | Buyer | 📊 Dashboard data |
| `/api/seller/properties` | POST | Seller | ➕ Create listing |
| `/api/seller/properties/<id>` | PUT | Seller | ✏️ Update listing |
| `/api/seller/properties/<id>` | DELETE | Seller | 🗑️ Delete listing |
| `/api/seller/<id>/properties` | GET | Seller | 📋 My listings |
| `/api/seller/<id>/dashboard` | GET | Seller | 📊 Dashboard data |

---

## 🎭 Figma Prototype Brief

### Design a Modern Real Estate Platform UI! 🎨

**Style:** Clean, modern, light theme with clear CTAs

**Pages to Design:**

### 🏠 Landing Page
- Hero section with search widget
- Featured properties grid
- Value propositions for buyers & sellers

### 🔍 Buyer – Search & Browse
- Filter sidebar (price slider, beds, property type)
- Property cards grid (image, price, location)
- Sort options

### 📸 Property Detail
- Image carousel
- Key info (price, beds, baths, area)
- Agent contact box
- "Save" & "Schedule" buttons

### 📊 Buyer Dashboard
- Saved favorites
- Upcoming appointments
- Appointment history

### 🏘️ Seller – Dashboard
- Stats cards (active, sold, pending)
- Properties table
- Quick actions (Edit, Remove)

### ➕ Seller – Add/Edit Property
- Form fields for all property details
- Image upload area
- Save/Cancel buttons

### 🔐 Auth Pages
- Login form (role selector)
- Register form
- Clean, minimal design

---

## 🗂️ Folder Structure

real-estate-platform/
│
├─ 📄 README.md                    ← You are here! 🎉
├─ 📄 .gitignore
│
├─ 📁 data/
│  └─ usa_housing.csv             ← Kaggle dataset
│
├─ 📁 scripts/
│  └─ import_data.py              ← Data import script
│
├─ 📁 backend/                     ← Person 2's space ⚙️
│  ├─ app.py
│  └─ requirements.txt
│
├─ 📁 frontend/                    ← Person 3's space 🎨
│  ├─ app_frontend.py
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ home.html
│  │  ├─ buyer_search.html
│  │  ├─ buyer_results.html
│  │  ├─ property_detail.html
│  │  ├─ buyer_dashboard.html
│  │  ├─ seller_dashboard.html
│  │  ├─ seller_add_property.html
│  │  ├─ seller_edit_property.html
│  │  ├─ login.html
│  │  └─ register.html
│  └─ static/
│     └─ style.css
│
└─ 📁 docs/                        ← Person 4's space 📚
   ├─ ER_diagram.png
   ├─ user_manual.md
   └─ final_report.md

---

## ⚡ Quick Start Commands

# 1️⃣ Clone
git clone https://github.com/<org>/real-estate-platform.git
cd real-estate-platform

# 2️⃣ Virtual environment
python -m venv venv
source venv/bin/activate  # macOS/Linux
venv\Scripts\activate     # Windows

# 3️⃣ Install Python dependencies
pip install flask flask-mysqldb flask-cors requests

# 4️⃣ Create MySQL database
mysql -u root -p < scripts/schema.sql

# 5️⃣ Import real data
python scripts/import_data.py

# 6️⃣ Start backend (Terminal 1)
cd backend
python app.py

# 7️⃣ Start frontend (Terminal 2)
cd frontend
python app_frontend.py

# 8️⃣ Open your browser 🌐
# Frontend: http://127.0.0.1:5001
# API: http://127.0.0.1:5000/api

---

## 📋 Checklist Before Launch

- [ ] ✅ Database created with all tables
- [ ] ✅ Kaggle data imported successfully
- [ ] ✅ Backend API running on port 5000
- [ ] ✅ Frontend running on port 5001
- [ ] ✅ API endpoints tested with Postman
- [ ] ✅ All team members have access to
