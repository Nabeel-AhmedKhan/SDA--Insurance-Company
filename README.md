# 🚗 Auto Insurance Management System

A fully functional **console-based Insurance Management System** built in **C++11** as a university project. The system manages the complete lifecycle of automobile insurance — from customer registration and policy issuance to claim filing, inspection, and manager approval — with all data persisted using **flat files** (no DBMS).

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [File Structure](#file-structure)
- [Class Diagram](#class-diagram)
- [Use Case Diagram](#use-case-diagram)
- [Roles & Access](#roles--access)
- [How to Build & Run](#how-to-build--run)
- [Default Login](#default-login)
- [Data Persistence](#data-persistence)
- [Code Quality](#code-quality)
- [Tech Stack](#tech-stack)

---

## Overview

This system simulates the day-to-day operations of an automobile insurance company. It supports three types of users — **Manager**, **Salesman**, and **Surveyor** — each with role-based access to different parts of the system.

All data is stored in pipe-delimited `.dat` files inside a `data/` directory. There is **no database** — the file handling layer acts as the persistence engine.

---

## Features

### 👤 Customer Management
- Register new customers with full details (name, CNIC, phone, email, address)
- Search customer by ID or CNIC
- Update customer contact information
- View all registered customers
- Report: new customers acquired in any given month

### 🚘 Vehicle Management
- Register vehicles against a customer
- Supports Car, Bike, Truck with make, model, year, registration number, chassis and engine numbers
- View all vehicles owned by a customer

### 📄 Policy Management
- Issue COMPREHENSIVE, THIRD_PARTY, or FIRE_THEFT policies
- Link policy to a customer, vehicle, and salesman
- Set sum insured, annual premium, and validity dates
- Cancel active policies
- Prevent duplicate active policies on the same vehicle

### 📋 Claim Management
- File a claim against an active policy
- Assign a surveyor to inspect the damage
- Assign a registered workshop for repairs
- Full claim lifecycle: `PENDING → UNDER_INSPECTION → APPROVED/REJECTED → REPAIR_COMPLETE → CLOSED`

### 🔍 Inspection Management
- Surveyors submit detailed inspection reports per claim
- Report includes: damage description, estimated repair cost, findings, and recommendation
- Manager reviews inspection before making approval decision

### ✅ Manager Approvals & Reports
- Approve or reject claims after reviewing the inspection report
- Add manager remarks on each decision
- Generate reports:
  - New customers by month
  - Pending claims
  - Claims under inspection
  - All inspection reports
  - Customer claim history
  - All policies summary (with total premium)
  - All claims summary (approved, rejected, pending counts)

### 🏭 Workshop Management
- Register workshops with owner details and registration number
- Only active registered workshops can be assigned to claims
- Deactivate workshops when needed

### 👥 Staff Management
- Add salesmen and surveyors
- Create login accounts for staff at the time of registration
- Deactivate staff members
- View all staff by role

---

## System Architecture

The system follows a strict **3-layer architecture**:

```
┌─────────────────────────────────────────┐
│             UI Layer (Menus)            │
│  CustomerMenu  PolicyMenu  ClaimMenu    │
│  InspectionMenu  StaffMenu  ReportsMenu │
└────────────────────┬────────────────────┘
                     │ calls
┌────────────────────▼────────────────────┐
│          Business Layer (Services)      │
│  CustomerService   PolicyService        │
│  ClaimService      InspectionService    │
│  StaffService      WorkshopService      │
│  VehicleService    AuthService          │
└────────────────────┬────────────────────┘
                     │ calls
┌────────────────────▼────────────────────┐
│        Data Layer (Repositories)        │
│  CustomerRepository  ClaimRepository    │
│  PolicyRepository    VehicleRepository  │
│  InspectionRepository StaffRepository  │
│  WorkshopRepository  UserRepository    │
└────────────────────┬────────────────────┘
                     │ uses
┌────────────────────▼────────────────────┐
│           FileHandler (Utility)         │
│   readAll / writeAll / appendLine       │
│   split / join / getNextId             │
└────────────────────┬────────────────────┘
                     │ reads/writes
┌────────────────────▼────────────────────┐
│         data/ (Flat Files)              │
│  customers.dat   policies.dat           │
│  vehicles.dat    claims.dat             │
│  inspections.dat staff.dat             │
│  workshops.dat   users.dat             │
└─────────────────────────────────────────┘
```

> The UI layer **never** directly reads or writes files. All file access goes through the Repository layer via FileHandler.

---

## File Structure

```
AutoInsuranceSystem/
│
├── include/                        # All header files (entities, repos, services, UI)
│   ├── Entities
│   │   ├── Customer.h
│   │   ├── Vehicle.h
│   │   ├── Policy.h
│   │   ├── Claim.h
│   │   ├── Inspection.h
│   │   ├── Staff.h
│   │   ├── Workshop.h
│   │   └── User.h
│   │
│   ├── Repositories
│   │   ├── FileHandler.h
│   │   ├── CustomerRepository.h
│   │   ├── VehicleRepository.h
│   │   ├── PolicyRepository.h
│   │   ├── ClaimRepository.h
│   │   ├── InspectionRepository.h
│   │   ├── StaffRepository.h
│   │   ├── WorkshopRepository.h
│   │   └── UserRepository.h
│   │
│   ├── Services
│   │   ├── AuthService.h
│   │   ├── CustomerService.h
│   │   ├── VehicleService.h
│   │   ├── PolicyService.h
│   │   ├── ClaimService.h
│   │   ├── InspectionService.h
│   │   ├── StaffService.h
│   │   └── WorkshopService.h
│   │
│   ├── UI Menus
│   │   ├── CustomerMenu.h
│   │   ├── PolicyMenu.h
│   │   ├── ClaimMenu.h
│   │   ├── InspectionMenu.h
│   │   ├── StaffMenu.h
│   │   ├── WorkshopMenu.h
│   │   └── ReportsMenu.h
│   │
│   └── Helpers
│       ├── Display.h
│       ├── InputHelper.h
│       └── Utils.h
│
├── src/
│   ├── main.cpp                    # Entry point, role-based routing
│   └── Makefile
│
├── data/                           # Auto-created on first run
│   ├── customers.dat
│   ├── vehicles.dat
│   ├── policies.dat
│   ├── claims.dat
│   ├── inspections.dat
│   ├── staff.dat
│   ├── workshops.dat
│   └── users.dat
│
├── ClassDiagram.png
├── UseCaseDiagram.png
└── README.md
```

---

## Class Diagram

![Class Diagram](ClassDiagram.png)

The class diagram shows all 25 classes across 3 packages — Entities, Repositories, and Services — with their attributes, methods, and relationships (composition, aggregation, and dependency).

---

## Use Case Diagram

![Use Case Diagram](UseCaseDiagram.png)

The use case diagram shows 3 actors (Manager, Salesman, Surveyor) with 38 use cases across 7 functional areas.

---

## Roles & Access

| Feature | Manager | Salesman | Surveyor |
|---|:---:|:---:|:---:|
| Login / Logout | ✅ | ✅ | ✅ |
| Register Customer | ✅ | ✅ | ❌ |
| Register Vehicle | ✅ | ✅ | ❌ |
| Issue Policy | ✅ | ✅ | ❌ |
| Cancel Policy | ✅ | ❌ | ❌ |
| File Claim | ✅ | ✅ | ❌ |
| Assign Surveyor | ✅ | ✅ | ❌ |
| Assign Workshop | ✅ | ✅ | ❌ |
| Submit Inspection Report | ❌ | ❌ | ✅ |
| View Assigned Claims | ❌ | ❌ | ✅ |
| Approve / Reject Claim | ✅ | ❌ | ❌ |
| Add Staff / Workshops | ✅ | ❌ | ❌ |
| View All Reports | ✅ | ❌ | ❌ |

---

## How to Build & Run

### Prerequisites
- GCC with C++11 support (Linux/Mac) **or** MSVC / MinGW (Windows)

### Linux / Mac

```bash
cd src
g++ -std=c++11 -I../include -o insurance main.cpp
./insurance
```

### Windows (Command Prompt / MinGW)

```bash
cd src
g++ -std=c++11 -I../include -o insurance.exe main.cpp
insurance.exe
```

### Using Makefile (Linux/Mac)

```bash
cd src
make
./insurance
```

> The `data/` directory is created automatically on first run. No setup required.

---

## Default Login

On the very first run, the system automatically creates an admin account:

| Username | Password | Role |
|---|---|---|
| `admin` | `admin123` | Manager |

You can then add Salesman and Surveyor staff from the Staff Management menu and create their login accounts from within the system.

---

## Data Persistence

All data is stored as **pipe-delimited flat files** in the `data/` folder. No DBMS is used.

**Example — `customers.dat`:**
```
1|Bilal Chaudhry|35201-9876543-2|0300-1234567|bilal@gmail.com|Plot 12 DHA|2024-01-15
```

**Example — `claims.dat`:**
```
1|1|1|1|2024-06-15|Front bumper damaged|2024-06-16|150000.00|APPROVED|2|1|Claim verified|2024-06-20
```

Each file has a companion `_id.dat` counter file that tracks the last used ID for auto-increment. The `FileHandler` class handles all reading, writing, splitting, joining, and ID generation.

---

## Code Quality

Code quality was measured using **CCCC (C and C++ Code Counter) v3.2**.

| Metric | Value |
|---|---|
| Number of Modules (NOM) | 38 |
| Lines of Code (LOC) | 2,414 |
| LOC per Module (avg) | 63.5 |
| McCabe Complexity (MVG) | 679 |
| MVG per Module (avg) | 17.9 |

**Key observations:**
- **DIT = 0, NOC = 0** across all classes — flat design with no deep inheritance chains
- **Entity classes WMC ≤ 14** — clean separation of data and business logic
- **Repository CBO = 2–4** — minimal inter-layer coupling
- No class exceeds the WMC > 25 or CBO > 10 warning thresholds

---

## Tech Stack

| Component | Technology |
|---|---|
| Language | C++11 |
| Persistence | Flat files (.dat) via custom FileHandler |
| UI | Console / text-based menus |
| Build | g++ / Makefile |
| Code Quality | CCCC v3.2 |
| Diagrams | PlantUML |

---

## Author

**Nabeel** — BS Computer Science, FAST-NUCES Lahore
