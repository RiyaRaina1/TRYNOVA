# TRYNOVA Project Report

## 1. Project Overview
TRYNOVA is a full-stack luxury streetwear e-commerce platform with:
- Customer storefront and account dashboard
- Admin dashboard for product/order/user/coupon management
- Primary Java Jakarta EE backend (MySQL)
- Secondary Node.js Express backend (MongoDB)

Primary repo reference:
- [README.md](README.md)

## 2. Business Scope and Features
Core business features implemented:
- Product catalog with search, category filter, size filter, price filter, and sort
- Product detail page with recommendations
- Cart and checkout flow with coupon support
- User registration, login, logout, and session tracking
- Customer dashboard with profile, orders, and wishlist
- Admin dashboard with analytics, order status management, product CRUD, user block/unblock, coupon creation, and invoice PDF generation
- Hybrid online/offline behavior (API-first with localStorage fallback)

Frontend entry pages:
- [frontend/index.html](frontend/index.html)
- [frontend/shop.html](frontend/shop.html)
- [frontend/product.html](frontend/product.html)
- [frontend/cart.html](frontend/cart.html)
- [frontend/checkout.html](frontend/checkout.html)
- [frontend/login.html](frontend/login.html)
- [frontend/register.html](frontend/register.html)
- [frontend/dashboard.html](frontend/dashboard.html)
- [frontend/admin-login.html](frontend/admin-login.html)
- [frontend/admin.html](frontend/admin.html)
- [frontend/about.html](frontend/about.html)
- [frontend/contact.html](frontend/contact.html)

## 3. High-Level Architecture
Architecture style:
- Multi-module monorepo with independent frontend, Java backend, and Node backend
- Frontend targets Java API by default at runtime
- Node backend exists as a secondary/alternative REST service

Main modules:
- Frontend static site: [frontend](frontend)
- Java backend: [backend-java](backend-java)
- Node backend: [backend-node](backend-node)
- SQL schema/seed: [database](database)
- WildFly datasource config: [config/wildfly-datasource.cli](config/wildfly-datasource.cli)

## 4. Technology Stack
Frontend stack:
- HTML5, CSS3, JavaScript
- Bootstrap 5.3.3 via CDN
- Chart.js for admin analytics chart
- jsPDF for invoice export
- Google Fonts (Cormorant Garamond, Sora)

Frontend references:
- [frontend/assets/css/styles.css](frontend/assets/css/styles.css)
- [frontend/assets/js/main.js](frontend/assets/js/main.js)
- [frontend/assets/js/admin.js](frontend/assets/js/admin.js)

Java backend stack:
- Java 17
- Jakarta EE 10 (Servlets, JSP, JPA, EJB)
- Hibernate ORM 6.4.8.Final
- Jackson databind 2.17.1
- Maven WAR packaging
- WildFly/JBoss deployment target

Java references:
- [backend-java/pom.xml](backend-java/pom.xml)
- [backend-java/src/main/resources/META-INF/persistence.xml](backend-java/src/main/resources/META-INF/persistence.xml)
- [backend-java/src/main/webapp/WEB-INF/web.xml](backend-java/src/main/webapp/WEB-INF/web.xml)

Node backend stack:
- Node.js (ES modules)
- Express 4.19.2
- Mongoose 8.5.0
- Helmet, CORS, Morgan, rate limiter
- JWT middleware (optional auth)
- Vitest + Supertest for tests

Node references:
- [backend-node/package.json](backend-node/package.json)
- [backend-node/src/app.js](backend-node/src/app.js)
- [backend-node/tests/app.test.js](backend-node/tests/app.test.js)

Databases:
- MySQL for Java backend domain
- MongoDB for Node backend domain

SQL references:
- [database/schema.sql](database/schema.sql)
- [database/seed.sql](database/seed.sql)

## 5. Repository Structure (Functional)
Top-level folders and purpose:
- frontend: UI pages and client logic
- backend-java: primary enterprise backend and business logic
- backend-node: secondary REST module
- database: MySQL schema and seed data
- config: infra/runtime setup scripts
- assets, controllers, models, services: documentation placeholders in this repo state

## 6. Java Backend Details
### 6.1 Packaging and Deployment
- Artifact: trynova-java.war
- Context root: /trynova-java
- Session timeout: 30 minutes
- Welcome page: index.jsp

References:
- [backend-java/pom.xml](backend-java/pom.xml)
- [backend-java/src/main/webapp/WEB-INF/jboss-web.xml](backend-java/src/main/webapp/WEB-INF/jboss-web.xml)
- [backend-java/src/main/webapp/WEB-INF/web.xml](backend-java/src/main/webapp/WEB-INF/web.xml)
- [backend-java/src/main/webapp/index.jsp](backend-java/src/main/webapp/index.jsp)

