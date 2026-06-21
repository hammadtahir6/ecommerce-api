# 🛒 Mini E-Commerce API

A multi-table RESTful API built with **FastAPI** and **PostgreSQL** that models a real shopping flow — products, cart, checkout, and orders — with JWT authentication and inventory management.

---

## 🛠️ Tech Stack

- Python 3.13
- FastAPI
- SQLAlchemy (with relational models)
- PostgreSQL
- JWT (python-jose) + bcrypt password hashing
- Pydantic (nested schemas)

---

## 🔐 Key Features

- User registration & JWT-based login
- Product catalog with stock tracking
- Add-to-cart with automatic quantity merging and stock validation
- **Checkout flow** that:
  - Validates stock for every cart item before processing
  - Creates an order with line items, snapshotting price at time of purchase
  - Reduces product stock atomically
  - Clears the cart — all in a single database transaction
- Order history with fully nested product details (no extra API calls needed)

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/hammadtahir6/ecommerce-api.git
cd ecommerce-api
```

### 2. Install dependencies
```bash
python -m pip install fastapi uvicorn sqlalchemy psycopg2-binary python-dotenv "python-jose[cryptography]" "passlib[bcrypt]" "bcrypt==4.0.1" python-multipart "pydantic[email]"
```

### 3. Create a `.env` file
DATABASE_URL=postgresql://postgres:yourpassword@localhost:5432/ecommerce

SECRET_KEY=your-super-secret-key

ALGORITHM=HS256

ACCESS_TOKEN_EXPIRE_MINUTES=30

### 4. Run the server
```bash
python -m uvicorn main:app --reload
```

### 5. Open API docs
http://127.0.0.1:8000/docs

---

## 📌 API Endpoints

| Method | Endpoint | Auth required | Description |
|--------|----------|----------------|-------------|
| POST | `/register` | ❌ | Create a new user account |
| POST | `/login` | ❌ | Login, returns a JWT access token |
| POST | `/products` | ✅ | Add a new product |
| GET | `/products` | ❌ | Browse all products |
| GET | `/products/{id}` | ❌ | Get a single product |
| POST | `/cart` | ✅ | Add a product to your cart |
| GET | `/cart` | ✅ | View your cart (with nested product info) |
| DELETE | `/cart/{item_id}` | ✅ | Remove an item from your cart |
| POST | `/checkout` | ✅ | Convert your cart into an order |
| GET | `/orders` | ✅ | View your order history |

---

## 🏗️ How checkout works under the hood

1. Fetch all items in the user's cart
2. Validate stock is available for **every** item before changing anything
3. Calculate the order total
4. Create the `Order`, then `flush()` to get its ID
5. Create an `OrderItem` for each product — storing `price_at_purchase` so historical orders never change even if prices update later
6. Reduce `stock` on each product
7. Clear the cart
8. Commit everything together as one transaction — if anything fails mid-way, nothing is saved

---

## 📁 Project Structure
ecommerce-api/

├── main.py        # Routes + checkout business logic

├── database.py    # Database connection

├── models.py       # User, Product, CartItem, Order, OrderItem + relationships

├── schemas.py      # Request/response shapes (incl. nested schemas)

├── auth.py         # Password hashing + JWT

├── .env            # Environment variables (not uploaded)

└── README.md

---

## 👨‍💻 Author

**Hammad Tahir** — [GitHub](https://github.com/hammadtahir6) · [LinkedIn](https://www.linkedin.com/in/hammad-tahir-69a77b25b/)