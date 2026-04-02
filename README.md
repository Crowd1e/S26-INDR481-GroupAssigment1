# HW2 — Relational & Dimensional Database Design
**Course:** Database Management Systems  
**University:** Koç University — Industrial Engineering

---

## Overview

This project involves designing and implementing two types of databases using Microsoft Access — a **relational database** and a **dimensional (data warehouse) database** — based on a university health center dataset tracking student prescriptions.

---

## Dataset

The source data (`HW_Data.xlsx`) contains four sheets:

| Sheet | Description | Rows |
|---|---|---|
| Students | Student demographics | 15 |
| Doctors | Doctor directory | 10 |
| Drugs | Drug catalog with cost & price | 30 |
| RX | Prescription records linking students, doctors, and drugs | 27 |

---

## Part 1 — Relational Database (`hw2_group_NO9_relational.accdb`)

### Tables & Schema

**Students**
- `Student ID` (PK, Long Integer)
- `Name`, `Phone`, `Date of Birth`, `Gender`

**Doctors**
- `DoctorId` (PK, Long Integer)
- `Name`, `Phone`

**Drugs**
- `UPN` (PK, Short Text)
- `Name`, `Description`, `Dosage`, `Cost`, `Price`

**RX**
- `Prescription ID` (PK, Long Integer)
- `UPN` (FK → Drugs), `Student ID` (FK → Students), `DoctorId` (FK → Doctors)
- `Quantity`, `Date`

### Relationships

All relationships are **1-to-Many**:
- `Students.Student ID` → `RX.Student ID`
- `Doctors.DoctorId` → `RX.DoctorId`
- `Drugs.UPN` → `RX.UPN`

### Queries

| Query | Description |
|---|---|
| Query 1 | Total and average drug cost grouped by student gender, sorted by total cost descending |
| Query 2 | Total cost and prescription count per student, sorted by total cost descending |
| Query 3 | Full prescription list with a calculated `Profit = (Price - Cost) * Quantity` field |
| Query 4 | Total profit, total cost, and drug count broken down by quarter for year 2007 (uses dimensional DB) |

---

## Part 2 — Dimensional Database (`hw2_group_NO9_dimensional.accdb`)

Transformed from the relational database into a **Star Schema** optimized for Profit & Loss analysis.

### Fact Table

**FactRX** — central table containing all measurable values:
- `Prescription_ID` (PK)
- `Student_ID`, `Doctor_ID`, `UPN`, `Date_ID` (Foreign Keys)
- `Quantity`, `Cost`, `Price`
- `Profit = (Price - Cost) * Quantity`
- `Revenue = Price * Quantity`

### Dimension Tables

**DimStudent** — `Student_ID` (PK), Name, Phone, DateOfBirth, Gender  
**DimDoctor** — `Doctor_ID` (PK), Name, Phone  
**DimDrug** — `UPN` (PK), Name, Description, Dosage  
**DimDate** — `Date_ID` (PK), FullDate, Day, Month, Quarter, Year  

### Star Schema Relationships

All relationships are **1-to-Many** from dimension to fact:
- `DimStudent.Student_ID` → `FactRX.Student_ID`
- `DimDoctor.Doctor_ID` → `FactRX.Doctor_ID`
- `DimDrug.UPN` → `FactRX.UPN`
- `DimDate.Date_ID` → `FactRX.Date_ID`

---

## Files

```
├── hw2_group_NO#_relational.accdb   # Relational database (Access)
├── hw2_group_NO#_dimensional.accdb  # Dimensional database (Access)
├── HW_Data.xlsx                     # Source data
├── Answersheet.docx                 # Completed answer sheet (Parts a–j)
└── README.md
```

---

## Notes

- `Date` field in the RX table was imported as `Short Text` due to Access type conversion issues. `CDate()` was used in queries to handle date operations.
- The `UPN` field in Drugs is stored as `Short Text` (not numeric) since it serves as an identifier only.
- Query 4 runs against the dimensional database, leveraging `DimDate.Quarter` and `DimDate.Year` for time-based analysis.
