# 🛍️ Instagram Thrift Creator Store — Database Design

A relational database schema for an Instagram-based Thrift & Handmade Creator Store. This system manages customers, orders, products (thrifted & handmade), payments, shipping, and address details.

---

## 📁 Database Schema Overview

The system consists of **11 core tables** covering the full e-commerce lifecycle — from customer registration to order delivery and payment processing.

---

## 🗂️ Tables & Schema

### 1. `Customers`
Stores registered customer information.

| Column | Type | Constraints |
|---|---|---|
| `customer_id` | SERIAL | PRIMARY KEY |
| `name` | VARCHAR(50) | UNIQUE, NOT NULL |
| `email` | VARCHAR(322) | UNIQUE, NOT NULL |
| `phone` | VARCHAR(15) | |
| `password` | VARCHAR(256) | |
| `address` | TEXT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 2. `Orders`
Tracks all customer orders.

| Column | Type | Constraints |
|---|---|---|
| `order_id` | INT | PRIMARY KEY |
| `customer_id` | INT | FOREIGN KEY → `Customers.customer_id` |
| `total_amount` | DECIMAL(19,4) | |
| `order_status` | ENUM | `'pending'`, `'placed'`, `'shipping'`, `'delivered'`, `'processing'`, `'cancelled'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 3. `Order_Items`
Individual line items within an order.

| Column | Type | Constraints |
|---|---|---|
| `order_items_id` | INT | PRIMARY KEY |
| `order_id` | INT | FOREIGN KEY → `Orders.order_id` |
| `product_id` | INT | FOREIGN KEY → `Product.id` |
| `product_name` | VARCHAR(255) | |
| `price` | DECIMAL(19,4) | |
| `quantity` | INT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 4. `Address_details`
Stores delivery addresses linked to customers and orders.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `customer_id` | INT | FOREIGN KEY → `Customers.customer_id` |
| `fullname` | VARCHAR(100) | |
| `country` | UUID(12) | |
| `state` | VARCHAR(100) | |
| `city` | VARCHAR(100) | |
| `pincode` | VARCHAR(20) | |
| `area` | VARCHAR(100) | |
| `landmark` | VARCHAR(128) | |
| `phone` | VARCHAR(20) | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 5. `Shipping_details`
Tracks shipment status and tracking for each order.

| Column | Type | Constraints |
|---|---|---|
| `shipping_id` | SERIAL | PRIMARY KEY |
| `order_id` | INT | FOREIGN KEY → `Orders.order_id` |
| `shipping_status` | VARCHAR(20) | |
| `tracking_number` | VARCHAR(100) | |
| `expected_delivery` | DATE | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 6. `Payment_details`
Records payment transactions per order.

| Column | Type | Constraints |
|---|---|---|
| `payment_id` | VARCHAR(255) | PRIMARY KEY |
| `order_id` | INT | FOREIGN KEY → `Orders.order_id` |
| `transaction_id` | VARCHAR(255) | UNIQUE, NOT NULL |
| `amount` | DECIMAL(19,4) | |
| `payment_method` | ENUM | `'credit-card'`, `'debit-card'`, `'COD'`, `'gift_card'`, `'UPI'` |
| `status` | ENUM | `'pending'`, `'authorized'`, `'completed'`, `'failed'`, `'refunded'` |
| `payment_gateway` | ENUM | `'stripe'`, `'razorpay'`, `'paypal'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 7. `Product`
Master product catalog.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `name` | VARCHAR(255) | |
| `description` | TEXT | |
| `price` | DECIMAL(19,4) | |
| `quantity` | INT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 8. `Product_Type`
Classifies products as either Thrifted or Handmade.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `thrifted` | VARCHAR(50) | |
| `handmade` | VARCHAR(50) | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 9. `Thrifted_Product_Category`
Category details specific to thrifted products.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `size_id` | INT | FOREIGN KEY → `Product_Size.size_id` |
| `color_id` | INT | FOREIGN KEY → `Product_Colors.color_id` |
| `name` | VARCHAR(100) | |
| `category` | UUID(36) | |
| `stock` | INT | |
| `condition` | VARCHAR | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 10. `Handmade_Product_Category`
Category details specific to handmade products.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `size_id` | INT | FOREIGN KEY → `Product_Size.size_id` |
| `color_id` | INT | FOREIGN KEY → `Product_Colors.color_id` |
| `name` | VARCHAR(100) | |
| `category` | UUID(36) | |
| `stock` | INT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 11. `Product_Size`
Standardized size labels for products.

