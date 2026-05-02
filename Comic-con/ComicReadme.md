# 🎪 Comic-Con Parking System

A comprehensive parking management system designed for large-scale events like Comic-Con. This system handles user registration, vehicle tracking, zone management, session tracking, ticketing, and payments.

---

## 📁 Database Schema Overview

The system consists of **9 core tables** organized around the parking lifecycle: from user registration to payment processing.

---

## 🗂️ Tables & Schema

### 1. `USERS`
Stores registered user information.

| Column | Type | Constraints |
|---|---|---|
| `user_id` | SERIAL | PRIMARY KEY |
| `full_name` | VARCHAR(100) | NOT NULL |
| `email` | VARCHAR(322) | NOT NULL |
| `phone_number` | VARCHAR(15) | |

---

### 2. `VEHICLES_CATEGORIES`
Defines the types of vehicles allowed in the parking system.

| Column | Type | Constraints |
|---|---|---|
| `vehicle_category_id` | SERIAL | PRIMARY KEY |
| `category_name` | ENUM | `'bikes'`, `'cars'`, `'SUVs'`, `'cabs'`, `'EV Vehicles'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 3. `VEHICLES`
Tracks all registered vehicles linked to users.

| Column | Type | Constraints |
|---|---|---|
| `vehicle_id` | SERIAL | PRIMARY KEY |
| `name` | VARCHAR(100) | |
| `user_id` | INT | FOREIGN KEY → `USERS.user_id` |
| `vehicle_category_id` | INT | FOREIGN KEY → `VEHICLES_CATEGORIES.vehicle_category_id` |
| `license_plate` | VARCHAR | UNIQUE |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 4. `PARKING_ZONES_LEVEL`
Defines parking zones and their security levels.

| Column | Type | Constraints |
|---|---|---|
| `zone_level_id` | SERIAL | PRIMARY KEY |
| `zone_name` | ENUM | `'A'`, `'B'`, `'C'`, `'D'` |
| `level_number` | INT | |
| `security_level` | VARCHAR | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 5. `PARKING_SPOT_CATEGORIES`
Defines categories of parking spots and their pricing.

| Column | Type | Constraints |
|---|---|---|
| `spot_categories_id` | SERIAL | PRIMARY KEY |
| `category_name` | ENUM | `'bikes'`, `'cars'`, `'cabs'`, `'EV Vehicles'` |
| `price_per_hour` | INT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 6. `PARKING_SPOTS`
Represents individual physical parking spots.

| Column | Type | Constraints |
|---|---|---|
| `spot_id` | SERIAL | PRIMARY KEY, NOT NULL |
| `spot_number` | INT | |
| `zone_level_id` | INT | FOREIGN KEY → `PARKING_ZONES_LEVEL.zone_level_id` |
| `spot_category_id` | INT | FOREIGN KEY → `PARKING_SPOT_CATEGORIES.spot_categories_id` |
| `is_functional` | BOOLEAN | |
| `is_available` | BOOLEAN | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 7. `PARKING_SESSIONS`
Tracks active and completed parking sessions.

| Column | Type | Constraints |
|---|---|---|
| `session_id` | SERIAL | PRIMARY KEY, UNIQUE, NOT NULL |
| `vehicle_id` | INT | FOREIGN KEY → `VEHICLES.vehicle_id` |
| `spot_id` | INT | FOREIGN KEY → `PARKING_SPOTS.spot_id` |
| `start_time` | TIMESTAMP | |
| `end_time` | TIMESTAMP | |
| `session_status` | ENUM | `'active'`, `'completed'`, `'overdue'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 8. `PARKING_TICKETS`
Issues tickets for each parking session.

| Column | Type | Constraints |
|---|---|---|
| `ticket_id` | SERIAL | PRIMARY KEY |
| `session_id` | INT | FOREIGN KEY → `PARKING_SESSIONS.session_id`, UNIQUE, NOT NULL |
| `ticket_number` | INT | UNIQUE |
| `issue_date` | DATETIME | NOT NULL |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 9. `PAYMENT`
Records payment transactions for each parking session.

| Column | Type | Constraints |
|---|---|---|
| `payment_id` | SERIAL | PRIMARY KEY |
| `session_id` | INT | FOREIGN KEY → `PARKING_SESSIONS.session_id` |
| `amount` | DECIMAL(10, 2) | |
| `payment_status` | ENUM | `'Pending'`, `'Paid'`, `'Failed'` |
| `payment_method` | ENUM | `'card'`, `'UPI'`, `'cash'` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