### 6.2 Persistence and Domain Model
Persistence unit:
- Name: trynovaPU
- Transaction type: JTA
- JNDI datasource: java:jboss/datasources/TrynovaDS

Entity model:
- User: name, email, password, role, blocked
- UserProfile: phone, address, city (1:1 with user)
- Product: name, category, price, stock, image, description, createdAt
- CustomerOrder: user, total, status, createdAt
- OrderItem: order, product, quantity, price
- Review: user, product, rating, comment
- Coupon: code, discount, expiry, active
- PaymentDetails: order, method, paymentStatus, transactionRef

References:
- [backend-java/src/main/resources/META-INF/persistence.xml](backend-java/src/main/resources/META-INF/persistence.xml)
- [backend-java/src/main/java/com/trynova/entity/User.java](backend-java/src/main/java/com/trynova/entity/User.java)
- [backend-java/src/main/java/com/trynova/entity/UserProfile.java](backend-java/src/main/java/com/trynova/entity/UserProfile.java)
- [backend-java/src/main/java/com/trynova/entity/Product.java](backend-java/src/main/java/com/trynova/entity/Product.java)
- [backend-java/src/main/java/com/trynova/entity/CustomerOrder.java](backend-java/src/main/java/com/trynova/entity/CustomerOrder.java)
- [backend-java/src/main/java/com/trynova/entity/OrderItem.java](backend-java/src/main/java/com/trynova/entity/OrderItem.java)
- [backend-java/src/main/java/com/trynova/entity/Review.java](backend-java/src/main/java/com/trynova/entity/Review.java)
- [backend-java/src/main/java/com/trynova/entity/Coupon.java](backend-java/src/main/java/com/trynova/entity/Coupon.java)
- [backend-java/src/main/java/com/trynova/entity/PaymentDetails.java](backend-java/src/main/java/com/trynova/entity/PaymentDetails.java)

### 6.3 Business Services (EJB)
- InventoryBean: pessimistic lock stock reservation and decrement
- OrderValidationBean: payload validation using jakarta.validation
- PaymentBean: payment status simulation and transaction reference generation

References:
- [backend-java/src/main/java/com/trynova/service/InventoryBean.java](backend-java/src/main/java/com/trynova/service/InventoryBean.java)
- [backend-java/src/main/java/com/trynova/service/OrderValidationBean.java](backend-java/src/main/java/com/trynova/service/OrderValidationBean.java)
- [backend-java/src/main/java/com/trynova/service/PaymentBean.java](backend-java/src/main/java/com/trynova/service/PaymentBean.java)

