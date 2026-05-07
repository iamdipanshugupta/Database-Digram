# 🏥 Clinic Appointment and Diagnostics Platform

A comprehensive database-driven platform for managing clinic operations including patient appointments, doctor consultations, diagnostic tests, reports, and payments.

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Database Schema](#database-schema)
- [Entity Descriptions](#entity-descriptions)
- [Relationships](#relationships)
- [ERD Diagram Reference](#erd-diagram-reference)
- [Setup & Installation](#setup--installation)

---

## Project Overview

This platform provides a full-stack data model for a multi-doctor clinic. It handles:

- Patient registration and medical history tracking
- Doctor profiles and speciality management
- Appointment scheduling and status tracking
- Consultation records with diagnosis and prescription
- Diagnostic test results linked to consultations
- Report generation per consultation
- Payment processing with multiple payment methods

---

## Database Schema

### 1. `Clinic` (Red)

Stores clinic-level information and links to its doctors and patients.

| Column       | Type      | Constraint |
|--------------|-----------|------------|
| clinic_id    | serial    | PK         |
| name         | varchar   |            |
| address      | text      |            |
| tel_phone    | int       |            |
| doctor_id    | int       | FK → Doctors |
| patient_id   | int       | FK → Patients |
| created_at   | timestamp |            |
| updated_at   | timestamp |            |

---

### 2. `Doctors` (Blue)

Stores doctor profiles including their speciality and contact details.

| Column          | Type          | Constraint |
|-----------------|---------------|------------|
| doctor_id       | serial        | PK         |
| full_name       | varchar(100)  |            |
| contact_number  | varchar(15)   |            |
| licence_number  | int           |            |
| experience      | int           |            |
| speciality      | varchar       |            |
| created_at      | timestamp     |            |
| updated_at      | timestamp     |            |

---

### 3. `Patients` (Green)

Stores patient personal details and medical background.

| Column          | Type                              | Constraint    |
|-----------------|-----------------------------------|---------------|
| patient_id      | serial                            | PK            |
| first_name      | varchar(50)                       | NOT NULL      |
| last_name       | varchar(50)                       | NOT NULL      |
| phone_number    | varchar(15)                       | NOT NULL      |
| date_of_birth   | date                              |               |
| gender          | enum('male', 'female', 'other')   |               |
| medical_history | varchar                           |               |
| Blood_Type      | varchar                           |               |
| address         | text                              |               |
| created_at      | timestamp                         |               |
| updated_at      | timestamp                         |               |

---

### 4. `Appointments` (Purple)

Tracks appointment scheduling between patients and doctors.

| Column              | Type                                          | Constraint         |
|---------------------|-----------------------------------------------|--------------------|
| appointment_id      | serial                                        | PK                 |
| patient_id          | int                                           | FK → Patients      |
| doctor_id           | int                                           | FK → Doctors       |
| appointment_date    | date                                          |                    |
| appointment_time    | time                                          |                    |
| appointment_status  | enum('completed', 'canceled', 'scheduled')    |                    |
| reason_for_visit    | text                                          |                    |
| created_at          | timestamp                                     |                    |
| updated_at          | timestamp                                     |                    |

---

### 5. `Consultations` (Yellow)

Records consultation details including symptoms, diagnosis, and prescription.

| Column           | Type      | Constraint           |
|------------------|-----------|----------------------|
| consultation_id  | serial    | PK                   |
| appointment_id   | int       | FK → Appointments    |
| patient_id       | int       | FK → Patients        |
| doctor_id        | int       | FK → Doctors         |
| symptoms         | text      |                      |
| diagnosis        | text      |                      |
| prescription     | text      |                      |
| consultation_fee | numeric   |                      |
| created_at       | timestamp |                      |
| updated_at       | timestamp |                      |

---

### 6. `Diagnostic_Tests` (Green)

Stores results of diagnostic tests ordered during consultations.

| Column           | Type      | Constraint              |
|------------------|-----------|-------------------------|
| test_id          | serial    | PK                      |
| test_type        | varchar   |                         |
| diagnosis_result | varchar   |                         |
| prescription     | text      |                         |
| created_at       | timestamp |                         |
| updated_at       | timestamp |                         |

> **Note:** Linked to `Consultations` via `consultation_id < test_id` relationship.

---

### 7. `Reports` (Orange)

Stores medical reports generated per consultation.

| Column          | Type      | Constraint             |
|-----------------|-----------|------------------------|
| report_id       | serial    | PK                     |
| patient_id      | int       | FK → Patients          |
| doctor_id       | int       | FK → Doctors           |
| consultation_id | int       | FK → Consultations     |
| report_type     | varchar   |                        |
| created_at      | timestamp |                        |
| updated_at      | timestamp |                        |

---

### 8. `Payments` (Red)

Handles payment records for consultations.

| Column          | Type                                              | Constraint              |
|-----------------|---------------------------------------------------|-------------------------|
| payment_id      | serial                                            | PK                      |
| patient_id      | int                                               | FK → Patients           |
| consultation_id | int                                               | FK → Consultations      |
| total_amount    | numeric                                           |                         |
| payment_method  | enum('card', 'cash', 'upi')                       |                         |
| payment_status  | enum('pending', 'paid', 'failed', 'refunded')     | DEFAULT: 'pending'      |
| created_at      | timestamp                                         |                         |
| updated_at      | timestamp                                         |                         |

---

## Relationships

| From                        | Relation     | To                           |
|-----------------------------|--------------|------------------------------|
| Clinic.doctor_id            | many-to-one  | Doctors.doctor_id            |
| Clinic.patient_id           | many-to-one  | Patients.patient_id          |
| Doctors.doctor_id           | one-to-many  | Appointments.doctor_id       |
| Doctors.doctor_id           | one-to-many  | Consultations.doctor_id      |
| Doctors.doctor_id           | one-to-many  | Reports.doctor_id            |
| Patients.patient_id         | one-to-many  | Appointments.patient_id      |
| Patients.patient_id         | one-to-many  | Consultations.patient_id     |
| Appointments.appointment_id | one-to-one   | Consultations.appointment_id |
| Consultations.consultation_id | one-to-one | Payments.consultation_id     |
| Consultations.consultation_id | one-to-many | Diagnostic_Tests.test_id   |

---

## ERD Diagram Reference

This schema was designed using [DBML](https://dbml.org) / ERD format and visualized using **DbDiagram.io** (as seen in the Canvas view titled **" "** — Clinic Appointment and Diagnostics Platform).

The color coding used in the ERD:
- 🔴 **Red** — Clinic, Payments
- 🔵 **Blue** — Doctors
- 🟢 **Green** — Patients, Diagnostic_Tests
- 🟣 **Purple** — Appointments
- 🟡 **Yellow** — Consultations
- 🟠 **Orange** — Reports

---






---

## 👤 Author

Designed as part of a Clinic Appointment and Diagnostics Platform project.

---

## 📄 License

This project is for educational/demo purposes. Customize as needed for production use.