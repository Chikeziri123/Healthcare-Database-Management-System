# Healthcare-Database-Management-System
**Title: Healthcare Database Management System -- A Comprehensive SQL Server Project**

**Abstract:**
This project presents a fully functional Healthcare Database Management System built using Microsoft SQL Server. The system is designed to manage core healthcare operations including patient records, medical histories, clinical diagnoses, treatment plans, billing, and appointment scheduling. The database employs normalised table structures, referential integrity through foreign key constraints, data validation via check constraints, and performance-optimised indexing. This README documents the project objectives, database architecture, table designs, sample data, and potential extensions.

**1. Introduction:**
Healthcare organisations handle large volumes of sensitive patient data daily, from demographics and medical histories to diagnoses, treatments, and financial records. Efficient, secure, and well-structured data management is essential for delivering quality patient care, supporting clinical decision-making, and maintaining regulatory compliance. The HealthCareDB project was developed to demonstrate a relational database solution that addresses these core data management requirements using SQL Server.

**2. Project Objectives:**

- Design a normalised relational database that eliminates data redundancy and ensures data integrity across all healthcare entities.
- Implement referential integrity using primary and foreign key relationships to maintain consistency between patients, diagnoses, treatments, billing, and appointments.
- Apply data validation rules through check constraints to enforce business logic at the database level.
- Optimise query performance through strategic indexing on frequently queried columns.
- Provide realistic sample data to demonstrate the system's functionality and support testing.

**3. Database Architecture and Design:**

**3.1 Entity-Relationship Overview:**
The database comprises six interrelated tables, with `PatientInformation` serving as the central entity. All other tables reference `PatientInformation` via foreign keys, establishing a hub-and-spoke relationship model. Additionally, the `Treatments` table references the `Diagnoses` table, linking each treatment to its corresponding diagnosis.

**3.2 Normalisation:**
The database follows Third Normal Form (3NF) principles. Each table stores data specific to a single entity, eliminating redundancy and transitive dependencies. Patient demographics, medical histories, clinical diagnoses, treatments, billing records, and appointments are each maintained in dedicated tables.

**3.3 Indexing Strategy:**
Non-clustered indexes are created on columns frequently used in WHERE clauses, JOIN operations, and ORDER BY statements to accelerate query execution:
- `LastName` on `PatientInformation`
- `DateOfDiagnosis` on `Diagnoses`
- `TreatmentDate` on `Treatments`
- `DateOfService` on `BillingInformation`
- `AppointmentDateTime` on `AppointmentScheduling`

**4. Table Structures:**

**4.1 PatientInformation:**
Stores core patient demographics including name, date of birth, gender, and contact details. A check constraint restricts the `Gender` column to accepted values ('Male', 'Female', 'Other').

| Column | Data Type | Constraint |
|---|---|---|
| PatientID | INT | PRIMARY KEY |
| FirstName | NVARCHAR(50) | NOT NULL |
| LastName | NVARCHAR(50) | NOT NULL |
| DateOfBirth | DATE | |
| Gender | NVARCHAR(10) | CHECK ('Male', 'Female', 'Other') |
| ContactInformation | NVARCHAR(100) | |

**4.2 MedicalHistory:**
Captures each patient's allergy information, past illnesses, surgical history, and family medical history. Linked to `PatientInformation` via `PatientID`.

| Column | Data Type | Constraint |
|---|---|---|
| MedicalHistoryID | INT | PRIMARY KEY |
| PatientID | INT | FOREIGN KEY |
| Allergies | NVARCHAR(255) | |
| PastIllnesses | NVARCHAR(255) | |
| Surgeries | NVARCHAR(255) | |
| FamilyMedicalHistory | NVARCHAR(255) | |

**4.3 Diagnoses:**
Records clinical diagnoses with the date, description, and treating physician. Linked to `PatientInformation` via `PatientID`.

| Column | Data Type | Constraint |
|---|---|---|
| DiagnosisID | INT | PRIMARY KEY |
| PatientID | INT | FOREIGN KEY |
| DateOfDiagnosis | DATE | |
| DiagnosisDescription | NVARCHAR(255) | |
| TreatingPhysician | NVARCHAR(100) | |

**4.4 Treatments:**
Documents treatment plans including medications prescribed and procedures performed. References both `PatientInformation` (via `PatientID`) and `Diagnoses` (via `DiagnosisID`), establishing a direct link between a treatment and its triggering diagnosis.

| Column | Data Type | Constraint |
|---|---|---|
| TreatmentID | INT | PRIMARY KEY |
| PatientID | INT | FOREIGN KEY |
| DiagnosisID | INT | FOREIGN KEY |
| TreatmentDate | DATE | |
| MedicationsPrescribed | NVARCHAR(255) | |
| ProceduresPerformed | NVARCHAR(255) | |

