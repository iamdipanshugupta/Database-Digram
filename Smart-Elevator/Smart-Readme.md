# 🛗 Smart Elevator Control System — Database Design

A relational database schema for a Smart Elevator Control System. This system manages buildings, elevator shafts, elevators, floor requests, ride assignments, ride logs, real-time status tracking, and maintenance scheduling.

---

## 📁 Database Schema Overview

The system consists of **11 core tables** organized across 3 domains:
- 🏢 **Infrastructure** — Buildings, Floors, Elevator Shafts, Elevators
- 🚀 **Operations** — Floor Requests, Ride Assignments, Ride Logs, Elevator Floor Map
- 🔧 **Maintenance** — Elevator Status Tracking, Maintenance Schedules, Maintenance Logs

---

## 🗂️ Tables & Schema

### 1. `Buildings`
Stores all registered buildings in the system.

| Column | Type | Constraints |
|---|---|---|
| `building_id` | SERIAL | PRIMARY KEY |
| `name` | VARCHAR(100) | NOT NULL |
| `location` | TEXT | |
| `total_floors` | INT | NOT NULL |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 2. `Floors`
Represents individual floors within a building.

| Column | Type | Constraints |
|---|---|---|
| `floor_id` | SERIAL | PRIMARY KEY |
| `building_id` | INT | FOREIGN KEY → `Buildings.building_id` |
| `floor_number` | INT | NOT NULL |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 3. `Elevator_Shaft`
Defines the physical shaft in a building within which elevators operate.

| Column | Type | Constraints |
|---|---|---|
| `shaft_id` | SERIAL | PRIMARY KEY |
| `building_id` | INT | FOREIGN KEY → `Buildings.building_id` |
| `start_floor` | INT | NOT NULL |
| `end_floor` | INT | NOT NULL |
| `shaft_label` | VARCHAR(50) | |
| `is_active` | BOOLEAN | DEFAULT `true` |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 4. `Elevators`
Represents individual elevator units within a shaft.

| Column | Type | Constraints |
|---|---|---|
| `elevator_id` | SERIAL | PRIMARY KEY |
| `shaft_id` | INT | FOREIGN KEY → `Elevator_Shaft.shaft_id` |
| `model_number` | VARCHAR(50) | |
| `capacity` | INT | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 5. `Elevator_Floor_Map`
Junction table mapping which floors each elevator can serve.

| Column | Type | Constraints |
|---|---|---|
| `id` | SERIAL | PRIMARY KEY |
| `elevator_id` | INT | FOREIGN KEY → `Elevators.elevator_id` |
| `floor_id` | INT | FOREIGN KEY → `Floors.floor_id` |

---

### 6. `floor_request`
Captures all elevator call requests from passengers.

| Column | Type | Constraints |
|---|---|---|
| `request_id` | SERIAL | PRIMARY KEY |
| `floor_id` | INT | FOREIGN KEY → `Floors.floor_id` |
| `direction` | ENUM | `'up'`, `'down'` |
| `request_type` | ENUM | `'internal'`, `'external'` |
| `priority` | ENUM | `'normal'`, `'vip'`, `'emergency'` DEFAULT `'normal'` |
| `request_status` | ENUM | `'pending'`, `'assigned'`, `'fulfilled'`, `'cancelled'` |
| `created_at` | TIMESTAMP | |
| `assigned_at` | TIMESTAMP | |
| `completed_at` | TIMESTAMP | |

---

### 7. `Ride_Assignments`
Links a floor request to the elevator assigned to fulfill it.

| Column | Type | Constraints |
|---|---|---|
| `assignment_id` | SERIAL | PRIMARY KEY |
| `request_id` | INT | FOREIGN KEY → `floor_request.request_id` |
| `elevator_id` | INT | FOREIGN KEY → `Elevators.elevator_id` |
| `assignment_status` | ENUM | `'active'`, `'completed'`, `'reassigned'`, `'failed'` |
| `estimated_arrival` | INT | (seconds) |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 8. `Ride_Logs`
Records the complete log of every ride taken.

| Column | Type | Constraints |
|---|---|---|
| `ride_log_id` | SERIAL | PRIMARY KEY |
| `request_id` | INT | FOREIGN KEY → `floor_request.request_id` |
| `elevator_id` | INT | FOREIGN KEY → `Elevators.elevator_id` |
| `start_floor` | INT | |
| `end_floor` | INT | |
| `passenger_count` | INT | |
| `started_at` | TIMESTAMP | |
| `completed_at` | TIMESTAMP | |
| `created_at` | TIMESTAMP | |

---

### 9. `Elevator_Status_Tracking`
Tracks real-time position and operational status of each elevator.

| Column | Type | Constraints |
|---|---|---|
| `status_id` | SERIAL | PRIMARY KEY |
| `elevator_id` | INT | FOREIGN KEY → `Elevators.elevator_id` |
| `current_floor` | INT | |
| `status` | ENUM | `'idle'`, `'moving'`, `'stopped'`, `'maintenance'` |
| `created_at` | TIMESTAMP | |

---

### 10. `Maintenance_Schedules`
Defines when and how maintenance for each elevator is triggered.

| Column | Type | Constraints |
|---|---|---|
| `schedule_id` | SERIAL | PRIMARY KEY |
| `elevator_id` | INT | FOREIGN KEY → `Elevators.elevator_id` |
| `trigger_type` | ENUM | `'time_based'`, `'usage_based'`, `'sensor_threshold'` |
| `threshold_value` | INT | |
| `last_maintenance_at` | TIMESTAMP | |
| `next_due_at` | TIMESTAMP | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |

---

### 11. `Maintenance_Logs`
Records details of each maintenance event performed.

| Column | Type | Constraints |
|---|---|---|
| `maintenance_log_id` | SERIAL | PRIMARY KEY |
| `schedule_id` | INT | FOREIGN KEY → `Maintenance_Schedules.schedule_id` |
| `issue_category` | ENUM | `'motor'`, `'cables'`, `'doors'`, `'software'`, `'sensors'` |
| `action_taken` | TEXT | |
| `downtime_duration` | INTERVAL | |
| `created_at` | TIMESTAMP | |

---

## 🔗 Entity Relationships

```
Buildings (1) ──────────── (M) Floors
Buildings (1) ──────────── (M) Elevator_Shaft
Elevator_Shaft (1) ──────── (M) Elevators
Elevators (M) ──────────── (M) Floors          [via Elevator_Floor_Map]
Floors (1) ─────────────── (M) floor_request
floor_request (1) ──────── (M) Ride_Assignments
Elevators (1) ──────────── (M) Ride_Assignments
floor_request (1) ──────── (M) Ride_Logs
Elevators (1) ──────────── (M) Ride_Logs
Elevators (1) ──────────── (M) Elevator_Status_Tracking
Elevators (1) ──────────── (M) Maintenance_Schedules
Maintenance_Schedules (1) ─ (M) Maintenance_Logs
```

### Relationship Summary

| From | Relation | To | Via |
|---|---|---|---|
| `Buildings` | One-to-Many | `Floors` | `building_id` |
| `Buildings` | One-to-Many | `Elevator_Shaft` | `building_id` |
| `Elevator_Shaft` | One-to-Many | `Elevators` | `shaft_id` |
| `Elevators` | Many-to-Many | `Floors` | `Elevator_Floor_Map` |
| `Floors` | One-to-Many | `floor_request` | `floor_id` |
| `floor_request` | One-to-Many | `Ride_Assignments` | `request_id` |
| `Elevators` | One-to-Many | `Ride_Assignments` | `elevator_id` |
| `floor_request` | One-to-Many | `Ride_Logs` | `request_id` |
| `Elevators` | One-to-Many | `Ride_Logs` | `elevator_id` |
| `Elevators` | One-to-Many | `Elevator_Status_Tracking` | `elevator_id` |
| `Elevators` | One-to-Many | `Maintenance_Schedules` | `elevator_id` |
| `Maintenance_Schedules` | One-to-Many | `Maintenance_Logs` | `schedule_id` |

---

## 🔄 System Flow

```
1. Building & Floors Registered
          ↓
2. Elevator Shaft defined per Building
          ↓
3. Elevator assigned to Shaft
   + Floor access mapped (Elevator_Floor_Map)
          ↓
4. Passenger presses button → floor_request created
   (direction: up/down | type: internal/external | priority: normal/vip/emergency)
          ↓
5. System assigns nearest available Elevator → Ride_Assignment created
          ↓
6. Elevator moves → Elevator_Status_Tracking updated in real-time
          ↓
7. Ride completed → Ride_Log recorded
   (start_floor, end_floor, passenger_count, duration)
          ↓
8. Maintenance triggered (time/usage/sensor) → Maintenance_Schedule fires
          ↓
9. Technician performs fix → Maintenance_Log recorded
   (issue_category, action_taken, downtime_duration)
```

---

## 📊 ENUM Reference

| Table | Column | Values |
|---|---|---|
| `floor_request` | `direction` | `up`, `down` |
| `floor_request` | `request_type` | `internal`, `external` |
| `floor_request` | `priority` | `normal`, `vip`, `emergency` |
| `floor_request` | `request_status` | `pending`, `assigned`, `fulfilled`, `cancelled` |
| `Ride_Assignments` | `assignment_status` | `active`, `completed`, `reassigned`, `failed` |
| `Elevator_Status_Tracking` | `status` | `idle`, `moving`, `stopped`, `maintenance` |
| `Maintenance_Schedules` | `trigger_type` | `time_based`, `usage_based`, `sensor_threshold` |
| `Maintenance_Logs` | `issue_category` | `motor`, `cables`, `doors`, `software`, `sensors` |

---

## 🏗️ Key Design Decisions

- **Elevator_Floor_Map (M:M junction)** — An elevator doesn't necessarily serve every floor in a building (e.g., service elevators, express lifts). This junction table captures exact floor access per elevator.
- **Priority-based requests** — `floor_request.priority` supports emergency and VIP routing, allowing the control system to preempt normal rides.
- **Shaft abstraction** — `Elevator_Shaft` decouples the physical shaft from the elevator unit, allowing multiple elevators per shaft (double-decker systems) or shaft replacement/upgrades.
- **Reassignment support** — `Ride_Assignments.assignment_status = 'reassigned'` handles elevator breakdowns mid-assignment without data loss.
- **Maintenance triggers** — Three trigger types (`time_based`, `usage_based`, `sensor_threshold`) allow predictive and preventive maintenance strategies.
- **Downtime tracking** — `Maintenance_Logs.downtime_duration` (INTERVAL type) enables SLA tracking and uptime analytics.
- **Status snapshots** — `Elevator_Status_Tracking` stores append-only status rows (not updates), enabling full historical tracking of elevator movement.

---

## 🛠️ Tech Stack Recommendation

| Layer | Suggestion |
|---|---|
| Database | PostgreSQL (ENUM, INTERVAL, SERIAL support) |
