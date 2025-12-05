POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({property_id: propertyId})
    })
    .then(r => r.json())
    .then(data => {
        alert(data.message || 'Added to favorites!');
        location.reload();
    })
    .catch(e => alert('Error: ' + e));
}
</script>
{% endblock %}
```

Create file: `frontend/templates/property_detail.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container my-5">
    <div class="row">
        <div class="col-md-8">
            <!-- Image Carousel -->
            {% if property.images %}
                <div id="propertyCarousel" class="carousel slide mb-4">
                    <div class="carousel-inner">
                        {% for image in property.images %}
                            <div class="carousel-item {% if loop.first %}active{% endif %}">
                                <img src="{{ image.image_url }}" class="d-block w-100" style="height: 400px; object-fit: cover;">
                            </div>
                        {% endfor %}
                    </div>
                    <button class="carousel-control-prev" type="button" data-bs-target="#propertyCarousel" data-bs-slide="prev">
                        <span class="carousel-control-prev-icon"></span>
                    </button>
                    <button class="carousel-control-next" type="button" data-bs-target="#propertyCarousel" data-bs-slide="next">
                        <span class="carousel-control-next-icon"></span>
                    </button>
                </div>
            {% endif %}
            
            <!-- Property Details -->
            <div class="card mb-4">
                <div class="card-body">
                    <h1 class="card-title mb-3">${{ "%.2f"|format(property.price) }}</h1>
                    <p class="lead">{{ property.address }}, {{ property.city }}, {{ property.state }} {{ property.zipcode }}</p>
                    
                    <hr>
                    
                    <div class="row">
                        <div class="col-md-3">
                            <h5>Bedrooms</h5>
                            <p class="fs-4">{{ property.bedrooms }}</p>
                        </div>
                        <div class="col-md-3">
                            <h5>Bathrooms</h5>
                            <p class="fs-4">{{ "%.1f"|format(property.bathrooms) }}</p>
                        </div>
                        <div class="col-md-3">
                            <h5>Square Feet</h5>
                            <p class="fs-4">{{ property.area_sqft }}</p>
                        </div>
                        <div class="col-md-3">
                            <h5>Type</h5>
                            <p class="fs-4">{{ property.property_type }}</p>
                        </div>
                    </div>
                    
                    <hr>
                    
                    <h4>Description</h4>
                    <p>{{ property.description or 'No description provided' }}</p>
                    
                    {% if property.price_history %}
                        <h4 class="mt-4">Price History</h4>
                        <table class="table">
                            <thead>
                                <tr>
                                    <th>Old Price</th>
                                    <th>New Price</th>
                                    <th>Date Changed</th>
                                </tr>
                            </thead>
                            <tbody>
                                {% for history in property.price_history %}
                                    <tr>
                                        <td>${{ "%.2f"|format(history.old_price) }}</td>
                                        <td>${{ "%.2f"|format(history.new_price) }}</td>
                                        <td>{{ history.change_date }}</td>
                                    </tr>
                                {% endfor %}
                            </tbody>
                        </table>
                    {% endif %}
                </div>
            </div>
        </div>
        
        <!-- Agent & Actions Sidebar -->
        <div class="col-md-4">
            <!-- Agent Info -->
            <div class="card mb-4">
                <div class="card-header bg-primary text-white">
                    <h5 class="mb-0">Agent Information</h5>
                </div>
                <div class="card-body">
                    <h6>{{ property.agent_name or 'No Agent' }}</h6>
                    {% if property.agent_phone %}
                        <p><i class="bi bi-telephone"></i> {{ property.agent_phone }}</p>
                    {% endif %}
                    {% if property.agent_email %}
                        <p><i class="bi bi-envelope"></i> {{ property.agent_email }}</p>
                    {% endif %}
                </div>
            </div>
            
            <!-- Actions -->
            <div class="card">
                <div class="card-body">
                    <button class="btn btn-danger w-100 mb-2" onclick="addToFavorites({{ property.property_id }})">
                        ❤️ Save to Favorites
                    </button>
                    <button class="btn btn-success w-100" data-bs-toggle="modal" data-bs-target="#appointmentModal">
                        📅 Schedule Tour
                    </button>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- Schedule Appointment Modal -->
<div class="modal fade" id="appointmentModal" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Schedule a Tour</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <form onsubmit="scheduleAppointment(event, {{ property.property_id }})">
                <div class="modal-body">
                    <div class="mb-3">
                        <label for="appointmentDate" class="form-label">Date & Time</label>
                        <input type="datetime-local" class="form-control" id="appointmentDate" required>
                    </div>
                    <div class="mb-3">
                        <label for="appointmentType" class="form-label">Type</label>
                        <select class="form-select" id="appointmentType">
                            <option value="In-Person">In-Person</option>
                            <option value="Virtual Tour">Virtual Tour</option>
                            <option value="Phone">Phone Call</option>
                        </select>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="submit" class="btn btn-primary">Schedule Tour</button>
                </div>
            </form>
        </div>
    </div>
</div>

<script>
function addToFavorites(propertyId) {
    fetch('/api/favorites', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({property_id: propertyId})
    })
    .then(r => r.json())
    .then(data => alert(data.message || 'Added!'))
    .catch(e => alert('Error: ' + e));
}

function scheduleAppointment(e, propertyId) {
    e.preventDefault();
    const dateInput = document.getElementById('appointmentDate').value;
    const type = document.getElementById('appointmentType').value;
    
    fetch('/api/appointments', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
            property_id: propertyId,
            appointment_date: new Date(dateInput).toISOString(),
            appointment_type: type
        })
    })
    .then(r => r.json())
    .then(data => {
        alert(data.message || 'Appointment scheduled!');
        bootstrap.Modal.getInstance(document.getElementById('appointmentModal')).hide();
        location.reload();
    })
    .catch(e => alert('Error: ' + e));
}
</script>
{% endblock %}
```

Create file: `frontend/templates/buyer_dashboard.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container my-5">
    <h2 class="mb-4">My Dashboard</h2>
    
    <!-- Tabs -->
    <ul class="nav nav-tabs mb-4">
        <li class="nav-item">
            <a class="nav-link active" data-bs-toggle="tab" href="#favorites">❤️ Favorites</a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="tab" href="#appointments">📅 Appointments</a>
        </li>
    </ul>
    
    <div class="tab-content">
        <!-- Favorites Tab -->
        <div id="favorites" class="tab-pane fade show active">
            {% if data.favorites %}
                <div class="row g-4">
                    {% for fav in data.favorites %}
                        <div class="col-md-6 col-lg-4">
                            <div class="card h-100">
                                {% if fav.image_url %}
                                    <img src="{{ fav.image_url }}" class="card-img-top" style="height: 200px; object-fit: cover;">
                                {% endif %}
                                <div class="card-body">
                                    <h5 class="card-title">${{ "%.2f"|format(fav.price) }}</h5>
                                    <p class="card-text">{{ fav.address }}, {{ fav.city }}</p>
                                    <p class="card-text small">{{ fav.bedrooms }} bed | {{ fav.bathrooms }} bath</p>
                                    <a href="{{ url_for('property_detail', property_id=fav.property_id) }}" class="btn btn-primary btn-sm">View</a>
                                </div>
                            </div>
                        </div>
                    {% endfor %}
                </div>
            {% else %}
                <div class="alert alert-info">No favorites yet. <a href="{{ url_for('buyer_search') }}">Start searching</a></div>
            {% endif %}
        </div>
        
        <!-- Appointments Tab -->
        <div id="appointments" class="tab-pane fade">
            {% if data.appointments %}
                <div class="table-responsive">
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th>Property</th>
                                <th>Date & Time</th>
                                <th>Agent</th>
                                <th>Status</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for appt in data.appointments %}
                                <tr>
                                    <td>
                                        <a href="{{ url_for('property_detail', property_id=appt.property_id) }}">
                                            {{ appt.address }}, {{ appt.city }}
                                        </a>
                                    </td>
                                    <td>{{ appt.appointment_date }}</td>
                                    <td>{{ appt.agent_name or 'N/A' }}</td>
                                    <td>
                                        <span class="badge bg-{% if appt.status == 'Scheduled' %}info{% elif appt.status == 'Completed' %}success{% else %}warning{% endif %}">
                                            {{ appt.status }}
                                        </span>
                                    </td>
                                </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            {% else %}
                <div class="alert alert-info">No appointments scheduled yet.</div>
            {% endif %}
        </div>
    </div>
</div>
{% endblock %}
```

Create file: `frontend/templates/seller_dashboard.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container my-5">
    <h2 class="mb-4">Seller Dashboard</h2>
    
    <!-- Statistics Cards -->
    <div class="row mb-4">
        <div class="col-md-3">
            <div class="card text-white bg-primary">
                <div class="card-body">
                    <h5 class="card-title">Active Listings</h5>
                    <p class="card-text fs-4">{{ data.stats.active_listings or 0 }}</p>
                </div>
            </div>
        </div>
        <div class="col-md-3">
            <div class="card text-white bg-warning">
                <div class="card-body">
                    <h5 class="card-title">Pending</h5>
                    <p class="card-text fs-4">{{ data.stats.pending_listings or 0 }}</p>
                </div>
            </div>
        </div>
        <div class="col-md-3">
            <div class="card text-white bg-success">
                <div class="card-body">
                    <h5 class="card-title">Sold</h5>
                    <p class="card-text fs-4">{{ data.stats.sold_listings or 0 }}</p>
                </div>
            </div>
        </div>
        <div class="col-md-3">
            <div class="card text-white bg-info">
                <div class="card-body">
                    <h5 class="card-title">Total Sales</h5>
                    <p class="card-text fs-4">${{ "%.0f"|format(data.stats.total_sales_value or 0) }}</p>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Add Property Button -->
    <a href="{{ url_for('seller_add_property') }}" class="btn btn-primary mb-4">➕ Add New Property</a>
    
    <!-- Properties Table -->
    {% if data.properties %}
        <div class="table-responsive">
            <table class="table table-striped">
                <thead>
                    <tr>
                        <th>Address</th>
                        <th>Price</th>
                        <th>Beds</th>
                        <th>Status</th>
                        <th>Views</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    {% for prop in data.properties %}
                        <tr>
                            <td>{{ prop.address }}, {{ prop.city }}</td>
                            <td>${{ "%.2f"|format(prop.price) }}</td>
                            <td>{{ prop.bedrooms }}</td>
                            <td>
                                <span class="badge bg-{% if prop.status == 'Available' %}success{% elif prop.status == 'Pending' %}warning{% else %}secondary{% endif %}">
                                    {{ prop.status }}
                                </span>
                            </td>
                            <td>{{ prop.view_count }}</td>
                            <td>
                                <a href="{{ url_for('seller_edit_property', property_id=prop.property_id) }}" class="btn btn-sm btn-warning">✏️ Edit</a>
                            </td>
                        </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    {% else %}
        <div class="alert alert-info">No properties yet. <a href="{{ url_for('seller_add_property') }}">Add one now</a></div>
    {% endif %}
</div>
{% endblock %}
```

Create file: `frontend/templates/seller_add_property.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container my-5">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card shadow">
                <div class="card-body p-5">
                    <h2 class="card-title mb-4">Add New Property</h2>
                    
                    {% if error %}
                        <div class="alert alert-danger">{{ error }}</div>
                    {% endif %}
                    
                    <form method="POST">
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label for="address" class="form-label">Address *</label>
                                <input type="text" class="form-control" id="address" name="address" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label for="city" class="form-label">City *</label>
                                <input type="text" class="form-control" id="city" name="city" required>
                            </div>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label for="state" class="form-label">State *</label>
                                <input type="text" class="form-control" id="state" name="state" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label for="zipcode" class="form-label">Zipcode *</label>
                                <input type="text" class="form-control" id="zipcode" name="zipcode" required>
                            </div>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label for="price" class="form-label">Price *</label>
                                <input type="number" class="form-control" id="price" name="price" step="0.01" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label for="property_type" class="form-label">Type *</label>
                                <select class="form-select" id="property_type" name="property_type" required>
                                    <option selected disabled>Choose...</option>
                                    <option value="House">House</option>
                                    <option value="Apartment">Apartment</option>
                                    <option value="Condo">Condo</option>
                                    <option value="Villa">Villa</option>
                                    <option value="Townhouse">Townhouse</option>
                                </select>
                            </div>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-4 mb-3">
                                <label for="bedrooms" class="form-label">Bedrooms *</label>
                                <input type="number" class="form-control" id="bedrooms" name="bedrooms" required>
                            </div>
                            <div class="col-md-4 mb-3">
                                <label for="bathrooms" class="form-label">Bathrooms *</label>
                                <input type="number" class="form-control" id="bathrooms" name="bathrooms" step="0.5" required>
                            </div>
                            <div class="col-md-4 mb-3">
                                <label for="area_sqft" class="form-label">Square Feet *</label>
                                <input type="number" class="form-control" id="area_sqft" name="area_sqft" required>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <label for="description" class="form-label">Description</label>
                            <textarea class="form-control" id="description" name="description" rows="4"></textarea>
                        </div>
                        
                        <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                            <a href="{{ url_for('seller_dashboard') }}" class="btn btn-secondary">Cancel</a>
                            <button type="submit" class="btn btn-primary">Add Property</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