**4.5 BillingInformation:**
Manages financial records including procedure codes, insurance details, and billed amounts. A check constraint ensures `AmountBilled` cannot be negative. Linked to `PatientInformation` via `PatientID`.

| Column | Data Type | Constraint |
|---|---|---|
| BillID | INT | PRIMARY KEY |
| PatientID | INT | FOREIGN KEY |
| DateOfService | DATE | |
| ProcedureCodes | NVARCHAR(50) | |
| InsuranceInformation | NVARCHAR(255) | |
| AmountBilled | DECIMAL(10,2) | CHECK (>= 0) |

**4.6 AppointmentScheduling:**
Handles appointment booking with date/time, assigned physician, and appointment type. A check constraint restricts `AppointmentType` to 'Routine Check-up' or 'Follow-up'. Linked to `PatientInformation` via `PatientID`.

| Column | Data Type | Constraint |
|---|---|---|
| AppointmentID | INT | PRIMARY KEY |
| PatientID | INT | FOREIGN KEY |
| AppointmentDateTime | DATETIME | |
| AssignedPhysician | NVARCHAR(100) | |
| AppointmentType | NVARCHAR(50) | CHECK ('Routine Check-up', 'Follow-up') |

**5. Sample Data:**
The database is populated with 10 sample patient records spanning diverse demographics, medical conditions, and treatment plans. Sample data covers a range of scenarios including allergy management, chronic condition treatment, surgical histories, and varied insurance providers, enabling comprehensive testing of queries, joins, and aggregations.

**6. Key SQL Features Demonstrated:**

- **Database and Table Creation:** Structured DDL statements for building the complete schema from scratch.
- **Primary and Foreign Keys:** Enforced entity identification and referential integrity across all six tables.
- **Check Constraints:** Business rule enforcement at the database level for gender values, appointment types, and billing amounts.
- **Non-Clustered Indexes:** Performance optimisation on high-traffic columns used in filtering, sorting, and joining.
- **Data Insertion:** Bulk INSERT statements to populate all tables with realistic healthcare data.
- **NVARCHAR Data Types:** Unicode support for international character compatibility across patient records.

**7. How to Use:**

**7.1 Prerequisites:**
- Microsoft SQL Server (2016 or later recommended) or SQL Server Express.
- SQL Server Management Studio (SSMS) or Azure Data Studio.

**7.2 Setup Instructions:**
1. Open SQL Server Management Studio and connect to your SQL Server instance.
2. Open the `HealthCareDB.sql` script file.
3. Execute the full script to create the database, tables, indexes, and sample data.
4. Verify successful creation by expanding the HealthCareDB database in Object Explorer and reviewing all six tables.

**7.3 Running Queries:**
Once set up, you can run queries against the database to explore the data. Examples:

```sql
-- Retrieve all patients with their diagnoses
SELECT p.FirstName, p.LastName, d.DiagnosisDescription, d.DateOfDiagnosis
FROM PatientInformation p
JOIN Diagnoses d ON p.PatientID = d.PatientID;

-- View total billing per patient
SELECT p.FirstName, p.LastName, SUM(b.AmountBilled) AS TotalBilled
FROM PatientInformation p
JOIN BillingInformation b ON p.PatientID = b.PatientID
GROUP BY p.FirstName, p.LastName;

-- List upcoming appointments with patient details
SELECT p.FirstName, p.LastName, a.AppointmentDateTime, a.AssignedPhysician, a.AppointmentType
FROM PatientInformation p
JOIN AppointmentScheduling a ON p.PatientID = a.PatientID
ORDER BY a.AppointmentDateTime;
```

**8. Potential Extensions and Future Enhancements:**

- **Stored Procedures:** Develop procedures for common operations such as registering new patients, scheduling appointments, and generating billing summaries.
- **Views:** Create views for frequently accessed data combinations, such as patient-diagnosis-treatment summaries.
- **Triggers:** Implement triggers for audit logging on sensitive tables like `PatientInformation` and `BillingInformation`.
- **Security:** Apply role-based access control and Transparent Data Encryption (TDE) for data protection and regulatory compliance.
- **ETL Integration:** Build SSIS packages to automate data ingestion from external sources such as EHR and laboratory systems.
- **Analytics and Reporting:** Integrate with Power BI or SSRS to create dashboards for patient demographics, treatment outcomes, and financial performance.
- **Regulatory Compliance:** Incorporate HIPAA-aligned security measures, audit trails, and data masking for protected health information.

**9. Technologies Used:**

- Microsoft SQL Server
- SQL Server Management Studio (SSMS)
- T-SQL (Transact-SQL)

**10. Author:**
Chikeziri Nnodum
Data Analyst | Data Engineer | Business Analyst

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/chikeziri-nnodum/) 