## 🔗 Entity Relationships

```
USERS (1) ──────────────────── (M) VEHICLES
VEHICLES_CATEGORIES (1) ──────── (M) VEHICLES
PARKING_ZONES_LEVEL (1) ─────── (M) PARKING_SPOTS
PARKING_SPOT_CATEGORIES (1) ──── (M) PARKING_SPOTS
VEHICLES (1) ───────────────── (M) PARKING_SESSIONS
PARKING_SPOTS (1) ──────────── (M) PARKING_SESSIONS
PARKING_SESSIONS (1) ─────────── (1) PARKING_TICKETS
PARKING_SESSIONS (1) ─────────── (M) PAYMENT
```

### Relationship Summary

| From Table | Relationship | To Table | Via Column |
|---|---|---|---|
| `USERS` | One-to-Many | `VEHICLES` | `user_id` |
| `VEHICLES_CATEGORIES` | One-to-Many | `VEHICLES` | `vehicle_category_id` |
| `PARKING_ZONES_LEVEL` | One-to-Many | `PARKING_SPOTS` | `zone_level_id` |
| `PARKING_SPOT_CATEGORIES` | One-to-Many | `PARKING_SPOTS` | `spot_category_id` |
| `VEHICLES` | One-to-Many | `PARKING_SESSIONS` | `vehicle_id` |
| `PARKING_SPOTS` | One-to-Many | `PARKING_SESSIONS` | `spot_id` |
| `PARKING_SESSIONS` | One-to-One | `PARKING_TICKETS` | `session_id` |
| `PARKING_SESSIONS` | One-to-Many | `PAYMENT` | `session_id` |

---

## 🔄 System Flow

```
1. User Registers
       ↓
2. User Registers Vehicle (linked to vehicle category)
       ↓
3. User Arrives → System finds available Parking Spot
   (filtered by zone, category, availability)
       ↓
4. Parking Session Created (start_time recorded)
       ↓
5. Parking Ticket Issued (linked 1:1 to session)
       ↓
6. Session Ends (end_time recorded, status → 'completed')
       ↓
7. Payment Processed (amount calculated from duration × price_per_hour)
```

---

## 🏗️ Key Design Decisions

- **Spot availability** is tracked in real-time via `is_available` boolean on `PARKING_SPOTS`.
- **Overdue sessions** are flagged with `session_status = 'overdue'` for enforcement.
- **Multiple payment attempts** are supported — `PAYMENT` has a many-to-one relationship with `PARKING_SESSIONS`.
- **License plates** are globally unique to prevent duplicate vehicle registrations.
- **Tickets** are 1:1 with sessions — each session generates exactly one ticket.
- **Zones A–D** allow for security-level differentiation (VIP, general, overflow, etc.).
- **Price-per-hour** is set at the `PARKING_SPOT_CATEGORIES` level, enabling dynamic pricing per vehicle type.

---

## 📊 ENUM Reference

| Table | Column | Values |
|---|---|---|
| `VEHICLES_CATEGORIES` | `category_name` | `bikes`, `cars`, `SUVs`, `cabs`, `EV Vehicles` |
| `PARKING_ZONES_LEVEL` | `zone_name` | `A`, `B`, `C`, `D` |
| `PARKING_SPOT_CATEGORIES` | `category_name` | `bikes`, `cars`, `cabs`, `EV Vehicles` |
| `PARKING_SESSIONS` | `session_status` | `active`, `completed`, `overdue` |
| `PAYMENT` | `payment_status` | `Pending`, `Paid`, `Failed` |
| `PAYMENT` | `payment_method` | `card`, `UPI`, `cash` |

---

## 🛠️ Tech Stack Recommendation

| Layer | Suggestion |
|---|---|
| Database | PostgreSQL (SERIAL, ENUM, DECIMAL support) |
| ORM | Prisma / Sequelize / SQLAlchemy |
| Backend | Node.js / FastAPI / Django |
| Auth | JWT-based (per `USERS` table) |

---

## 📝 Notes

- All tables include `created_at` and `updated_at` timestamps for audit purposes.
- The system supports **EV Vehicles** as a dedicated parking category with separate spot allocation.
- `PARKING_SESSIONS.session_id` is both `UNIQUE` and `NOT NULL` to ensure each session is trackable and deduplicated.
- Payment supports Indian digital payment method **UPI** alongside card and cash.