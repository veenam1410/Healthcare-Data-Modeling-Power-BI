# Healthcare Data Modeling | Power BI

## 📌 Project Overview

This project focuses on building a robust and scalable **data model in Power BI** from raw healthcare operational datasets.

The primary objective of this project is **data modeling**, rather than dashboard development or advanced DAX analytics.

The project demonstrates how raw, multi-process healthcare data can be transformed into a clean, validated, star-schema-oriented analytical model using Power Query and Power BI.

The project covers:

- Understanding business processes
- Identifying table grain
- Identifying fact and dimension tables
- Power Query transformations
- Append and Merge operations
- Handling different data grains
- Designing relationships
- Building conformed dimensions
- Avoiding incorrect fact-to-fact relationships
- Avoiding unnecessary dimension-to-dimension relationships
- Data model validation
- Referential integrity validation
- Date and month modeling
- Row-Level Security as an additional practice exercise

## 🎯 Project Objectives

The main objectives of this project were to:

1. Understand the grain of every source table.
2. Identify appropriate fact and dimension tables.
3. Build a star-schema-oriented data model.
4. Handle multiple healthcare business processes independently.
5. Preserve table grain while performing Power Query transformations.
6. Build reusable and conformed dimensions.
7. Handle both daily and monthly-grain data.
8. Design appropriate one-to-many relationships.
9. Validate keys and relationships.
10. Practice Row-Level Security in Power BI.

## 🏥 Business Domain

The dataset represents a healthcare organization with multiple operational processes:

- Patient management
- Appointments
- Clinical encounters
- Diagnoses
- Laboratory orders
- Laboratory results
- Procedures
- Prescriptions
- Medications
- Hospital admissions
- Bed movements
- Billing
- Payments
- Insurance claims
- Marketing campaigns
- Facility capacity
- Department targets
- Currency exchange rates

Because each business process operates at a different grain, the data was modeled using multiple fact tables connected through shared dimensions.

## 🧠 Modeling Approach

The project followed a **grain-first approach**.

Before deciding whether a table should be a fact, dimension, merge, append, or relationship, the following question was asked:

> What does one row represent?

Examples:

| Table | Grain |
|---|---|
| `dim_patients` | 1 row = 1 patient |
| `dim_doctors` | 1 row = 1 doctor |
| `dim_facilities` | 1 row = 1 facility |
| `dim_departments` | 1 row = 1 department |
| `dim_diagnoses` | 1 row = 1 diagnosis |
| `dim_lab_tests` | 1 row = 1 laboratory test |
| `dim_medications` | 1 row = 1 medication |
| `dim_procedures_master` | 1 row = 1 procedure |
| `dim_insurance_plans` | 1 row = 1 insurance plan |
| `dim_campaigns` | 1 row = 1 campaign |
| `dim_beds` | 1 row = 1 bed |
| `dim_date` | 1 row = 1 date |
| `dim_month` | 1 row = 1 month |
| `fact_appointments` | 1 row = 1 appointment |
| `fact_encounters` | 1 row = 1 encounter |
| `fact_diagnoses` | 1 row = 1 diagnosis recorded for an encounter |
| `fact_lab_orders` | 1 row = 1 lab order |
| `fact_lab_results` | 1 row = 1 laboratory test result |
| `fact_procedure_events` | 1 row = 1 procedure event |
| `fact_prescriptions` | 1 row = 1 prescription |
| `fact_prescription_items` | 1 row = 1 medication item within a prescription |
| `fact_admissions` | 1 row = 1 hospital admission |
| `fact_bed_moves` | 1 row = 1 bed movement |
| `fact_invoices` | 1 row = 1 invoice |
| `fact_invoice_lines` | 1 row = 1 invoice line |
| `fact_insurance_claims` | 1 row = 1 insurance claim |
| `fact_campaign_log` | 1 row = 1 campaign per day |
| `fact_capacity_monthly` | 1 row = 1 facility per month |
| `fact_department_targets` | 1 row = 1 department per month |
| `fact_exchange_rates` | 1 row = 1 currency per month |

Understanding the grain was the foundation for all subsequent modeling decisions.

## 🏗️ Model Architecture

The final model follows a **star-schema-oriented architecture**.

### Dimensions

- `dim_patients`
- `dim_doctors`
- `dim_facilities`
- `dim_departments`
- `dim_diagnoses`
- `dim_lab_tests`
- `dim_medications`
- `dim_procedures_master`
- `dim_insurance_plans`
- `dim_campaigns`
- `dim_beds`
- `dim_date`
- `dim_month`