| Column | Type | Constraints |
|---|---|---|
| `size_id` | SERIAL | PRIMARY KEY |
| `size_label` | ENUM | `'XXS'`, `'XS'`, `'S'`, `'M'`, `'L'`, `'XL'`, `'XXL'` |
| `size_group` | ENUM | `'EU Footwear'`, `'US Alpha'`, `'Inches'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 12. `Product_Colors`
Color options available for products.

| Column | Type | Constraints |
|---|---|---|
| `color_id` | SERIAL | PRIMARY KEY |
| `color_name` | VARCHAR | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

## 🔗 Entity Relationships

```
Customers (1) ──────────── (M) Orders
Orders (1) ─────────────── (M) Order_Items
Orders (1) ─────────────── (1) Address_details
Orders (1) ─────────────── (1) Shipping_details
Orders (1) ─────────────── (1) Payment_details
Order_Items (M) ────────── (1) Product
Product (1) ────────────── (M) Product_Type
Product_Type (1) ───────── (1) Thrifted_Product_Category
Product_Type (1) ───────── (1) Handmade_Product_Category
Thrifted_Product_Category > Product_Size
Thrifted_Product_Category > Product_Colors
Handmade_Product_Category > Product_Size
Handmade_Product_Category > Product_Colors
```

### Relationship Summary

| From | Relation | To | Via |
|---|---|---|---|
| `Customers` | One-to-Many | `Orders` | `customer_id` |
| `Orders` | One-to-Many | `Order_Items` | `order_id` |
| `Orders` | One-to-One | `Address_details` | `order_id` |
| `Orders` | One-to-One | `Shipping_details` | `order_id` |
| `Orders` | One-to-One | `Payment_details` | `order_id` |
| `Order_Items` | Many-to-One | `Product` | `product_id` |
| `Product` | One-to-Many | `Product_Type` | `id` |
| `Product_Type` | One-to-One | `Thrifted_Product_Category` | `id` |
| `Product_Type` | One-to-One | `Handmade_Product_Category` | `id` |
| `Thrifted_Product_Category` | Many-to-One | `Product_Size` | `size_id` |
| `Thrifted_Product_Category` | Many-to-One | `Product_Colors` | `color_id` |
| `Handmade_Product_Category` | Many-to-One | `Product_Size` | `size_id` |
| `Handmade_Product_Category` | Many-to-One | `Product_Colors` | `color_id` |

---

## 🔄 System Flow

```
1. Customer Registers
        ↓
2. Customer Browses Products
   (filtered by type → Thrifted / Handmade → size, color, condition)
        ↓
3. Customer Places Order
   (Order created with line items → Order_Items)
        ↓
4. Address Captured
   (Address_details linked to Order)
        ↓
5. Payment Processed
   (Payment_details — UPI / Card / COD via Razorpay / Stripe / PayPal)
        ↓
6. Order Shipped
   (Shipping_details created with tracking number & expected delivery)
        ↓
7. Order Delivered
   (order_status → 'delivered', shipping_status updated)
```

---

## 📊 ENUM Reference

| Table | Column | Values |
|---|---|---|
| `Orders` | `order_status` | `pending`, `placed`, `shipping`, `delivered`, `processing`, `cancelled` |
| `Payment_details` | `payment_method` | `credit-card`, `debit-card`, `COD`, `gift_card`, `UPI` |
| `Payment_details` | `status` | `pending`, `authorized`, `completed`, `failed`, `refunded` |
| `Payment_details` | `payment_gateway` | `stripe`, `razorpay`, `paypal` |
| `Product_Size` | `size_label` | `XXS`, `XS`, `S`, `M`, `L`, `XL`, `XXL` |
| `Product_Size` | `size_group` | `EU Footwear`, `US Alpha`, `Inches` |

---

## 🏗️ Key Design Decisions

- **Thrifted vs Handmade split** — `Product_Type` separates the two product lines, each with its own category table. Thrifted products have an extra `condition` field (e.g., mint, good, fair).
- **Flexible sizing** — `Product_Size` supports multiple sizing systems (EU, US Alpha, Inches) making it suitable for clothing and footwear.
- **Multi-gateway payments** — Supports Razorpay (India), Stripe, and PayPal for global reach, with UPI as a payment method for Indian customers.
- **Address stored per order** — `Address_details` is linked to both `Customers` and `Orders` to preserve the delivery address at time of purchase.
- **Stock tracked at category level** — Both `Thrifted_Product_Category` and `Handmade_Product_Category` maintain their own `stock` count for accurate inventory.

---

## 🛠️ Tech Stack Recommendation

| Layer | Suggestion |
|---|---|
| Database | PostgreSQL |