### **DAYS 16-20: Integration & Testing**

Create file: `frontend/static/css/style.css`

```css
:root {
    --primary-color: #007bff;
    --secondary-color: #6c757d;
    --success-color: #28a745;
    --danger-color: #dc3545;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background-color: #f8f9fa;
}

.navbar {
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.hero-section {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 400px;
    display: flex;
    align-items: center;
}

.feature-box {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.feature-box:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 25px rgba(0,0,0,0.1);
}

.property-card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.property-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 25px rgba(0,0,0,0.15);
}

.card {
    border: none;
    border-radius: 8px;
}

.btn {
    border-radius: 6px;
    font-weight: 500;
}

footer {
    margin-top: auto;
}

@media (max-width: 768px) {
    .hero-section {
        min-height: 250px;
    }
}
```

Run the frontend:

```bash
cd frontend
python app_frontend.py

# Output should show:
# 🚀 Real Estate Frontend Starting...
# 🔗 Running on http://127.0.0.1:5001
```

---

### **Your Final Deliverables:**
✅ `frontend/app_frontend.py` - Flask frontend app  
✅ 8+ HTML templates with Bootstrap  
✅ Responsive design (mobile + desktop)  
✅ API integration complete  
✅ User authentication UI  
✅ Buyer & Seller dashboards  
✅ Property search & filtering  
✅ CSS styling (`static/css/style.css`)

---

## 🧪 Person 4: QA & DevOps (Days 1-20)

### 📌 Your Role
Ensure everything works. Test all features and document everything.

---

### **DAYS 1-5: Setup & Test Planning**

#### Day 1-2: Create Test Plan

Create file: `docs/TEST_PLAN.md`

```markdown
# Real Estate Platform - Test Plan

## Testing Strategy

### 1. Database Testing
- [ ] All tables created successfully
- [ ] Primary keys and foreign keys enforced
- [ ] Data constraints working (CHECK constraints)
- [ ] Triggers fire on INSERT/UPDATE
- [ ] Stored procedures execute correctly

### 2. API Testing (15+ Endpoints)

#### Authentication
- [ ] POST /api/auth/register - Create new user
- [ ] POST /api/auth/login - User login
- [ ] Invalid credentials return 401

#### Buyer Endpoints
- [ ] GET /api/properties - List properties
- [ ] GET /api/properties?city=Los Angeles&min_price=100000&max_price=500000 - Filter
- [ ] GET /api/properties/<id> - Property details
- [ ] POST /api/favorites - Save to favorites
- [ ] GET /api/buyer/<id>/dashboard - View dashboard
- [ ] POST /api/appointments - Schedule appointment

#### Seller Endpoints
- [ ] POST /api/seller/properties - Create listing
- [ ] PUT /api/seller/properties/<id> - Update listing
- [ ] DELETE /api/seller/properties/<id> - Delete listing
- [ ] GET /api/seller/<id>/dashboard - Seller stats

#### Admin Endpoints
- [ ] GET /api/admin/dashboard - View analytics

### 3. Frontend Testing
- [ ] Landing page loads
- [ ] User can register (Buyer)
- [ ] User can register (Seller)
- [ ] User can login
- [ ] Search properties works with filters
- [ ] Property detail page displays correctly
- [ ] Buyer can save favorites
- [ ] Buyer can schedule appointments
- [ ] Seller can add property
- [ ] Seller can edit property
- [ ] Seller can delete property
- [ ] Mobile responsive design

### 4. Integration Testing
- [ ] Frontend → Backend → Database full flow
- [ ] User can search → view details → save → schedule
- [ ] Seller can create → edit → delete properties
- [ ] Price changes logged to Price_History
- [ ] Appointments created correctly

### 5. Security Testing
- [ ] Only logged in users can access protected routes
- [ ] Sellers can only edit own properties
- [ ] JWT tokens properly validated
- [ ] SQL injection prevention

### 6. Performance Testing
- [ ] Properties search returns < 2 seconds
- [ ] Pagination works with 1000+ records
- [ ] Indices improve query speed
```

#### Day 3: Install Testing Tools

```bash
# Install Postman
# Download from https://www.postman.com/downloads/

# OR install via terminal
# macOS:
# brew install postman

# Create requests collection for testing
```

#### Day 4-5: Create Postman Collection

Create file: `docs/Postman_Collection.json`

```json
{
  "info": {
    "name": "Real Estate API",
    "description": "Complete API test collection",
    "version": "1.0.0"
  },
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Register",
          "request": {
            "method": "POST",
            "url": "http://localhost:5000/api/auth/register",
            "header": [
              {"key": "Content-Type", "value": "application/json"}
            ],
            "body": {
              "raw": "{\"name\":\"John Buyer\",\"email\":\"john@test.com\",\"password\":\"test123\",\"user_type\":\"Buyer\"}"
            }
          }
        },
        {
          "name": "Login",
          "request": {
            "method": "POST",
            "url": "http://localhost:5000/api/auth/login",
            "header": [
              {"key": "Content-Type", "value": "application/json"}
            ],
            "body": {
              "raw": "{\"email\":\"john@test.com\",\"password\":\"test123\"}"
            }
          }
        }
      ]
    },
    {
      "name": "Properties",
      "item": [
        {
          "name": "List Properties",
          "request": {
            "method": "GET",
            "url": "http://localhost:5000/api/properties"
          }
        },
        {
          "name": "Search with Filters",
          "request": {
            "method": "GET",
            "url": "http://localhost:5000/api/properties?city=Los Angeles&min_price=100000&max_price=500000&bedrooms=2"
          }
        },
        {
          "name": "Get Property Details",
          "request": {
            "method": "GET",
            "url": "http://localhost:5000/api/properties/1"
          }
        }
      ]
    }
  ]
}
```

---

### **DAYS 6-15: Comprehensive Testing**

#### Day 6-10: API Testing with Postman

```bash
# Start MySQL
mysql -u root -p

# Start Backend
cd backend
python app.py

# In Postman, run each endpoint:

# 1. Register Buyer
POST http://localhost:5000/api/auth/register
{
  "name": "John Buyer",
  "email": "john@test.com",
  "password": "test123",
  "user_type": "Buyer"
}

# Copy token from response

# 2. Register Seller
POST http://localhost:5000/api/auth/register
{
  "name": "Jane Seller",
  "email": "jane@test.com",
  "password": "test123",
  "user_type": "Seller"
}

# 3. Test all 15+ endpoints with valid and invalid data
# Check response codes:
# 200 = Success
# 201 = Created
# 400 = Bad Request
# 401 = Unauthorized
# 403 = Forbidden
# 404 = Not Found
# 500 = Server Error
```

#### Day 11-15: Frontend Testing

Manually test all user flows:

```
Buyer Flow:
1. Register as Buyer ✓
2. Login ✓
3. Search properties ✓
4. Filter by city, price, bedrooms ✓
5. View property details ✓
6. Save to favorites ✓
7. Schedule appointment ✓
8. View dashboard with favorites ✓
9. View appointments ✓

Seller Flow:
1. Register as Seller ✓
2. Login ✓
3. Add property ✓
4. Edit property ✓
5. Delete property ✓
6. View dashboard with stats ✓
7. View all properties ✓

Mobile Testing:
1. Test on mobile browser (iPhone/Android)
2. Check responsive layout
3. Test touch interactions
4. Check form inputs
```

---

### **DAYS 16-18: Create Documentation**

#### Create file: `docs/API_DOCUMENTATION.md`

```markdown
# Real Estate API Documentation

## Base URL
`http://localhost:5000/api`

## Authentication
All protected endpoints require JWT token:
```
Header: Authorization: Bearer <TOKEN>
```

## Endpoints

### Authentication

#### Register
```
POST /auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "user_type": "Buyer"
}

Response:
{
  "message": "User registered successfully",
  "user_id": 1,
  "token": "eyJhbGc...",
  "user_type": "Buyer"
}
```

#### Login
```
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}

Response:
{
  "message": "Login successful",
  "user_id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "user_type": "Buyer",
  "token": "eyJhbGc..."
}
```

### Properties

#### List Properties
```
GET /properties?city=Los Angeles&min_price=100000&max_price=500000&bedrooms=2&page=1&per_page=20

Response:
{
  "properties": [
    {
      "property_id": 1,
      "address": "123 Main St",
      "city": "Los Angeles",
      "price": 350000,
      "bedrooms": 3,
      "bathrooms": 2,
      "area_sqft": 2000,
      "status": "Available"
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 150,
    "pages": 8
  }
}
```

#### Get Property Details
```
GET /properties/1

Response:
{
  "property_id": 1,
  "address": "123 Main St",
  "city": "Los Angeles",
  "price": 350000,
  "bedrooms": 3,
  "bathrooms": 2,
  "agent_name": "John Smith",
  "agent_phone": "555-0001",
  "images": [...],
  "price_history": [...]
}
```

### Favorites

#### Add to Favorites
```
POST /favorites
Authorization: Bearer <TOKEN>
Content-Type: application/json

{
  "property_id": 1
}

Response: {"message": "Added to favorites"}
```

### Appointments

#### Schedule Appointment
```
POST /appointments
Authorization: Bearer <TOKEN>
Content-Type: application/json

{
  "property_id": 1,
  "appointment_date": "2025-01-15T14:00:00",
  "appointment_type": "In-Person"
}

Response: {"message": "Appointment scheduled", "appointment_id": 5}
```

### Seller Endpoints

#### Create Property
```
POST /seller/properties
Authorization: Bearer <TOKEN>
Content-Type: application/json

{
  "address": "456 Oak Ave",
  "city": "Los Angeles",
  "state": "CA",
  "zipcode": "90001",
  "price": 450000,
  "bedrooms": 4,
  "bathrooms": 3,
  "area_sqft": 3000,
  "property_type": "House"
}

Response: {"message": "Property created", "property_id": 102}
```

#### Update Property
```
PUT /seller/properties/102
Authorization: Bearer <TOKEN>
Content-Type: application/json

{
  "price": 475000,
  "status": "Pending"
}

Response: {"message": "Property updated"}
```

#### Delete Property
```
DELETE /seller/properties/102
Authorization: Bearer <TOKEN>

Response: {"message": "Property deleted"}
```

---

### **DAYS 19-20: Final Report & Documentation**

#### Create file: `docs/FINAL_REPORT.md`

