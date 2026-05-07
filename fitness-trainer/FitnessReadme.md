# Fitness Influencer Coaching Platform - ER Diagram

## Overview

This project represents the database design for an online fitness influencer coaching platform.  
The platform allows trainers/influencers to manage clients, create fitness plans, schedule sessions, track client progress, manage subscriptions, and handle payments.

This is not a gym management system.  
The focus is on online coaching, consultations, subscriptions, and fitness progress tracking.

---

# Features Supported

- Trainer and client management
- Multiple coaching plans/programs
- Client subscriptions to plans
- Session and consultation scheduling
- Weekly check-ins
- Progress tracking
- Payment management
- Multiple clients per plan
- Multiple plans per client over time

---

# Entities Included

## 1. Users
Stores common authentication and profile information for all users.

### Attributes
- user_id (PK)
- name
- email
- password
- role
- created_at
- updated_at

---

## 2. Trainers
Stores trainer-specific information.

### Attributes
- trainer_id (PK)
- user_id (FK)
- bio
- experience_years

---

## 3. Clients
Stores client-specific information.

### Attributes
- client_id (PK)
- user_id (FK)
- age
- height

---

## 4. Plans
Stores coaching plans/programs created by trainers.

### Attributes
- plan_id (PK)
- trainer_id (FK)
- name
- price
- duration_days

---

## 5. Subscriptions
Tracks which client purchased which plan.

### Attributes
- subscription_id (PK)
- client_id (FK)
- plan_id (FK)
- start_date
- end_date
- status

---

## 6. Sessions
Stores consultation or live training sessions.

### Attributes
- session_id (PK)
- trainer_id (FK)
- client_id (FK)
- session_date
- type

---

## 7. CheckIns
Stores weekly check-in reports submitted by clients.

### Attributes
- checkin_id (PK)
- client_id (FK)
- date
- weight
- notes

---

## 8. Progress
Stores body measurements and progress details.

### Attributes
- progress_id (PK)
- client_id (FK)
- date
- waist
- chest
- biceps

---

## 9. Payments
Stores subscription payment information.

### Attributes
- payment_id (PK)
- subscription_id (FK)
- amount
- payment_date
- status

---

# Relationships

- One User can be one Trainer
- One User can be one Client
- One Trainer can create many Plans
- One Client can purchase many Subscriptions
- One Plan can have many Subscriptions
- One Trainer can conduct many Sessions
- One Client can attend many Sessions
- One Client can submit many CheckIns
- One Client can have many Progress records
- One Subscription can contain multiple Payments

---

# ER Relationships

```txt
Users.user_id > Trainers.user_id
Users.user_id > Clients.user_id

Trainers.trainer_id > Plans.trainer_id

Clients.client_id > Subscriptions.client_id
Plans.plan_id > Subscriptions.plan_id

Trainers.trainer_id > Sessions.trainer_id
Clients.client_id > Sessions.client_id

Clients.client_id > CheckIns.client_id

Clients.client_id > Progress.client_id

Subscriptions.subscription_id > Payments.subscription_id


# Database Design Highlights
Proper normalization used
Progress tracking separated from user data
Sessions and check-ins modeled separately
Subscription table resolves many-to-many relationship between clients and plans
Practical and scalable structure for real-world coaching platforms