### Fact Tables

- `fact_appointments`
- `fact_encounters`
- `fact_diagnoses`
- `fact_lab_orders`
- `fact_lab_results`
- `fact_procedure_events`
- `fact_prescriptions`
- `fact_prescription_items`
- `fact_admissions`
- `fact_bed_moves`
- `fact_invoices`
- `fact_invoice_lines`
- `fact_insurance_claims`
- `fact_campaign_log`
- `fact_capacity_monthly`
- `fact_department_targets`
- `fact_exchange_rates`

### Supporting Tables

- `patient_insurance`
- `patient_contacts`
- `security_users`

## 🔄 Power Query Transformations

Power Query was used to transform the raw source data into the analytical model.

### Append Operations

Year-specific appointment datasets were appended because they represented the same business process and the same target grain.

```text
appointments_2025
        +
appointments_2026
        ↓
fact_appointments
```
### Merge Operations

Merges were used when additional attributes could be brought into a table without changing its intended grain.

```text
appointments + appointment_details
admissions + discharges
invoices + payments
fact_diagnoses + encounter attributes
fact_lab_orders + encounter attributes
fact_lab_results + lab order attributes
fact_procedure_events + encounter attributes
fact_prescription_items + prescription attributes
fact_bed_moves + admission attributes
```
### 🔗 Relationship Design

Most relationships use:

```text
Cardinality: 1:*
Cross-filter direction: Single
```
### 🚫 Fact-to-Fact Relationships

Direct relationships between fact tables were intentionally avoided.

```text
fact_prescriptions
        ↓
fact_prescription_items
```
relevant attributes such as: patient_id, doctor_id, prescription_date were brought into fact_prescription_items through Power Query.

Both tables can then independently connect to shared dimensions. This avoids ambiguous filter paths and maintains a cleaner star-schema structure.
### 🚫 Unnecessary Dimension-to-Dimension Relationships

Relationships such as:

```text
dim_departments → dim_doctors
dim_specialties → dim_doctors
dim_facilities → dim_doctors
```
were avoided.

Instead, relevant descriptive attributes were incorporated into dim_doctors.
### 📅 Date Modeling

The dataset contains both daily and monthly-grain data.
### 📊 Different Fact Grains

One of the main modeling challenges was handling multiple levels of granularity.

Transaction / Event Facts
```text
fact_appointments
fact_encounters
fact_diagnoses
fact_lab_orders
fact_lab_results
fact_procedure_events
fact_prescriptions
fact_admissions
fact_invoices
fact_insurance_claims
```
Child / Line-Level Facts
```text
fact_prescription_items
fact_invoice_lines
fact_bed_moves
```
Daily Facts
```text
fact_campaign_log
```
Monthly Facts
```text
fact_capacity_monthly
fact_department_targets
fact_exchange_rates
```
### Data Model Validation

After building the model, several validation tests were performed.

1. Grain Validation

Business keys and composite keys were checked to ensure that Power Query transformations had not changed the intended grain.
```text
appointment_id
encounter_id
procedure_event_id
prescription_id + medication_code
invoice_id + line_number
admission_id + move_sequence
lab_order_id + test_code
```
The row counts matched the corresponding distinct business-key counts for the tested tables.

2. Referential Integrity Validation

Foreign keys were checked against their corresponding dimension tables.
```text
fact_appointments → dim_patients
fact_appointments → dim_doctors
fact_appointments → dim_facilities
fact_diagnoses → dim_diagnoses
fact_lab_results → dim_lab_tests
fact_procedure_events → dim_procedures_master
fact_prescription_items → dim_medications
fact_insurance_claims → dim_insurance_plans
```
All tested orphan-key checks returned: 0

This confirmed that the tested fact-table foreign keys had matching dimension records.

3. Filter Propagation Validation

The model was tested using Power BI slicers.

Filters were successfully propagated using:
```text
Year
Department
Specialty
```
The filters correctly affected the relevant fact tables.

This confirmed that the relationship structure was functioning as intended.
### 🛠️ Tools Used
```text
Power BI Desktop
Power Query
DAX
GitHub
```
DAX was used primarily for model validation.
### Repository Structure
```text
Healthcare-Data-Modeling-Power-BI/
│
├── README.md
│
├── data/
│       └── healthcare source CSV files
│
├── powerbi/
│   └── healthcare_data_model.pbix
│
└── screenshots/
    └── healthcare_data_model.png
```
### 👩‍💻 Author

[Veena M](https://www.linkedin.com/in/veenam1410/)