```markdown
# Real Estate Platform - Final Development Report

## Executive Summary
Successfully developed a full-stack real estate management platform in 20 days with a 4-person team.

## Project Scope
- Complete database with 8 tables
- REST API with 15+ endpoints
- User authentication and role-based access
- Responsive web frontend
- Integration with real Kaggle housing data

## Architecture Overview

### Tech Stack
- **Frontend:** Flask, HTML5, CSS3, JavaScript, Bootstrap 5
- **Backend:** Python, Flask, Flask-MySQLdb
- **Database:** MySQL 8.x
- **Authentication:** JWT tokens
- **Real Data:** Kaggle USA Housing Dataset (1000+ properties)

### Database Schema
8 tables with proper normalization:
- Users (Buyers, Sellers, Admins)
- Agents
- Properties
- Property_Images
- Appointments
- Favorites
- Transactions
- Price_History

### API Endpoints (15+)
✅ 4 Authentication endpoints
✅ 6 Buyer endpoints
✅ 5 Seller endpoints

## Team Contributions

### Person 1 - Database Architect
- Designed ER diagram
- Created complete schema with constraints
- Implemented stored procedures and triggers
- Imported 1000+ real properties from Kaggle
- Created indices for performance optimization

### Person 2 - Backend Engineer
- Built Flask REST API
- Implemented JWT authentication
- Developed 15+ API endpoints
- Added role-based access control
- Integrated with MySQL database

### Person 3 - Frontend Developer
- Created 8+ responsive HTML templates
- Built buyer search and property detail pages
- Developed seller dashboard
- Implemented user dashboards
- Created authentication UI

### Person 4 - QA & DevOps
- Created comprehensive test plan
- Tested all API endpoints with Postman
- Performed UI/UX testing
- Created API documentation
- Generated this final report

## Testing Results

### Database Testing: PASSED ✅
- All tables created successfully
- Constraints enforced
- Triggers working
- Stored procedures functioning
- Data integrity verified

### API Testing: PASSED ✅
- All 15+ endpoints tested
- Response codes correct
- Error handling working
- Authorization implemented
- Data validation in place

### Frontend Testing: PASSED ✅
- All pages loading correctly
- Search and filters working
- User flows complete
- Mobile responsive
- No console errors

### Integration Testing: PASSED ✅
- Frontend → API → Database complete
- User registration and login working
- Property search and filtering working
- Favorite saving working
- Appointment scheduling working

## Performance Metrics
- Database queries: < 500ms
- API response time: < 1 second
- Page load time: < 2 seconds
- Supports 1000+ properties with pagination

## Deliverables Checklist

### Database (Person 1)
- [x] Schema creation script (scripts/schema.sql)
- [x] Stored procedures (scripts/stored_procedures.sql)
- [x] Triggers (scripts/triggers.sql)
- [x] Data import script (scripts/import_data.py)
- [x] ER diagram (docs/ER_DIAGRAM.md)
- [x] 1000+ real properties imported
- [x] Indices created for performance

### Backend (Person 2)
- [x] Flask app (backend/app.py)
- [x] 15+ API endpoints
- [x] Authentication (register/login)
- [x] Role-based access control
- [x] Error handling
- [x] Request validation
- [x] Requirements.txt (backend/requirements.txt)

### Frontend (Person 3)
- [x] 8+ HTML templates
- [x] Bootstrap styling
- [x] Responsive design
- [x] API integration
- [x] User authentication UI
- [x] Buyer dashboard
- [x] Seller dashboard
- [x] Property search
- [x] CSS file (frontend/static/css/style.css)

### Documentation & QA (Person 4)
- [x] Test plan (docs/TEST_PLAN.md)
- [x] API documentation (docs/API_DOCUMENTATION.md)
- [x] Postman collection (docs/Postman_Collection.json)
- [x] User manual (docs/USER_MANUAL.md)
- [x] This final report

## Known Limitations & Future Improvements

### Current Limitations
1. Basic authentication (use bcrypt for production)
2. No image upload (uses URLs)
3. No email notifications
4. No admin panel

### Future Improvements
1. [ ] Implement image upload with cloud storage (AWS S3)
2. [ ] Add email notifications (SendGrid)
3. [ ] Create admin dashboard
4. [ ] Add property reviews and ratings
5. [ ] Implement real-time notifications (WebSockets)
6. [ ] Add mortgage calculator
7. [ ] Deploy to production (Heroku/AWS)
8. [ ] Add automated testing (pytest)
9. [ ] Implement caching (Redis)
10. [ ] Add analytics dashboard

## Conclusion
The Real Estate Platform has been successfully developed with all core features implemented and tested. The system is ready for demonstration and can serve as a foundation for production development.

---

**Report Generated:** December 2024
**Team:** 4 Developers
**Timeline:** 20 Days
**Status:** ✅ COMPLETE
```

#### Create file: `docs/USER_MANUAL.md`

```markdown
# Real Estate Platform - User Manual

## For Buyers

### Getting Started
1. Go to http://127.0.0.1:5001
2. Click "Register"
3. Fill in your details and select "Buyer"
4. Click "Register"

### Searching Properties
1. Click "Search" in navigation
2. Use filters to narrow down:
   - City
   - Price range
   - Minimum bedrooms
   - Property type
3. Click "Search" button

### Viewing Property Details
1. Click on any property card
2. See full details, images, and agent info
3. Review price history

### Saving Favorites
1. On property detail page, click "❤️ Save to Favorites"
2. View all favorites in Dashboard

### Scheduling Tours
1. On property detail page, click "📅 Schedule Tour"
2. Select date, time, and type
3. Click "Schedule Tour"
4. View appointments in Dashboard

## For Sellers

### Getting Started
1. Go to http://127.0.0.1:5001
2. Click "Register"
3. Fill in your details and select "Seller"
4. Click "Register"

### Adding Properties
1. Click "Dashboard" or "Add Property"
2. Fill in all property details:
   - Address, City, State, Zipcode
   - Price
   - Bedrooms, Bathrooms
   - Square footage
   - Description
3. Click "Add Property"

### Managing Listings
1. View all listings in "Seller Dashboard"
2. See statistics (Active, Pending, Sold)
3. Click "✏️ Edit" to modify property
4. Update price, status, etc.
5. See number of viewing requests

### Monitoring Interest
- Dashboard shows viewing count for each property
- See scheduled appointments from interested buyers

## Troubleshooting

### "Cannot Login"
- Check email and password
- Make sure you registered first
- Clear browser cookies

### "Property Not Found"
- Property may have been deleted
- Go back to search

### "Authorization Failed"
- You may not have permission
- Sellers can only edit their own properties
- Try logging out and logging back in

### "Connection Error"
- Make sure backend is running: `python backend/app.py`
- Make sure MySQL is running
- Check internet connection
```

---

### **DAYS 19-20: Final Review & Launch**

#### Day 19: Full System Test

Checklist before launch:

```bash
# 1. Start MySQL
mysql -u root -p

# 2. Create database and import schema
mysql -u root -p real_estate_db < scripts/schema.sql

# 3. Import data
python scripts/import_data.py

# 4. Terminal 1: Start Backend
cd backend
python app.py
# Check: http://127.0.0.1:5000/api/health

# 5. Terminal 2: Start Frontend
cd frontend
python app_frontend.py
# Check: http://127.0.0.1:5001

# 6. In Browser, test complete user flows:
# - Register as Buyer
# - Search properties
# - Save to favorites
# - Schedule appointment
# - View dashboard
# - Register as Seller
# - Add property
# - View seller dashboard
```

#### Day 20: Final Checks & Documentation

```bash
# Final checks:
- [ ] All files committed to GitHub
- [ ] README.md complete
- [ ] All documentation generated
- [ ] No console errors
- [ ] All features working
- [ ] Database clean
- [ ] API responding
- [ ] Frontend responsive

# Create README at root:
touch README.md

# Add content (copy from this document)
# Commit everything:
git add .
git commit -m "🎉 Final Real Estate Platform Release"
git push origin main
```

---

## 📋 Final Checklist

### ✅ Database & Backend
- [x] MySQL database created (real_estate_db)
- [x] All 8 tables created with constraints
- [x] Indices created for performance
- [x] Stored procedures working
- [x] Triggers implemented
- [x] 1000+ properties imported from Kaggle
- [x] Flask API running on port 5000
- [x] 15+ endpoints implemented
- [x] JWT authentication working
- [x] Role-based access control implemented

### ✅ Frontend
- [x] Flask frontend running on port 5001
- [x] 8+ HTML templates created
- [x] Bootstrap styling applied
- [x] Responsive design (mobile + desktop)
- [x] API integration complete
- [x] Authentication UI working
- [x] Buyer search and dashboard
- [x] Seller dashboard and property management
- [x] CSS file created

### ✅ Testing & Documentation
- [x] Complete test plan created
- [x] All API endpoints tested
- [x] Postman collection created
- [x] API documentation complete
- [x] User manual written
- [x] This 20-day sprint guide
- [x] Final report generated
- [x] ER diagram documented

### ✅ Deployment Ready
- [x] GitHub repository set up
- [x] All code committed
- [x] .gitignore configured
- [x] Requirements.txt files complete
- [x] Environment variables set
- [x] No hardcoded secrets
- [x] Error handling implemented
- [x] Logging added

---

## 🎯 Quick Commands Reference

```bash
# Clone repo
git clone https://github.com/<org>/real-estate-platform.git
cd real-estate-platform

# Setup
python -m venv venv
source venv/bin/activate
pip install -r backend/requirements.txt
pip install -r frontend/requirements.txt

# Database
mysql -u root -p real_estate_db < scripts/schema.sql
python scripts/import_data.py

# Run (Terminal 1)
cd backend && python app.py

# Run (Terminal 2)
cd frontend && python app_frontend.py

# Visit
# Frontend: http://127.0.0.1:5001
# Backend API: http://127.0.0.1:5000/api
```

---

## 🏆 Success Criteria - ALL MET ✅

✅ Full-stack application built in 20 days  
✅ 4-person team collaboration  
✅ Real Kaggle housing data (1000+ properties)  
✅ Complete database with 8 tables  
✅ 15+ working API endpoints  
✅ Responsive web frontend  
✅ User authentication & authorization  
✅ Comprehensive testing completed  
✅ Full documentation provided  
✅ Production-ready code  

---

**🎉 REAL ESTATE PLATFORM COMPLETE!**

Your team has built an amazing full-stack application! Celebrate and prepare for the demo! 🚀# Real Estate Platform - Complete 20 Day Development Plan

**Project:** Full-Stack Real Estate Management Platform  
**Team Size:** 4 Developers  
**Timeline:** 20 Days  
**Tech Stack:** Python + Flask + MySQL  
**Status:** Active Development

---

## 📋 Table of Contents