### 6.4 Utility Layer
- JsonUtil: JSON request/response helper with Jackson
- PasswordUtil: SHA-256 hashing and password matching
- SessionUtil: auth/admin checks and guard methods
- CorsFilter: CORS headers on /api/*

References:
- [backend-java/src/main/java/com/trynova/util/JsonUtil.java](backend-java/src/main/java/com/trynova/util/JsonUtil.java)
- [backend-java/src/main/java/com/trynova/util/PasswordUtil.java](backend-java/src/main/java/com/trynova/util/PasswordUtil.java)
- [backend-java/src/main/java/com/trynova/util/SessionUtil.java](backend-java/src/main/java/com/trynova/util/SessionUtil.java)
- [backend-java/src/main/java/com/trynova/util/CorsFilter.java](backend-java/src/main/java/com/trynova/util/CorsFilter.java)

### 6.5 Java API Endpoints
Authentication:
- GET /api/auth/login: session status
- POST /api/auth/login: login
- POST /api/auth/register: register
- GET /api/auth/logout: logout

Product:
- GET /api/products: list/filter
- GET /api/products?id=:id: get by id
- POST /api/products: create (admin required)
- PUT /api/products: update by body.id (admin required)
- DELETE /api/products?id=:id: delete (admin required)

Order:
- GET /api/orders: list own orders, or all for admin
- POST /api/orders: place order

Admin:
- GET /api/admin: dashboard totals and monthly sales
- GET /api/admin?action=users: customer list with search
- GET /api/admin?action=coupons: coupon list
- GET /api/admin?action=reports: best-selling and top customers
- POST /api/admin with action:
- action=updateOrderStatus
- action=blockUser
- action=createCoupon

Servlet references:
- [backend-java/src/main/java/com/trynova/servlet/RegisterServlet.java](backend-java/src/main/java/com/trynova/servlet/RegisterServlet.java)
- [backend-java/src/main/java/com/trynova/servlet/LoginServlet.java](backend-java/src/main/java/com/trynova/servlet/LoginServlet.java)
- [backend-java/src/main/java/com/trynova/servlet/LogoutServlet.java](backend-java/src/main/java/com/trynova/servlet/LogoutServlet.java)
- [backend-java/src/main/java/com/trynova/servlet/ProductServlet.java](backend-java/src/main/java/com/trynova/servlet/ProductServlet.java)
- [backend-java/src/main/java/com/trynova/servlet/OrderServlet.java](backend-java/src/main/java/com/trynova/servlet/OrderServlet.java)
- [backend-java/src/main/java/com/trynova/servlet/AdminServlet.java](backend-java/src/main/java/com/trynova/servlet/AdminServlet.java)

## 7. Node Backend Details
### 7.1 Runtime and App Pipeline
- Server starts after MongoDB connection
- Middleware: helmet, cors, json parser, urlencoded parser, morgan, rate limiter
- Health endpoint: GET /health

References:
- [backend-node/src/server.js](backend-node/src/server.js)
- [backend-node/src/config/db.js](backend-node/src/config/db.js)
- [backend-node/src/app.js](backend-node/src/app.js)

### 7.2 Node Data Model
- Product: name, category, price, stock, image, description
- Order: userEmail, total, status enum, items[]
- User: name, email, role, blocked

References:
- [backend-node/src/models/product.model.js](backend-node/src/models/product.model.js)
- [backend-node/src/models/order.model.js](backend-node/src/models/order.model.js)
- [backend-node/src/models/user.model.js](backend-node/src/models/user.model.js)

### 7.3 Node API Endpoints
Products:
- GET /api/products
- GET /api/products/:id
- POST /api/products
- PUT /api/products/:id
- DELETE /api/products/:id

Orders:
- GET /api/orders
- POST /api/orders

Users:
- GET /api/users

Route/controller references:
- [backend-node/src/routes/index.js](backend-node/src/routes/index.js)
- [backend-node/src/routes/products.routes.js](backend-node/src/routes/products.routes.js)
- [backend-node/src/routes/orders.routes.js](backend-node/src/routes/orders.routes.js)
- [backend-node/src/routes/users.routes.js](backend-node/src/routes/users.routes.js)
- [backend-node/src/controllers/products.controller.js](backend-node/src/controllers/products.controller.js)
- [backend-node/src/controllers/orders.controller.js](backend-node/src/controllers/orders.controller.js)
- [backend-node/src/controllers/users.controller.js](backend-node/src/controllers/users.controller.js)

### 7.4 Node Security/Middleware
- optionalJwtAuth parses bearer token if present
- Does not enforce mandatory auth by itself
- notFound and global error handler implemented

References:
- [backend-node/src/middleware/auth.middleware.js](backend-node/src/middleware/auth.middleware.js)
- [backend-node/src/middleware/error.middleware.js](backend-node/src/middleware/error.middleware.js)

## 8. Frontend Application Details
### 8.1 State and Data Strategy
Client state in localStorage:
- trynova_products
- trynova_user
- trynova_cart
- trynova_wishlist
- trynova_orders
- trynova_theme
- trynova_coupon_code
- trynova_discount

Frontend integration strategy:
- API base from localStorage key trynova_api_base
- Default Java backend base: http://localhost:8080/trynova-java/api
- Uses credentials: include for session cookies
- If backend unreachable, many features fallback to offline localStorage mode

References:
- [frontend/assets/js/api.js](frontend/assets/js/api.js)
- [frontend/assets/js/main.js](frontend/assets/js/main.js)

### 8.2 Frontend Functional Scripts
- data.js: static products, testimonials, order statuses
- home.js: featured, arrivals, testimonials, quick add
- shop.js: filter and sort engine
- product.js: product detail and recommendations
- cart.js: quantity, remove, coupon apply
- checkout.js: order placement online/offline
- auth.js: register/login/forgot flows, offline admin seed
- dashboard.js: customer profile, orders, wishlist
- admin.js: full admin workflow including charts, invoice PDF, coupons

Script references:
- [frontend/assets/js/data.js](frontend/assets/js/data.js)
- [frontend/assets/js/home.js](frontend/assets/js/home.js)
- [frontend/assets/js/shop.js](frontend/assets/js/shop.js)
- [frontend/assets/js/product.js](frontend/assets/js/product.js)
- [frontend/assets/js/cart.js](frontend/assets/js/cart.js)
- [frontend/assets/js/checkout.js](frontend/assets/js/checkout.js)
- [frontend/assets/js/auth.js](frontend/assets/js/auth.js)
- [frontend/assets/js/dashboard.js](frontend/assets/js/dashboard.js)
- [frontend/assets/js/admin.js](frontend/assets/js/admin.js)

### 8.3 UI and Design Characteristics
- Dark luxury theme with black, white, gold palette
- Light mode toggle
- Glassmorphism cards and reveal animations
- Countdown timer for drop campaign
- Search suggestions and responsive layout

References:
- [frontend/assets/css/styles.css](frontend/assets/css/styles.css)
- [frontend/index.html](frontend/index.html)

## 9. Database Design (MySQL)
Tables:
- users
- profiles
- products
- orders
- order_items
- coupons
- reviews
- payment_details

Key relationships:
- users 1:1 profiles
- users 1:N orders
- orders 1:N order_items
- orders 1:1 payment_details
- products 1:N order_items
- users 1:N reviews
- products 1:N reviews

Seed data includes:
- Admin user and customer user
- Example profile
- Initial products
- Coupons GOAT10 and NOVA15

References:
- [database/schema.sql](database/schema.sql)
- [database/seed.sql](database/seed.sql)

## 10. Environment and Setup
### 10.1 Frontend
- Serve [frontend](frontend) as static site
- Default backend endpoint points to Java API

### 10.2 Java Backend
Prerequisites:
- Java 17+
- Maven 3.9+
- WildFly 30+ / compatible JBoss
- MySQL 8+

Build and deploy:
- mvn clean package
- Deploy backend-java/target/trynova-java.war

Datasource/JNDI:
- JNDI: java:jboss/datasources/TrynovaDS
- WildFly CLI script provided

References:
- [backend-java/README.md](backend-java/README.md)
- [config/wildfly-datasource.cli](config/wildfly-datasource.cli)

### 10.3 Node Backend
Prerequisites:
- Node.js 20+
- MongoDB

Run:
- npm install
- configure .env with MONGODB_URI
- npm run dev
- npm test

References:
- [backend-node/README.md](backend-node/README.md)
- [backend-node/package.json](backend-node/package.json)

## 11. Testing and Verification State
Java tests:
- No Java test source files present under backend-java/test in current repo snapshot

Node tests:
- Basic API smoke tests exist for /health and 404 path

Reference:
- [backend-node/tests/app.test.js](backend-node/tests/app.test.js)

## 12. Security Posture (Current)
Implemented:
- Java password hashing with SHA-256
- Java session-based auth and role checks
- Java admin guards for protected operations
- CORS filter enabled for /api/*
- Node middleware stack includes helmet and rate limiting
- Optional JWT verification available in Node

Important observations for report risk section:
- Node routes currently use optional auth and do not enforce admin authorization for product/order/user endpoints
- Java CORS reflects request Origin while allowing credentials, which should be restricted to trusted origins in production
- Frontend offline mode stores user/session-like state in localStorage for demo resilience, not production-grade security

References:
- [backend-java/src/main/java/com/trynova/util/PasswordUtil.java](backend-java/src/main/java/com/trynova/util/PasswordUtil.java)
- [backend-java/src/main/java/com/trynova/util/SessionUtil.java](backend-java/src/main/java/com/trynova/util/SessionUtil.java)
- [backend-java/src/main/java/com/trynova/util/CorsFilter.java](backend-java/src/main/java/com/trynova/util/CorsFilter.java)
- [backend-node/src/middleware/auth.middleware.js](backend-node/src/middleware/auth.middleware.js)
- [frontend/assets/js/auth.js](frontend/assets/js/auth.js)

## 13. Project Metrics (Source-Only Snapshot)
Computed from workspace excluding node_modules and target folders:
- Total files: 88
- Code/document/config files counted: 80
- Approximate total lines: 8,398

Notes:
- If dependencies/build outputs are included, file count and LOC become significantly larger and less representative for report scope.

## 14. Suggested Report Sections for Submission
Suggested structure:
- Abstract
- Objective and Problem Statement
- System Architecture
- Module-wise Implementation
- Database Design and ER Relationships
- API Design
- Security and Validation
- Testing Strategy and Results
- Deployment and Environment Setup
- Limitations and Future Enhancements
- Conclusion

## 15. Quick Reference Index
Main docs:
- [README.md](README.md)
- [backend-java/README.md](backend-java/README.md)
- [backend-node/README.md](backend-node/README.md)

Core backend files:
- [backend-java/src/main/java/com/trynova/servlet](backend-java/src/main/java/com/trynova/servlet)
- [backend-java/src/main/java/com/trynova/service](backend-java/src/main/java/com/trynova/service)
- [backend-java/src/main/java/com/trynova/entity](backend-java/src/main/java/com/trynova/entity)
- [backend-node/src/controllers](backend-node/src/controllers)
- [backend-node/src/routes](backend-node/src/routes)
- [backend-node/src/models](backend-node/src/models)

Frontend core files:
- [frontend/assets/js](frontend/assets/js)
- [frontend/assets/css/styles.css](frontend/assets/css/styles.css)

Database and infra:
- [database/schema.sql](database/schema.sql)
- [database/seed.sql](database/seed.sql)
- [config/wildfly-datasource.cli](config/wildfly-datasource.cli)