1. [Quick Start](#quick-start)
2. [20-Day Sprint Overview](#20-day-sprint-overview)
3. [Person 1: Database Architect](#person-1-database-architect-days-1-20)
4. [Person 2: Backend Engineer](#person-2-backend-engineer-days-1-20)
5. [Person 3: Frontend Developer](#person-3-frontend-developer-days-1-20)
6. [Person 4: QA & DevOps](#person-4-qa--devops-days-1-20)
7. [Final Checklist](#final-checklist)

---

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/<org>/real-estate-platform.git
cd real-estate-platform

# Create virtual environment
python -m venv venv
source venv/bin/activate  # macOS/Linux
venv\Scripts\activate     # Windows

# Install dependencies
pip install flask flask-mysqldb flask-cors requests

# Start MySQL Server and create database
mysql -u root -p
CREATE DATABASE real_estate_db;

# Import schema
mysql -u root -p real_estate_db < scripts/schema.sql

# Import data
python scripts/import_data.py

# Terminal 1: Start Backend (Port 5000)
cd backend
python app.py

# Terminal 2: Start Frontend (Port 5001)
cd frontend
python app_frontend.py

# Open browser
# Frontend: http://127.0.0.1:5001
# API: http://127.0.0.1:5000/api
```

---

## 📅 20-Day Sprint Overview

| Days | Focus | Person 1 | Person 2 | Person 3 | Person 4 |
|------|-------|----------|----------|----------|----------|
| 1-3 | Setup & Planning | ER Diagram | Flask Setup | Figma Design | Test Plan |
| 4-7 | Database & Foundation | Schema & Indices | Auth Routes | Mockups | Postman Setup |
| 8-11 | API Development | Data Import | Build Endpoints | UI Design | API Testing |
| 12-15 | Integration | Optimization | Bug Fixes | Frontend Pages | Frontend Test |
| 16-18 | Testing | Support | Final Testing | Integration | Comprehensive Test |
| 19-20 | Documentation | Final Review | Deploy | Deploy | Report & Docs |

---

## 👤 Person 1: Database Architect (Days 1-20)

### 📌 Your Role
Design and maintain the database. You own the data layer that powers everything.

---

### **DAYS 8-11: API Endpoint Testing**

Run the backend:

```bash
cd backend
python app.py

# Output should show:
# 🚀 Real Estate API Starting...
# 🔗 Running on http://127.0.0.1:5000
```

Test with cURL or Postman:

```bash
# Test Health Endpoint
curl http://localhost:5000/api/health

# Register User
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"John Buyer","email":"john@example.com","password":"password123","user_type":"Buyer"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"john@example.com","password":"password123"}'

# Search Properties
curl "http://localhost:5000/api/properties?city=Los%20Angeles&min_price=100000&max_price=500000&bedrooms=2"

# Get Property Details
curl http://localhost:5000/api/properties/1

# Add to Favorites (with token)
curl -X POST http://localhost:5000/api/favorites \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"property_id":1}'

# Schedule Appointment
curl -X POST http://localhost:5000/api/appointments \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"property_id":1,"appointment_date":"2025-01-15 14:00:00"}'
```

---

### **DAYS 12-20: Testing & Optimization**

- Work with Person 4 on comprehensive API testing
- Fix bugs found during testing
- Optimize slow queries
- Add logging and error handling
- Final code review and documentation

### **Your Final Deliverables:**
✅ `backend/app.py` - Complete Flask API  
✅ `backend/requirements.txt` - Dependencies  
✅ 15+ working API endpoints  
✅ Authentication & role-based access  
✅ Proper error handling  
✅ API documentation

---

## 🎨 Person 3: Frontend Developer (Days 1-20)

### 📌 Your Role
Build the user-facing web application. You make it beautiful and functional.

---

### **DAYS 1-3: Design & Planning**

#### Day 1-2: Create Figma Prototype (Reference)

Key Pages to Design:
- Landing page with hero section
- Buyer search page
- Property detail page
- Buyer dashboard
- Seller dashboard
- Add/Edit property form
- Login/Register pages

#### Day 3: Setup Frontend Project

```bash
# Create feature branch
git checkout -b feature/person3-frontend

# Create frontend folder structure
mkdir -p frontend/templates frontend/static
cd frontend

# Create files
touch app_frontend.py requirements.txt
mkdir templates/components
mkdir static/css static/js static/images
```

Create file: `frontend/requirements.txt`

```
Flask==2.3.3
requests==2.31.0
python-dotenv==1.0.0
```

---

### **DAYS 4-7: Base Setup & Templates**

Create file: `frontend/app_frontend.py`

```python
import os
from flask import Flask, render_template, request, redirect, url_for, session, jsonify
import requests
from dotenv import load_dotenv
from datetime import datetime

load_dotenv()

app = Flask(__name__)
app.secret_key = os.getenv('SECRET_KEY', 'dev_key_change_this')

# Backend API configuration
API_BASE = os.getenv('API_BASE', 'http://127.0.0.1:5000/api')
API_TIMEOUT = 10

# =====================================================
# HELPER FUNCTIONS
# =====================================================

def get_auth_headers():
    """Get authorization headers if user is logged in"""
    token = session.get('token')
    if token:
        return {'Authorization': f'Bearer {token}'}
    return {}

def api_call(method, endpoint, data=None, params=None):
    """Make API call with error handling"""
    try:
        headers = get_auth_headers()
        headers['Content-Type'] = 'application/json'
        
        url = f"{API_BASE}{endpoint}"
        
        if method == 'GET':
            response = requests.get(url, headers=headers, params=params, timeout=API_TIMEOUT)
        elif method == 'POST':
            response = requests.post(url, headers=headers, json=data, timeout=API_TIMEOUT)
        elif method == 'PUT':
            response = requests.put(url, headers=headers, json=data, timeout=API_TIMEOUT)
        elif method == 'DELETE':
            response = requests.delete(url, headers=headers, timeout=API_TIMEOUT)
        
        return response.status_code, response.json()
    
    except Exception as e:
        return 500, {'error': str(e)}

def is_logged_in():
    """Check if user is logged in"""
    return 'token' in session and 'user_id' in session

# =====================================================
# PUBLIC ROUTES
# =====================================================

@app.route('/')
def home():
    """Landing page"""
    return render_template('home.html')

@app.route('/auth/register', methods=['GET', 'POST'])
def register():
    """Register new user"""
    if request.method == 'POST':
        name = request.form.get('name')
        email = request.form.get('email')
        password = request.form.get('password')
        confirm_password = request.form.get('confirm_password')
        user_type = request.form.get('user_type')
        
        # Validation
        if not all([name, email, password, user_type]):
            return render_template('register.html', error='All fields required'), 400
        
        if password != confirm_password:
            return render_template('register.html', error='Passwords do not match'), 400
        
        if len(password) < 6:
            return render_template('register.html', error='Password must be at least 6 characters'), 400
        
        # Call API
        status, response = api_call('POST', '/auth/register', {
            'name': name,
            'email': email,
            'password': password,
            'user_type': user_type
        })
        
        if status == 201:
            session['token'] = response['token']
            session['user_id'] = response['user_id']
            session['user_type'] = response['user_type']
            session['name'] = name
            
            if user_type == 'Buyer':
                return redirect(url_for('buyer_search'))
            else:
                return redirect(url_for('seller_dashboard'))
        else:
            error = response.get('error', 'Registration failed')
            return render_template('register.html', error=error), status
    
    return render_template('register.html')

@app.route('/auth/login', methods=['GET', 'POST'])
def login():
    """Login user"""
    if request.method == 'POST':
        email = request.form.get('email')
        password = request.form.get('password')
        
        if not all([email, password]):
            return render_template('login.html', error='Email and password required'), 400
        
        status, response = api_call('POST', '/auth/login', {
            'email': email,
            'password': password
        })
        
        if status == 200:
            session['token'] = response['token']
            session['user_id'] = response['user_id']
            session['user_type'] = response['user_type']
            session['name'] = response['name']
            
            if response['user_type'] == 'Buyer':
                return redirect(url_for('buyer_search'))
            else:
                return redirect(url_for('seller_dashboard'))
        else:
            error = response.get('error', 'Login failed')
            return render_template('login.html', error=error), status
    
    return render_template('login.html')

@app.route('/logout')
def logout():
    """Logout user"""
    session.clear()
    return redirect(url_for('home'))

# =====================================================
# BUYER ROUTES
# =====================================================

@app.route('/buyer/search', methods=['GET', 'POST'])
def buyer_search():
    """Search properties"""
    if not is_logged_in() or session.get('user_type') != 'Buyer':
        return redirect(url_for('login'))
    
    properties = []
    pagination = {}
    
    if request.method == 'POST' or request.args.get('page'):
        page = request.args.get('page', 1, type=int)
        
        params = {
            'min_price': request.form.get('min_price') or request.args.get('min_price', 0),
            'max_price': request.form.get('max_price') or request.args.get('max_price', 999999999),
            'city': request.form.get('city') or request.args.get('city', ''),
            'bedrooms': request.form.get('bedrooms') or request.args.get('bedrooms', 0),
            'property_type': request.form.get('property_type') or request.args.get('property_type', ''),
            'page': page,
            'per_page': 12
        }
        
        status, response = api_call('GET', '/properties', params=params)
        
        if status == 200:
            properties = response.get('properties', [])
            pagination = response.get('pagination', {})
    
    return render_template('buyer_search.html', properties=properties, pagination=pagination)

@app.route('/property/<int:property_id>')
def property_detail(property_id):
    """View property details"""
    status, response = api_call('GET', f'/properties/{property_id}')
    
    if status == 200:
        property_data = response
        return render_template('property_detail.html', property=property_data)
    else:
        return 'Property not found', 404

@app.route('/buyer/dashboard')
def buyer_dashboard():
    """Buyer dashboard - favorites and appointments"""
    if not is_logged_in() or session.get('user_type') != 'Buyer':
        return redirect(url_for('login'))
    
    user_id = session['user_id']
    status, response = api_call('GET', f'/buyer/{user_id}/dashboard')
    
    if status == 200:
        data = response
        return render_template('buyer_dashboard.html', data=data)
    else:
        return render_template('buyer_dashboard.html', data={'favorites': [], 'appointments': []})

@app.route('/api/favorites', methods=['POST'])
def add_favorite_api():
    """API endpoint to add favorite (AJAX)"""
    if not is_logged_in():
        return jsonify({'error': 'Not logged in'}), 401
    
    data = request.get_json()
    status, response = api_call('POST', '/favorites', data)
    
    return jsonify(response), status

@app.route('/api/appointments', methods=['POST'])
def schedule_appointment_api():
    """API endpoint to schedule appointment (AJAX)"""
    if not is_logged_in():
        return jsonify({'error': 'Not logged in'}), 401
    
    data = request.get_json()
    status, response = api_call('POST', '/appointments', data)
    
    return jsonify(response), status

# =====================================================
# SELLER ROUTES
# =====================================================

@app.route('/seller/dashboard')
def seller_dashboard():
    """Seller dashboard"""
    if not is_logged_in() or session.get('user_type') != 'Seller':
        return redirect(url_for('login'))
    
    seller_id = session['user_id']
    status, response = api_call('GET', f'/seller/{seller_id}/dashboard')
    
    if status == 200:
        data = response
    else:
        data = {'stats': {}, 'properties': []}
    
    return render_template('seller_dashboard.html', data=data)

@app.route('/seller/property/add', methods=['GET', 'POST'])
def seller_add_property():
    """Add new property"""
    if not is_logged_in() or session.get('user_type') != 'Seller':
        return redirect(url_for('login'))
    
    if request.method == 'POST':
        property_data = {
            'address': request.form.get('address'),
            'city': request.form.get('city'),
            'state': request.form.get('state'),
            'zipcode': request.form.get('zipcode'),
            'price': float(request.form.get('price')),
            'bedrooms': int(request.form.get('bedrooms')),
            'bathrooms': float(request.form.get('bathrooms')),
            'area_sqft': int(request.form.get('area_sqft')),
            'property_type': request.form.get('property_type'),
            'description': request.form.get('description'),
        }
        
        status, response = api_call('POST', '/seller/properties', property_data)
        
        if status == 201:
            return redirect(url_for('seller_dashboard'))
        else:
            error = response.get('error', 'Failed to create property')
            return render_template('seller_add_property.html', error=error), status
    
    return render_template('seller_add_property.html')

@app.route('/seller/property/<int:property_id>/edit', methods=['GET', 'POST'])
def seller_edit_property(property_id):
    """Edit property"""
    if not is_logged_in() or session.get('user_type') != 'Seller':
        return redirect(url_for('login'))
    
    if request.method == 'GET':
        status, response = api_call('GET', f'/properties/{property_id}')
        if status == 200:
            property_data = response
        else:
            return 'Property not found', 404
    
    elif request.method == 'POST':
        update_data = {
            'price': float(request.form.get('price')),
            'bedrooms': int(request.form.get('bedrooms')),
            'bathrooms': float(request.form.get('bathrooms')),
            'area_sqft': int(request.form.get('area_sqft')),
            'status': request.form.get('status'),
            'description': request.form.get('description'),
        }
        
        status, response = api_call('PUT', f'/seller/properties/{property_id}', update_data)
        
        if status == 200:
            return redirect(url_for('seller_dashboard'))
        else:
            error = response.get('error', 'Failed to update property')
            return render_template('seller_edit_property.html', property=property_data, error=error), status
    
    return render_template('seller_edit_property.html', property=property_data)

# =====================================================
# ERROR HANDLERS
# =====================================================

@app.errorhandler(404)
def not_found(error):
    return render_template('404.html'), 404

@app.errorhandler(500)
def server_error(error):
    return render_template('500.html'), 500

if __name__ == '__main__':
    print("🚀 Real Estate Frontend Starting...")
    print("🔗 Running on http://127.0.0.1:5001")
    app.run(host='127.0.0.1', port=5001, debug=True)
```

---

### **DAYS 8-15: Create HTML Templates**

Create file: `frontend/templates/base.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Real Estate Platform{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
    {% block extra_css %}{% endblock %}
</head>
<body>
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark sticky-top">
        <div class="container">
            <a class="navbar-brand fw-bold" href="/">🏠 RealEstate</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    {% if not session.get('token') %}
                        <li class="nav-item">
                            <a class="nav-link" href="{{ url_for('login') }}">Login</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{{ url_for('register') }}">Register</a>
                        </li>
                    {% else %}
                        {% if session.get('user_type') == 'Buyer' %}
                            <li class="nav-item">
                                <a class="nav-link" href="{{ url_for('buyer_search') }}">Search</a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" href="{{ url_for('buyer_dashboard') }}">Dashboard</a>
                            </li>
                        {% elif session.get('user_type') == 'Seller' %}
                            <li class="nav-item">
                                <a class="nav-link" href="{{ url_for('seller_dashboard') }}">Dashboard</a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" href="{{ url_for('seller_add_property') }}">Add Property</a>
                            </li>
                        {% endif %}
                        <li class="nav-item">
                            <a class="nav-link" href="{{ url_for('logout') }}">Logout ({{ session.get('name') }})</a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>

    <!-- Flash Messages -->
    {% with messages = get_flashed_messages(with_categories=true) %}
        {% if messages %}
            {% for category, message in messages %}
                <div class="alert alert-{{ category }} alert-dismissible fade show" role="alert">
                    {{ message }}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
            {% endif %}
        {% endwith %}

    <!-- Main Content -->
    <main class="py-4">
        {% block content %}{% endblock %}
    </main>

    <!-- Footer -->
    <footer class="bg-dark text-white text-center py-4 mt-5">
        <div class="container">
            <p>&copy; 2025 Real Estate Platform. All rights reserved.</p>
            <small>Built with Flask, MySQL, and Bootstrap</small>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <script src="{{ url_for('static', filename='js/main.js') }}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

Create file: `frontend/templates/home.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="hero-section bg-primary text-white py-5">
    <div class="container text-center">
        <h1 class="display-4 fw-bold mb-4">Find Your Dream Home</h1>
        <p class="lead mb-4">Search thousands of properties, connect with agents, and make smart decisions</p>
        {% if not session.get('token') %}
            <a href="{{ url_for('register') }}" class="btn btn-light btn-lg me-2">Get Started</a>
            <a href="{{ url_for('login') }}" class="btn btn-outline-light btn-lg">Login</a>
        {% else %}
            {% if session.get('user_type') == 'Buyer' %}
                <a href="{{ url_for('buyer_search') }}" class="btn btn-light btn-lg me-2">Search Properties</a>
            {% else %}
                <a href="{{ url_for('seller_dashboard') }}" class="btn btn-light btn-lg me-2">View Dashboard</a>
            {% endif %}
        {% endif %}
    </div>
</div>

<div class="container my-5">
    <div class="row">
        <div class="col-md-4 text-center mb-4">
            <div class="feature-box p-4 border rounded">
                <h4>🔍 Smart Search</h4>
                <p>Filter properties by price, location, bedrooms, and more</p>
            </div>
        </div>
        <div class="col-md-4 text-center mb-4">
            <div class="feature-box p-4 border rounded">
                <h4>❤️ Save Favorites</h4>
                <p>Create your wishlist and track properties you love</p>
            </div>
        </div>
        <div class="col-md-4 text-center mb-4">
            <div class="feature-box p-4 border rounded">
                <h4>📅 Schedule Tours</h4>
                <p>Book appointments with agents easily</p>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

Create file: `frontend/templates/login.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container mt-5">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card shadow">
                <div class="card-body p-5">
                    <h2 class="card-title text-center mb-4">Login</h2>
                    
                    {% if error %}
                        <div class="alert alert-danger">{{ error }}</div>
                    {% endif %}
                    
                    <form method="POST">
                        <div class="mb-3">
                            <label for="email" class="form-label">Email</label>
                            <input type="email" class="form-control" id="email" name="email" required>
                        </div>
                        
                        <div class="mb-3">
                            <label for="password" class="form-label">Password</label>
                            <input type="password" class="form-control" id="password" name="password" required>
                        </div>
                        
                        <button type="submit" class="btn btn-primary w-100">Login</button>
                    </form>
                    
                    <p class="text-center mt-3">
                        Don't have an account? <a href="{{ url_for('register') }}">Register here</a>
                    </p>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

Create file: `frontend/templates/register.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container mt-5">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card shadow">
                <div class="card-body p-5">
                    <h2 class="card-title text-center mb-4">Create Account</h2>
                    
                    {% if error %}
                        <div class="alert alert-danger">{{ error }}</div>
                    {% endif %}
                    
                    <form method="POST">
                        <div class="mb-3">
                            <label for="name" class="form-label">Full Name</label>
                            <input type="text" class="form-control" id="name" name="name" required>
                        </div>
                        
                        <div class="mb-3">
                            <label for="email" class="form-label">Email</label>
                            <input type="email" class="form-control" id="email" name="email" required>
                        </div>
                        
                        <div class="mb-3">
                            <label for="user_type" class="form-label">Account Type</label>
                            <select class="form-select" id="user_type" name="user_type" required>
                                <option selected disabled>Choose...</option>
                                <option value="Buyer">Buyer</option>
                                <option value="Seller">Seller</option>
                            </select>
                        </div>
                        
                        <div class="mb-3">
                            <label for="password" class="form-label">Password</label>
                            <input type="password" class="form-control" id="password" name="password" required>
                        </div>
                        
                        <div class="mb-3">
                            <label for="confirm_password" class="form-label">Confirm Password</label>
                            <input type="password" class="form-control" id="confirm_password" name="confirm_password" required>
                        </div>
                        
                        <button type="submit" class="btn btn-primary w-100">Register</button>
                    </form>
                    
                    <p class="text-center mt-3">
                        Already have an account? <a href="{{ url_for('login') }}">Login here</a>
                    </p>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

Create file: `frontend/templates/buyer_search.html`

```html
{% extends "base.html" %}

{% block content %}
<div class="container my-5">
    <h2 class="mb-4">Search Properties</h2>
    
    <!-- Filter Form -->
    <div class="card mb-4">
        <div class="card-body">
            <form method="POST" class="row g-3">
                <div class="col-md-3">
                    <label for="city" class="form-label">City</label>
                    <input type="text" class="form-control" id="city" name="city" placeholder="Enter city">
                </div>
                
                <div class="col-md-2">
                    <label for="min_price" class="form-label">Min Price</label>
                    <input type="number" class="form-control" id="min_price" name="min_price" placeholder="0">
                </div>
                
                <div class="col-md-2">
                    <label for="max_price" class="form-label">Max Price</label>
                    <input type="number" class="form-control" id="max_price" name="max_price" placeholder="999999">
                </div>
                
                <div class="col-md-2">
                    <label for="bedrooms" class="form-label">Min Bedrooms</label>
                    <input type="number" class="form-control" id="bedrooms" name="bedrooms" placeholder="0">
                </div>
                
                <div class="col-md-2">
                    <label for="property_type" class="form-label">Type</label>
                    <select class="form-select" id="property_type" name="property_type">
                        <option value="">All</option>
                        <option value="House">House</option>
                        <option value="Apartment">Apartment</option>
                        <option value="Condo">Condo</option>
                        <option value="Villa">Villa</option>
                    </select>
                </div>
                
                <div class="col-12">
                    <button type="submit" class="btn btn-primary">Search</button>
                </div>
            </form>
        </div>
    </div>
    
    <!-- Properties Grid -->
    {% if properties %}
        <div class="row g-4">
            {% for prop in properties %}
                <div class="col-md-6 col-lg-4">
                    <div class="card h-100 shadow-sm property-card">
                        {% if prop.featured_image %}
                            <img src="{{ prop.featured_image }}" class="card-img-top" alt="{{ prop.address }}" style="height: 200px; object-fit: cover;">
                        {% else %}
                            <div class="card-img-top bg-light d-flex align-items-center justify-content-center" style="height: 200px;">
                                <span class="text-muted">No Image</span>
                            </div>
                        {% endif %}
                        
                        <div class="card-body">
                            <h5 class="card-title">${{ "%.2f"|format(prop.price) }}</h5>
                            <p class="card-text text-muted">{{ prop.address }}</p>
                            <p class="card-text small">{{ prop.bedrooms }} bed | {{ prop.bathrooms }} bath | {{ prop.area_sqft }} sqft</p>
                            
                            <a href="{{ url_for('property_detail', property_id=prop.property_id) }}" class="btn btn-primary btn-sm">View Details</a>
                            <button class="btn btn-outline-danger btn-sm" onclick="addFavorite({{ prop.property_id }})">❤️ Save</button>
                        </div>
                    </div>
                </div>
            {% endfor %}
        </div>
        
        <!-- Pagination -->
        {% if pagination.pages > 1 %}
            <nav class="mt-5">
                <ul class="pagination justify-content-center">
                    {% if pagination.page > 1 %}
                        <li class="page-item">
                            <a class="page-link" href="?page=1">First</a>
                        </li>
                        <li class="page-item">
                            <a class="page-link" href="?page={{ pagination.page - 1 }}">Previous</a>
                        </li>
                    {% endif %}
                    
                    {% for page_num in range(1, pagination.pages + 1) %}
                        <li class="page-item {% if page_num == pagination.page %}active{% endif %}">
                            <a class="page-link" href="?page={{ page_num }}">{{ page_num }}</a>
                        </li>
                    {% endfor %}
                    
                    {% if pagination.page < pagination.pages %}
                        <li class="page-item">
                            <a class="page-link" href="?page={{ pagination.page + 1 }}">Next</a>
                        </li>
                        <li class="page-item">
                            <a class="page-link" href="?page={{ pagination.pages }}">Last</a>
                        </li>
                    {% endif %}
                </ul>
            </nav>
        {% endif %}
    {% else %}
        <div class="alert alert-info">No properties found. Try adjusting your filters.</div>
    {% endif %}
</div>

<script>
function addFavorite(propertyId) {
    fetch('/api/favorites', {
        method: 'DAYS 1-3: Planning & Design**

#### Day 1: ER Diagram & Database Design
```
Tasks:
1. Create detailed ER diagram with all 8 tables
2. Define all column names and data types
3. Identify primary keys and foreign keys
4. Plan indices for performance

Create file: docs/ER_DIAGRAM.md
```

#### Day 2: Normalization & Constraints
```
Design all constraints:
- PRIMARY KEY for each table
- FOREIGN KEY relationships
- UNIQUE constraints on email fields
- CHECK constraints on numeric fields (price >= 0, bedrooms >= 0)
- DEFAULT values (dates, status)

Document: docs/DATABASE_DESIGN.md
```

#### Day 3: Setup MySQL & Test Connection
```bash
# Install MySQL if not already
# Download from https://dev.mysql.com/downloads/mysql/

# Create database
mysql -u root -p
CREATE DATABASE real_estate_db;
EXIT;

# Test connection
mysql -u root -p real_estate_db -e "SELECT VERSION();"
```

---

### **DAYS 4-7: Database Creation & Indices**

#### Day 4-5: Create Complete Schema

Create file: `scripts/schema.sql`

```sql
-- =====================================================
-- REAL ESTATE DATABASE SCHEMA
-- =====================================================

-- Users Table (Buyers, Sellers, Admins)
CREATE TABLE Users (
  user_id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(150) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  phone VARCHAR(20),
  user_type ENUM('Buyer','Seller','Admin') NOT NULL,
  registration_date DATE DEFAULT CURDATE(),
  is_active BOOLEAN DEFAULT TRUE,
  UNIQUE KEY unique_email (email)
);

-- Agents Table
CREATE TABLE Agents (
  agent_id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(150) NOT NULL UNIQUE,
  phone VARCHAR(20),
  license_number VARCHAR(50) UNIQUE,
  commission_rate DECIMAL(5,2) CHECK (commission_rate >= 0 AND commission_rate <= 100),
  hire_date DATE,
  is_active BOOLEAN DEFAULT TRUE
);

-- Properties Table (Main Listing Table)
CREATE TABLE Properties (
  property_id INT PRIMARY KEY AUTO_INCREMENT,
  address VARCHAR(200) NOT NULL,
  city VARCHAR(100) NOT NULL,
  state VARCHAR(50) NOT NULL,
  zipcode VARCHAR(20) NOT NULL,
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  price DECIMAL(12,2) NOT NULL CHECK (price >= 0),
  bedrooms INT NOT NULL CHECK (bedrooms >= 0),
  bathrooms DECIMAL(4,1) NOT NULL CHECK (bathrooms >= 0),
  area_sqft INT NOT NULL CHECK (area_sqft >= 0),
  property_type ENUM('House', 'Apartment', 'Condo', 'Villa', 'Townhouse') NOT NULL,
  description TEXT,
  status ENUM('Available', 'Pending', 'Sold') DEFAULT 'Available',
  listing_date DATE DEFAULT CURDATE(),
  seller_id INT NOT NULL,
  agent_id INT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_properties_seller 
    FOREIGN KEY (seller_id) REFERENCES Users(user_id) 
    ON UPDATE CASCADE ON DELETE RESTRICT,
  CONSTRAINT fk_properties_agent 
    FOREIGN KEY (agent_id) REFERENCES Agents(agent_id) 
    ON UPDATE CASCADE ON DELETE SET NULL,
  
  KEY idx_city (city),
  KEY idx_price (price),
  KEY idx_status (status),
  KEY idx_seller_id (seller_id),
  KEY idx_property_type (property_type)
);

-- Property Images Table
CREATE TABLE Property_Images (
  image_id INT PRIMARY KEY AUTO_INCREMENT,
  property_id INT NOT NULL,
  image_url VARCHAR(500) NOT NULL,
  image_order INT DEFAULT 1,
  uploaded_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_images_property 
    FOREIGN KEY (property_id) REFERENCES Properties(property_id) 
    ON DELETE CASCADE,
  
  KEY idx_property_id (property_id)
);

-- Appointments/Viewings Table
CREATE TABLE Appointments (
  appointment_id INT PRIMARY KEY AUTO_INCREMENT,
  property_id INT NOT NULL,
  buyer_id INT NOT NULL,
  agent_id INT,
  appointment_date DATETIME NOT NULL,
  appointment_type ENUM('Virtual Tour', 'In-Person', 'Phone') DEFAULT 'In-Person',
  status ENUM('Scheduled','Completed','Cancelled','No-Show') DEFAULT 'Scheduled',
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_appt_property 
    FOREIGN KEY (property_id) REFERENCES Properties(property_id) 
    ON DELETE CASCADE,
  CONSTRAINT fk_appt_buyer 
    FOREIGN KEY (buyer_id) REFERENCES Users(user_id) 
    ON DELETE CASCADE,
  CONSTRAINT fk_appt_agent 
    FOREIGN KEY (agent_id) REFERENCES Agents(agent_id) 
    ON DELETE SET NULL,
  
  KEY idx_appointment_date (appointment_date),
  KEY idx_property_id (property_id),
  KEY idx_buyer_id (buyer_id)
);

-- Favorites/Wishlist Table
CREATE TABLE Favorites (
  favorite_id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  property_id INT NOT NULL,
  saved_date DATETIME DEFAULT NOW(),
  
  UNIQUE KEY unique_user_property (user_id, property_id),
  CONSTRAINT fk_fav_user 
    FOREIGN KEY (user_id) REFERENCES Users(user_id) 
    ON DELETE CASCADE,
  CONSTRAINT fk_fav_property 
    FOREIGN KEY (property_id) REFERENCES Properties(property_id) 
    ON DELETE CASCADE,
  
  KEY idx_user_id (user_id)
);

-- Transactions/Sales Table
CREATE TABLE Transactions (
  transaction_id INT PRIMARY KEY AUTO_INCREMENT,
  property_id INT NOT NULL,
  buyer_id INT NOT NULL,
  seller_id INT NOT NULL,
  agent_id INT,
  sale_price DECIMAL(12,2) NOT NULL CHECK (sale_price >= 0),
  commission_amount DECIMAL(12,2),
  sale_date DATE DEFAULT CURDATE(),
  closing_date DATE,
  status ENUM('Pending', 'Completed', 'Cancelled') DEFAULT 'Pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_tx_property 
    FOREIGN KEY (property_id) REFERENCES Properties(property_id) 
    ON DELETE RESTRICT,
  CONSTRAINT fk_tx_buyer 
    FOREIGN KEY (buyer_id) REFERENCES Users(user_id) 
    ON DELETE RESTRICT,
  CONSTRAINT fk_tx_seller 
    FOREIGN KEY (seller_id) REFERENCES Users(user_id) 
    ON DELETE RESTRICT,
  CONSTRAINT fk_tx_agent 
    FOREIGN KEY (agent_id) REFERENCES Agents(agent_id) 
    ON DELETE SET NULL,
  
  KEY idx_sale_date (sale_date),
  KEY idx_property_id (property_id),
  KEY idx_buyer_id (buyer_id),
  KEY idx_seller_id (seller_id)
);

-- Price History Tracking
CREATE TABLE Price_History (
  history_id INT PRIMARY KEY AUTO_INCREMENT,
  property_id INT NOT NULL,
  old_price DECIMAL(12,2),
  new_price DECIMAL(12,2),
  change_date DATETIME DEFAULT NOW(),
  change_reason VARCHAR(255),
  
  CONSTRAINT fk_ph_property 
    FOREIGN KEY (property_id) REFERENCES Properties(property_id) 
    ON DELETE CASCADE,
  
  KEY idx_property_id (property_id),
  KEY idx_change_date (change_date)
);

-- Search History for Analytics
CREATE TABLE Search_History (
  search_id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT,
  search_query TEXT,
  filters_used VARCHAR(500),
  results_count INT,
  search_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_search_user 
    FOREIGN KEY (user_id) REFERENCES Users(user_id) 
    ON DELETE SET NULL,
  
  KEY idx_user_id (user_id),
  KEY idx_search_date (search_date)
);
```

#### Day 6: Create Stored Procedures

Create file: `scripts/stored_procedures.sql`

```sql
-- =====================================================
-- STORED PROCEDURES
-- =====================================================

DELIMITER //

-- Search Properties with Filters
CREATE PROCEDURE SearchProperties(
  IN p_min_price DECIMAL(12,2),
  IN p_max_price DECIMAL(12,2),
  IN p_min_beds INT,
  IN p_city VARCHAR(100),
  IN p_property_type VARCHAR(50),
  IN p_limit INT DEFAULT 20,
  IN p_offset INT DEFAULT 0
)
BEGIN
  SELECT 
    p.property_id,
    p.address,
    p.city,
    p.state,
    p.zipcode,
    p.price,
    p.bedrooms,
    p.bathrooms,
    p.area_sqft,
    p.property_type,
    p.status,
    p.listing_date,
    a.name AS agent_name,
    a.phone AS agent_phone,
    (SELECT image_url FROM Property_Images WHERE property_id = p.property_id LIMIT 1) AS featured_image
  FROM Properties p
  LEFT JOIN Agents a ON p.agent_id = a.agent_id
  WHERE p.price BETWEEN p_min_price AND p_max_price
    AND p.bedrooms >= p_min_beds
    AND (p_city IS NULL OR p.city = p_city)
    AND (p_property_type IS NULL OR p.property_type = p_property_type)
    AND p.status = 'Available'
  ORDER BY p.listing_date DESC
  LIMIT p_limit OFFSET p_offset;
END //

-- Get Property Details with Images
CREATE PROCEDURE GetPropertyDetails(
  IN p_property_id INT
)
BEGIN
  SELECT 
    p.*,
    a.name AS agent_name,
    a.phone AS agent_phone,
    a.email AS agent_email,
    a.commission_rate,
    u.name AS seller_name,
    u.email AS seller_email
  FROM Properties p
  LEFT JOIN Agents a ON p.agent_id = a.agent_id
  LEFT JOIN Users u ON p.seller_id = u.user_id
  WHERE p.property_id = p_property_id;
  
  SELECT image_id, image_url, image_order 
  FROM Property_Images 
  WHERE property_id = p_property_id 
  ORDER BY image_order;
END //

-- Record a Property Sale
CREATE PROCEDURE RecordSale(
  IN p_property_id INT,
  IN p_buyer_id INT,
  IN p_seller_id INT,
  IN p_agent_id INT,
  IN p_sale_price DECIMAL(12,2)
)
BEGIN
  DECLARE EXIT HANDLER FOR SQLEXCEPTION
  BEGIN
    ROLLBACK;
    RESIGNAL;
  END;
  
  START TRANSACTION;
  
  -- Insert transaction
  INSERT INTO Transactions 
  (property_id, buyer_id, seller_id, agent_id, sale_price, status)
  VALUES (p_property_id, p_buyer_id, p_seller_id, p_agent_id, p_sale_price, 'Completed');
  
  -- Update property status to Sold
  UPDATE Properties 
  SET status = 'Sold' 
  WHERE property_id = p_property_id;
  
  COMMIT;
END //

-- Get Buyer Dashboard Data
CREATE PROCEDURE GetBuyerDashboard(
  IN p_user_id INT
)
BEGIN
  -- Favorite properties
  SELECT 
    p.property_id,
    p.address,
    p.city,
    p.price,
    p.bedrooms,
    p.bathrooms,
    f.saved_date,
    (SELECT image_url FROM Property_Images WHERE property_id = p.property_id LIMIT 1) AS image_url
  FROM Favorites f
  JOIN Properties p ON f.property_id = p.property_id
  WHERE f.user_id = p_user_id
  ORDER BY f.saved_date DESC;
  
  -- Upcoming appointments
  SELECT 
    a.appointment_id,
    a.appointment_date,
    a.status,
    p.address,
    p.city,
    ag.name AS agent_name,
    ag.phone AS agent_phone
  FROM Appointments a
  JOIN Properties p ON a.property_id = p.property_id
  LEFT JOIN Agents ag ON a.agent_id = ag.agent_id
  WHERE a.buyer_id = p_user_id
    AND a.appointment_date > NOW()
    AND a.status IN ('Scheduled', 'Completed')
  ORDER BY a.appointment_date DESC;
END //

-- Get Seller Dashboard Data
CREATE PROCEDURE GetSellerDashboard(
  IN p_seller_id INT
)
BEGIN
  SELECT 
    COUNT(CASE WHEN status = 'Available' THEN 1 END) AS active_listings,
    COUNT(CASE WHEN status = 'Pending' THEN 1 END) AS pending_listings,
    COUNT(CASE WHEN status = 'Sold' THEN 1 END) AS sold_listings,
    SUM(CASE WHEN status = 'Sold' THEN price ELSE 0 END) AS total_sales_value
  FROM Properties
  WHERE seller_id = p_seller_id;
  
  SELECT 
    property_id,
    address,
    city,
    price,
    bedrooms,
    status,
    listing_date,
    (SELECT COUNT(*) FROM Appointments WHERE property_id = Properties.property_id) AS view_count
  FROM Properties
  WHERE seller_id = p_seller_id
  ORDER BY listing_date DESC;
END //

-- Get Admin Dashboard (All Transactions)
CREATE PROCEDURE GetAdminDashboard()
BEGIN
  SELECT 
    DATE(sale_date) AS transaction_date,
    COUNT(*) AS transaction_count,
    SUM(sale_price) AS total_sale_value,
    AVG(sale_price) AS avg_sale_price
  FROM Transactions
  WHERE status = 'Completed'
  GROUP BY DATE(sale_date)
  ORDER BY transaction_date DESC
  LIMIT 30;
END //

DELIMITER ;
```

#### Day 7: Create Triggers

Create file: `scripts/triggers.sql`

```sql
-- =====================================================
-- TRIGGERS FOR AUTOMATION
-- =====================================================

DELIMITER //

-- Log price changes
CREATE TRIGGER log_price_change
BEFORE UPDATE ON Properties
FOR EACH ROW
BEGIN
  IF OLD.price <> NEW.price THEN
    INSERT INTO Price_History 
    (property_id, old_price, new_price, change_reason)
    VALUES (NEW.property_id, OLD.price, NEW.price, 'Price Updated');
  END IF;
END //

-- Auto-update property status when transaction created
CREATE TRIGGER after_transaction_insert
AFTER INSERT ON Transactions
FOR EACH ROW
BEGIN
  UPDATE Properties 
  SET status = 'Sold' 
  WHERE property_id = NEW.property_id;
END //

-- Validate appointment date is in future
CREATE TRIGGER validate_appointment_date
BEFORE INSERT ON Appointments
FOR EACH ROW
BEGIN
  IF NEW.appointment_date <= NOW() THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Appointment must be in the future';
  END IF;
END //

-- Log search history
CREATE TRIGGER log_search_history
AFTER SELECT ON Properties
FOR EACH ROW
BEGIN
  -- Note: This is a simplified example, actual implementation would be in application
  INSERT INTO Search_History (user_id, search_query) VALUES (NULL, 'search');
END //

DELIMITER ;
```

---

### **DAYS 8-11: Data Import & Optimization**

#### Day 8: Download Kaggle Data

```bash
# Go to https://www.kaggle.com/
# Search for "USA Real Estate Data" or "USA House Sales"
# Download the CSV file
# Place in: data/usa_housing.csv

# Verify file exists
ls -la data/usa_housing.csv
```

#### Day 9-10: Create & Run Import Script

Create file: `scripts/import_data.py`

```python
import csv
import mysql.connector
from mysql.connector import Error
from datetime import datetime, timedelta
import random

# Configuration
CSV_PATH = 'data/usa_housing.csv'
DB_CONFIG = {
    'host': 'localhost',
    'user': 'root',
    'password': 'your_root_password',
    'database': 'real_estate_db'
}

def connect_db():
    """Create database connection"""
    try:
        conn = mysql.connector.connect(**DB_CONFIG)
        return conn
    except Error as e:
        print(f"Error connecting to database: {e}")
        return None

def import_agents(cursor, conn):
    """Import sample agents"""
    agents = [
        ('John Smith', 'john.smith@realty.com', '555-0001', 3.5),
        ('Sarah Johnson', 'sarah.j@realty.com', '555-0002', 3.0),
        ('Michael Brown', 'michael.b@realty.com', '555-0003', 4.0),
        ('Emily Davis', 'emily.d@realty.com', '555-0004', 3.5),
        ('Robert Wilson', 'robert.w@realty.com', '555-0005', 3.25),
    ]
    
    for name, email, phone, commission in agents:
        cursor.execute("""
            INSERT INTO Agents (name, email, phone, commission_rate, hire_date)
            VALUES (%s, %s, %s, %s, %s)
        """, (name, email, phone, commission, datetime.now().date()))
    
    conn.commit()
    print(f"✅ Imported {len(agents)} agents")

def import_sample_sellers(cursor, conn):
    """Import sample sellers"""
    sellers = [
        ('Alice Miller', 'alice@example.com', '555-1001', 'Seller'),
        ('Bob Anderson', 'bob@example.com', '555-1002', 'Seller'),
        ('Carol Thomas', 'carol@example.com', '555-1003', 'Seller'),
        ('David Moore', 'david@example.com', '555-1004', 'Seller'),
        ('Eve Jackson', 'eve@example.com', '555-1005', 'Seller'),
    ]
    
    for name, email, phone, user_type in sellers:
        cursor.execute("""
            INSERT INTO Users (name, email, password_hash, phone, user_type)
            VALUES (%s, %s, %s, %s, %s)
        """, (name, email, 'hashed_password', phone, user_type))
    
    conn.commit()
    print(f"✅ Imported {len(sellers)} sellers")

def import_properties(cursor, conn):
    """Import properties from Kaggle CSV"""
    try:
        with open(CSV_PATH, newline='', encoding='utf-8') as csvfile:
            reader = csv.DictReader(csvfile)
            
            # Get agent and seller IDs
            cursor.execute("SELECT agent_id FROM Agents LIMIT 1")
            agent_id = cursor.fetchone()[0] if cursor.fetchone() else 1
            cursor.reset()
            
            cursor.execute("SELECT user_id FROM Users WHERE user_type='Seller' LIMIT 5")
            seller_ids = [row[0] for row in cursor.fetchall()]
            
            if not seller_ids:
                cursor.execute("SELECT user_id FROM Users LIMIT 5")
                seller_ids = [row[0] for row in cursor.fetchall()]
            
            count = 0
            for row in reader:
                if count >= 1000:  # Import first 1000 records
                    break
                
                try:
                    # Parse CSV columns (adjust based on your CSV structure)
                    address = row.get('address') or row.get('Address') or 'Unknown Address'
                    city = row.get('city') or row.get('City') or 'Unknown City'
                    state = row.get('state') or row.get('State') or 'CA'
                    zipcode = row.get('zipcode') or row.get('Zipcode') or '00000'
                    price = float(row.get('price') or row.get('Price') or '300000')
                    bedrooms = int(row.get('bedrooms') or row.get('Bedrooms') or '3')
                    bathrooms = float(row.get('bathrooms') or row.get('Bathrooms') or '2')
                    area = int(row.get('sqft') or row.get('sqft_living') or row.get('Area') or '2000')
                    property_type = row.get('property_type') or row.get('Type') or 'House'
                    
                    # Validate property type
                    if property_type not in ('House', 'Apartment', 'Condo', 'Villa', 'Townhouse'):
                        property_type = 'House'
                    
                    seller_id = seller_ids[count % len(seller_ids)]
                    
                    cursor.execute("""
                        INSERT INTO Properties 
                        (address, city, state, zipcode, price, bedrooms, bathrooms,
                         area_sqft, property_type, status, seller_id, agent_id)
                        VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
                    """, (address, city, state, zipcode, price, bedrooms, bathrooms,
                          area, property_type, 'Available', seller_id, agent_id))
                    
                    count += 1
                    if count % 100 == 0:
                        conn.commit()
                        print(f"  Processed {count} properties...")
                    
                except Exception as e:
                    print(f"  Skipped row: {str(e)}")
                    continue
            
            conn.commit()
            print(f"✅ Imported {count} properties from Kaggle")
            
    except FileNotFoundError:
        print(f"❌ CSV file not found: {CSV_PATH}")

def main():
    """Main import process"""
    print("🚀 Starting Real Estate Database Import...")
    print("=" * 50)
    
    conn = connect_db()
    if not conn:
        return
    
    cursor = conn.cursor()
    
    try:
        print("1️⃣ Importing Agents...")
        import_agents(cursor, conn)
        
        print("2️⃣ Importing Sample Sellers...")
        import_sample_sellers(cursor, conn)
        
        print("3️⃣ Importing Properties from Kaggle...")
        import_properties(cursor, conn)
        
        print("=" * 50)
        print("✅ Import completed successfully!")
        
        # Show statistics
        cursor.execute("SELECT COUNT(*) FROM Properties")
        prop_count = cursor.fetchone()[0]
        
        cursor.execute("SELECT COUNT(*) FROM Users")
        user_count = cursor.fetchone()[0]
        
        print(f"\n📊 Database Statistics:")
        print(f"   - Total Properties: {prop_count}")
        print(f"   - Total Users: {user_count}")
        
    except Error as e:
        print(f"❌ Import error: {e}")
        conn.rollback()
    finally:
        cursor.close()
        conn.close()

if __name__ == '__main__':
    main()
```

#### Day 10: Run Import

```bash
# Run the import script
python scripts/import_data.py

# Output should show:
# 🚀 Starting Real Estate Database Import...
# ✅ Imported 5 agents
# ✅ Imported 5 sellers
# ✅ Imported 1000 properties from Kaggle
```

#### Day 11: Verify & Create Indices

```bash
# Connect to MySQL and verify
mysql -u root -p real_estate_db

# Run these queries to verify:
SELECT COUNT(*) FROM Properties;
SELECT COUNT(*) FROM Users;
SELECT COUNT(*) FROM Agents;

# Check indices were created
SHOW INDEX FROM Properties;
SHOW INDEX FROM Appointments;

# Test a stored procedure
CALL SearchProperties(100000, 500000, 2, 'Los Angeles', NULL, 20, 0);

# Exit
EXIT;
```

---

### **DAYS 12-20: Support & Optimization**

- Answer Person 2's database questions
- Help optimize queries if needed
- Support Person 3's frontend queries
- Validate data integrity during testing

### **Your Final Deliverables:**
✅ `scripts/schema.sql` - Complete database schema  
✅ `scripts/stored_procedures.sql` - All stored procedures  
✅ `scripts/triggers.sql` - Automatic triggers  
✅ `scripts/import_data.py` - Working import script  
✅ `docs/ER_DIAGRAM.md` - Entity relationship diagram  
✅ Database with 1000+ real properties

---

## ⚙️ Person 2: Backend Engineer (Days 1-20)

### 📌 Your Role
Build the REST API that powers the entire application. You connect the database to the frontend.

---

### **DAYS 1-3: Setup & Planning**

#### Day 1-2: Environment Setup

```bash
# Create feature branch
git checkout -b feature/person2-backend

# Create backend folder structure
mkdir backend
cd backend
touch app.py requirements.txt .env

# Create virtual environment
python -m venv venv
source venv/bin/activate  # macOS/Linux
# or
venv\Scripts\activate  # Windows
```

#### Create file: `backend/requirements.txt`

```
Flask==2.3.3
Flask-MySQLdb==1.0.1
Flask-CORS==4.0.0
python-dotenv==1.0.0
requests==2.31.0
mysql-connector-python==8.1.0
PyJWT==2.8.1
Werkzeug==2.3.7
```

#### Create file: `backend/.env`

```
FLASK_ENV=development
FLASK_DEBUG=True
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=your_root_password
MYSQL_DB=real_estate_db
SECRET_KEY=your_super_secret_key_change_this
```

#### Day 3: Test Flask Connection

```bash
# Install dependencies
pip install -r requirements.txt

# Test MySQL connection
python -c "import MySQLdb; print('MySQL connection OK')"
```

---

### **DAYS 4-7: Foundation & Authentication**

#### Create file: `backend/app.py` (Part 1 - Setup)

```python
import os
from flask import Flask, request, jsonify, session
from flask_mysqldb import MySQL
from flask_cors import CORS
from dotenv import load_dotenv
import json
from datetime import datetime, timedelta
from functools import wraps
import jwt

# Load environment variables
load_dotenv()

# Initialize Flask app
app = Flask(__name__)
app.config['SECRET_KEY'] = os.getenv('SECRET_KEY', 'dev_key_change_this')

# MySQL Configuration
app.config['MYSQL_HOST'] = os.getenv('MYSQL_HOST')
app.config['MYSQL_USER'] = os.getenv('MYSQL_USER')
app.config['MYSQL_PASSWORD'] = os.getenv('MYSQL_PASSWORD')
app.config['MYSQL_DB'] = os.getenv('MYSQL_DB')
app.config['MYSQL_CURSORCLASS'] = 'DictCursor'

# Initialize MySQL
mysql = MySQL(app)

# Enable CORS
CORS(app, supports_credentials=True)

# =====================================================
# HELPER FUNCTIONS
# =====================================================

def token_required(f):
    """Decorator to check JWT token"""
    @wraps(f)
    def decorated(*args, **kwargs):
        token = None
        
        if 'Authorization' in request.headers:
            try:
                token = request.headers['Authorization'].split(" ")[1]
            except:
                return jsonify({'error': 'Invalid token format'}), 401
        
        if not token:
            token = request.args.get('token') or session.get('token')
        
        if not token:
            return jsonify({'error': 'Token missing'}), 401
        
        try:
            data = jwt.decode(token, app.config['SECRET_KEY'], algorithms=["HS256"])
            request.user_id = data['user_id']
            request.user_type = data['user_type']
        except:
            return jsonify({'error': 'Invalid token'}), 401
        
        return f(*args, **kwargs)
    
    return decorated

def role_required(role):
    """Decorator to check user role"""
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            if request.user_type != role and request.user_type != 'Admin':
                return jsonify({'error': 'Insufficient permissions'}), 403
            return f(*args, **kwargs)
        return decorated_function
    return decorator

def dict_to_json_serializable(data):
    """Convert MySQL DictCursor results to JSON serializable"""
    if data is None:
        return None
    
    if isinstance(data, list):
        return [dict_to_json_serializable(item) for item in data]
    
    if isinstance(data, dict):
        result = {}
        for key, value in data.items():
            if isinstance(value, (datetime, date)):
                result[key] = value.isoformat()
            else:
                result[key] = value
        return result
    
    return data

# =====================================================
# AUTHENTICATION ROUTES
# =====================================================

@app.route('/api/auth/register', methods=['POST'])
def register():
    """Register a new user"""
    data = request.get_json()
    
    # Validate input
    if not all(k in data for k in ['name', 'email', 'password', 'user_type']):
        return jsonify({'error': 'Missing required fields'}), 400
    
    if data['user_type'] not in ['Buyer', 'Seller', 'Admin']:
        return jsonify({'error': 'Invalid user type'}), 400
    
    cur = mysql.connection.cursor()
    
    try:
        # Check if email exists
        cur.execute("SELECT user_id FROM Users WHERE email = %s", (data['email'],))
        if cur.fetchone():
            return jsonify({'error': 'Email already exists'}), 409
        
        # Simple password hashing (use bcrypt in production!)
        from werkzeug.security import generate_password_hash
        password_hash = generate_password_hash(data['password'])
        
        # Insert user
        cur.execute("""
            INSERT INTO Users (name, email, password_hash, phone, user_type)
            VALUES (%s, %s, %s, %s, %s)
        """, (data['name'], data['email'], password_hash, data.get('phone', ''), data['user_type']))
        
        mysql.connection.commit()
        user_id = cur.lastrowid
        
        # Create token
        token = jwt.encode({
            'user_id': user_id,
            'user_type': data['user_type'],
            'exp': datetime.utcnow() + timedelta(days=7)
        }, app.config['SECRET_KEY'], algorithm="HS256")
        
        return jsonify({
            'message': 'User registered successfully',
            'user_id': user_id,
            'token': token,
            'user_type': data['user_type']
        }), 201
    
    except Exception as e:
        mysql.connection.rollback()
        return jsonify({'error': str(e)}), 500
    
    finally:
        cur.close()

@app.route('/api/auth/login', methods=['POST'])
def login():
    """Login user"""
    data = request.get_json()
    
    if not all(k in data for k in ['email', 'password']):
        return jsonify({'error': 'Missing email or password'}), 400
    
    cur = mysql.connection.cursor()
    
    try:
        cur.execute("""
            SELECT user_id, name, email, password_hash, user_type FROM Users 
            WHERE email = %s
        """, (data['email'],))
        
        user = cur.fetchone()
        
        if not user:
            return jsonify({'error': 'Invalid credentials'}), 401
        
        # Verify password
        from werkzeug.security import check_password_hash
        if not check_password_hash(user['password_hash'], data['password']):
            return jsonify({'error': 'Invalid credentials'}), 401
        
        # Create token
        token = jwt.encode({
            'user_id': user['user_id'],
            'user_type': user['user_type'],
            'exp': datetime.utcnow() + timedelta(days=7)
        }, app.config['SECRET_KEY'], algorithm="HS256")
        
        return jsonify({
            'message': 'Login successful',
            'user_id': user['user_id'],
            'name': user['name'],
            'email': user['email'],
            'user_type': user['user_type'],
            'token': token
        }), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500
    
    finally:
        cur.close()

# =====================================================
# BUYER ROUTES
# =====================================================

@app.route('/api/properties', methods=['GET'])
def list_properties():
    """Search and filter properties"""
    try:
        # Get filters from query parameters
        city = request.args.get('city', '')
        min_price = request.args.get('min_price', 0, type=float)
        max_price = request.args.get('max_price', 999999999, type=float)
        bedrooms = request.args.get('bedrooms', 0, type=int)
        property_type = request.args.get('property_type', '')
        page = request.args.get('page', 1, type=int)
        per_page = request.args.get('per_page', 20, type=int)
        
        offset = (page - 1) * per_page
        
        cur = mysql.connection.cursor()
        
        # Build dynamic query
        sql = """
            SELECT 
                p.property_id, p.address, p.city, p.state, p.zipcode,
                p.price, p.bedrooms, p.bathrooms, p.area_sqft,
                p.property_type, p.status, p.listing_date,
                a.name AS agent_name, a.phone AS agent_phone,
                (SELECT image_url FROM Property_Images 
                 WHERE property_id = p.property_id LIMIT 1) AS featured_image
            FROM Properties p
            LEFT JOIN Agents a ON p.agent_id = a.agent_id
            WHERE p.price BETWEEN %s AND %s
              AND p.bedrooms >= %s
              AND p.status = 'Available'
        """
        
        params = [min_price, max_price, bedrooms]
        
        if city:
            sql += " AND LOWER(p.city) LIKE LOWER(%s)"
            params.append(f"%{city}%")
        
        if property_type:
            sql += " AND p.property_type = %s"
            params.append(property_type)
        
        # Add pagination
        sql += f" ORDER BY p.listing_date DESC LIMIT {per_page} OFFSET {offset}"
        
        cur.execute(sql, params)
        properties = cur.fetchall()
        
        # Get total count for pagination
        count_sql = """
            SELECT COUNT(*) as total FROM Properties p
            WHERE p.price BETWEEN %s AND %s
              AND p.bedrooms >= %s
              AND p.status = 'Available'
        """
        
        count_params = [min_price, max_price, bedrooms]
        if city:
            count_sql += " AND LOWER(p.city) LIKE LOWER(%s)"
            count_params.append(f"%{city}%")
        if property_type:
            count_sql += " AND p.property_type = %s"
            count_params.append(property_type)
        
        cur.execute(count_sql, count_params)
        total = cur.fetchone()['total']
        
        cur.close()
        
        return jsonify({
            'properties': dict_to_json_serializable(properties),
            'pagination': {
                'page': page,
                'per_page': per_page,
                'total': total,
                'pages': (total + per_page - 1) // per_page
            }
        }), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/properties/<int:property_id>', methods=['GET'])
def get_property(property_id):
    """Get property details with images"""
    try:
        cur = mysql.connection.cursor()
        
        # Get property details
        cur.execute("""
            SELECT 
                p.*,
                a.name AS agent_name,
                a.phone AS agent_phone,
                a.email AS agent_email,
                a.commission_rate,
                u.name AS seller_name,
                u.phone AS seller_phone
            FROM Properties p
            LEFT JOIN Agents a ON p.agent_id = a.agent_id
            LEFT JOIN Users u ON p.seller_id = u.user_id
            WHERE p.property_id = %s
        """, (property_id,))
        
        property_data = cur.fetchone()
        
        if not property_data:
            return jsonify({'error': 'Property not found'}), 404
        
        # Get images
        cur.execute("""
            SELECT image_id, image_url, image_order
            FROM Property_Images
            WHERE property_id = %s
            ORDER BY image_order
        """, (property_id,))
        
        images = cur.fetchall()
        
        # Get price history
        cur.execute("""
            SELECT old_price, new_price, change_date
            FROM Price_History
            WHERE property_id = %s
            ORDER BY change_date DESC
            LIMIT 5
        """, (property_id,))
        
        price_history = cur.fetchall()
        
        cur.close()
        
        property_data['images'] = dict_to_json_serializable(images)
        property_data['price_history'] = dict_to_json_serializable(price_history)
        
        return jsonify(dict_to_json_serializable(property_data)), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/favorites', methods=['POST'])
@token_required
def add_favorite():
    """Save property to favorites"""
    try:
        data = request.get_json()
        
        if 'property_id' not in data:
            return jsonify({'error': 'Missing property_id'}), 400
        
        cur = mysql.connection.cursor()
        
        # Check if property exists
        cur.execute("SELECT property_id FROM Properties WHERE property_id = %s", 
                   (data['property_id'],))
        if not cur.fetchone():
            return jsonify({'error': 'Property not found'}), 404
        
        # Check if already favorited
        cur.execute("""
            SELECT favorite_id FROM Favorites 
            WHERE user_id = %s AND property_id = %s
        """, (request.user_id, data['property_id']))
        
        if cur.fetchone():
            return jsonify({'error': 'Already in favorites'}), 409
        
        # Add favorite
        cur.execute("""
            INSERT INTO Favorites (user_id, property_id)
            VALUES (%s, %s)
        """, (request.user_id, data['property_id']))
        
        mysql.connection.commit()
        cur.close()
        
        return jsonify({'message': 'Added to favorites'}), 201
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/favorites/<int:favorite_id>', methods=['DELETE'])
@token_required
def remove_favorite(favorite_id):
    """Remove property from favorites"""
    try:
        cur = mysql.connection.cursor()
        
        # Verify ownership
        cur.execute("""
            SELECT user_id FROM Favorites 
            WHERE favorite_id = %s
        """, (favorite_id,))
        
        fav = cur.fetchone()
        if not fav or fav['user_id'] != request.user_id:
            return jsonify({'error': 'Unauthorized'}), 403
        
        # Delete
        cur.execute("DELETE FROM Favorites WHERE favorite_id = %s", (favorite_id,))
        mysql.connection.commit()
        cur.close()
        
        return jsonify({'message': 'Removed from favorites'}), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/buyer/<int:user_id>/dashboard', methods=['GET'])
@token_required
def buyer_dashboard(user_id):
    """Get buyer dashboard data"""
    try:
        if user_id != request.user_id and request.user_type != 'Admin':
            return jsonify({'error': 'Unauthorized'}), 403
        
        cur = mysql.connection.cursor()
        
        # Get favorites
        cur.execute("""
            SELECT 
                f.favorite_id,
                p.property_id, p.address, p.city, p.price,
                p.bedrooms, p.bathrooms, f.saved_date,
                (SELECT image_url FROM Property_Images 
                 WHERE property_id = p.property_id LIMIT 1) AS image_url
            FROM Favorites f
            JOIN Properties p ON f.property_id = p.property_id
            WHERE f.user_id = %s
            ORDER BY f.saved_date DESC
        """, (user_id,))
        
        favorites = cur.fetchall()
        
        # Get appointments
        cur.execute("""
            SELECT 
                a.appointment_id, a.appointment_date, a.status,
                p.property_id, p.address, p.city,
                ag.name AS agent_name, ag.phone AS agent_phone
            FROM Appointments a
            JOIN Properties p ON a.property_id = p.property_id
            LEFT JOIN Agents ag ON a.agent_id = ag.agent_id
            WHERE a.buyer_id = %s
            ORDER BY a.appointment_date DESC
        """, (user_id,))
        
        appointments = cur.fetchall()
        
        cur.close()
        
        return jsonify({
            'favorites': dict_to_json_serializable(favorites),
            'appointments': dict_to_json_serializable(appointments)
        }), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/appointments', methods=['POST'])
@token_required
def schedule_appointment():
    """Schedule property viewing appointment"""
    try:
        data = request.get_json()
        
        required = ['property_id', 'appointment_date']
        if not all(k in data for k in required):
            return jsonify({'error': 'Missing required fields'}), 400
        
        cur = mysql.connection.cursor()
        
        # Check property exists
        cur.execute("SELECT property_id FROM Properties WHERE property_id = %s",
                   (data['property_id'],))
        if not cur.fetchone():
            return jsonify({'error': 'Property not found'}), 404
        
        try:
            # Insert appointment
            cur.execute("""
                INSERT INTO Appointments 
                (property_id, buyer_id, appointment_date, appointment_type, status)
                VALUES (%s, %s, %s, %s, %s)
            """, (data['property_id'], request.user_id, 
                  data['appointment_date'],
                  data.get('appointment_type', 'In-Person'),
                  'Scheduled'))
            
            mysql.connection.commit()
            appointment_id = cur.lastrowid
            
            return jsonify({
                'message': 'Appointment scheduled',
                'appointment_id': appointment_id
            }), 201
        
        except Exception as e:
            mysql.connection.rollback()
            return jsonify({'error': str(e)}), 500
        
        finally:
            cur.close()
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# =====================================================
# SELLER ROUTES
# =====================================================

@app.route('/api/seller/properties', methods=['POST'])
@token_required
@role_required('Seller')
def create_property():
    """Create new property listing"""
    try:
        data = request.get_json()
        
        required = ['address', 'city', 'state', 'zipcode', 'price', 'bedrooms', 'bathrooms', 'area_sqft']
        if not all(k in data for k in required):
            return jsonify({'error': 'Missing required fields'}), 400
        
        cur = mysql.connection.cursor()
        
        try:
            cur.execute("""
                INSERT INTO Properties
                (address, city, state, zipcode, price, bedrooms, bathrooms,
                 area_sqft, property_type, status, seller_id, agent_id)
                VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s)
            """, (
                data['address'], data['city'], data['state'], data['zipcode'],
                data['price'], data['bedrooms'], data['bathrooms'], data['area_sqft'],
                data.get('property_type', 'House'), 'Available',
                request.user_id, data.get('agent_id', 1)
            ))
            
            mysql.connection.commit()
            property_id = cur.lastrowid
            
            return jsonify({
                'message': 'Property created',
                'property_id': property_id
            }), 201
        
        except Exception as e:
            mysql.connection.rollback()
            return jsonify({'error': str(e)}), 500
        
        finally:
            cur.close()
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/seller/properties/<int:property_id>', methods=['PUT'])
@token_required
@role_required('Seller')
def update_property(property_id):
    """Update property listing"""
    try:
        data = request.get_json()
        cur = mysql.connection.cursor()
        
        # Verify ownership
        cur.execute("SELECT seller_id FROM Properties WHERE property_id = %s", (property_id,))
        prop = cur.fetchone()
        
        if not prop or prop['seller_id'] != request.user_id:
            return jsonify({'error': 'Unauthorized'}), 403
        
        # Build dynamic update
        allowed_fields = ['price', 'bedrooms', 'bathrooms', 'area_sqft', 'status', 'description']
        update_fields = []
        params = []
        
        for field in allowed_fields:
            if field in data:
                update_fields.append(f"{field} = %s")
                params.append(data[field])
        
        if not update_fields:
            return jsonify({'error': 'No fields to update'}), 400
        
        params.append(property_id)
        
        sql = f"UPDATE Properties SET {', '.join(update_fields)} WHERE property_id = %s"
        
        try:
            cur.execute(sql, params)
            mysql.connection.commit()
            
            return jsonify({'message': 'Property updated'}), 200
        
        except Exception as e:
            mysql.connection.rollback()
            return jsonify({'error': str(e)}), 500
        
        finally:
            cur.close()
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/seller/properties/<int:property_id>', methods=['DELETE'])
@token_required
@role_required('Seller')
def delete_property(property_id):
    """Delete property listing"""
    try:
        cur = mysql.connection.cursor()
        
        # Verify ownership
        cur.execute("SELECT seller_id FROM Properties WHERE property_id = %s", (property_id,))
        prop = cur.fetchone()
        
        if not prop or prop['seller_id'] != request.user_id:
            return jsonify({'error': 'Unauthorized'}), 403
        
        # Check if property is sold
        if prop['status'] == 'Sold':
            return jsonify({'error': 'Cannot delete sold property'}), 400
        
        try:
            cur.execute("DELETE FROM Properties WHERE property_id = %s", (property_id,))
            mysql.connection.commit()
            
            return jsonify({'message': 'Property deleted'}), 200
        
        except Exception as e:
            mysql.connection.rollback()
            return jsonify({'error': str(e)}), 500
        
        finally:
            cur.close()
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/seller/<int:seller_id>/dashboard', methods=['GET'])
@token_required
def seller_dashboard(seller_id):
    """Get seller dashboard data"""
    try:
        if seller_id != request.user_id and request.user_type != 'Admin':
            return jsonify({'error': 'Unauthorized'}), 403
        
        cur = mysql.connection.cursor()
        
        # Get stats
        cur.execute("""
            SELECT 
                COUNT(CASE WHEN status = 'Available' THEN 1 END) AS active_listings,
                COUNT(CASE WHEN status = 'Pending' THEN 1 END) AS pending_listings,
                COUNT(CASE WHEN status = 'Sold' THEN 1 END) AS sold_listings,
                SUM(CASE WHEN status = 'Sold' THEN price ELSE 0 END) AS total_sales_value
            FROM Properties
            WHERE seller_id = %s
        """, (seller_id,))
        
        stats = cur.fetchone()
        
        # Get properties
        cur.execute("""
            SELECT 
                p.property_id, p.address, p.city, p.price,
                p.bedrooms, p.bathrooms, p.status, p.listing_date,
                (SELECT COUNT(*) FROM Appointments 
                 WHERE property_id = p.property_id) AS view_count
            FROM Properties p
            WHERE p.seller_id = %s
            ORDER BY p.listing_date DESC
        """, (seller_id,))
        
        properties = cur.fetchall()
        cur.close()
        
        return jsonify({
            'stats': dict_to_json_serializable(stats),
            'properties': dict_to_json_serializable(properties)
        }), 200
    
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# =====================================================
# ERROR HANDLERS
# =====================================================

@app.errorhandler(404)
def not_found(error):
    return jsonify({'error': 'Endpoint not found'}), 404

@app.errorhandler(500)
def server_error(error):
    return jsonify({'error': 'Internal server error'}), 500

@app.route('/api/health', methods=['GET'])
def health():
    """Health check endpoint"""
    return jsonify({'status': 'API is running'}), 200

# =====================================================
# RUN APP
# =====================================================

if __name__ == '__main__':
    print("🚀 Real Estate API Starting...")
    print("🔗 Running on http://127.0.0.1:5000")
    print("📚 API Documentation will be at /api/docs")
    app.run(host='127.0.0.1', port=5000, debug=True)
```

---

### **
