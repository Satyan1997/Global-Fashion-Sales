# 🌐 Enterprise ETL Pipeline (SSIS) 🚀

Welcome to the orchestration of robust data engineering! This project is a modular, production-ready SSIS ETL solution crafted to automate bulk data loading, enforce advanced error handling, deliver dynamic auditing, and empower granular business insights. 

## Table of Contents

- [Project Overview](#project-overview)  
- [Architectural Flow](#architectural-flow)  
- [Key Features](#key-features)  
- [Module Breakdown](#module-breakdown)  
- [Error Management](#error-management)  
- [Installation & Usage](#installation--usage)  
- [Data Sources & Destinations](#data-sources--destinations)  
- [Customization](#customization)  
- [Troubleshooting](#troubleshooting)  
- [Credits](#credits)

---

## Project Overview

This SSIS project addresses the common challenges faced in enterprise data processing by providing an automated ETL pipeline that loads data from flat files into a structured data warehouse. The pipeline is designed to handle transforming diverse raw data formats, maintain slowly changing dimensions for historical accuracy, and provide detailed error and audit logging to improve reliability and transparency in data workflows.

It incorporates best practices like modular task separation, dynamic parameterization, and real-time alerting to support scalable and maintainable ETL operations.

---

## 🏗️ Architectural Flow

**Control Flow Explanation:**  
The control flow orchestrates the entire ETL process, starting from file validation to final notifications. It includes looping containers to iterate through multiple data files, scripts to parameterize workflows dynamically, and precedence constraints which use expressions and logical operators (AND/OR) to determine the execution path based on runtime conditions.

Various control tasks update status logs before and after executing the main data flow, ensuring that each phase is auditable and errors are captured promptly.

**Data Flow Explanation:**  
The data flow phases read raw data from flat files, then perform a series of transformations:

- **Data Conversion:** Converts data types to match database schema requirements, handling common type mismatches.  
- **Derived Column:** Creates new calculated columns or flags based on business rules.  
- **Conditional Split:** Segregates valid and invalid records to streamline processing and error management.  
- **Slowly Changing Dimension (SCD):** Updates dimension tables by tracking new records, changes, and preserving history for analytical querying.  
- **Lookups:** Validate foreign keys by ensuring referenced records exist in related tables, routing unmatched records to separate error handling flows.  
- **Row Count and Auditing:** Counts processed records for logging and monitoring.  
- **OLE DB Destination:** Loads cleaned and transformed data into the target database.

The error handling flow writes out invalid data records to a separate location, allowing for downstream review without interrupting the primary ETL pipeline.

---

## ⚡ Key Features

- **Modular Control Flows:** Use of Foreach Loops and Script Tasks allows processing multiple files or entities dynamically, reducing manual configuration and increasing automation.  
- **Comprehensive Error Handling:** Automatic capturing of error details triggers failure emails, updates error logs in the database, and moves problematic files to designated folders for investigation.  
- **Audit Trail & Logging:** Every load is tracked with time stamps, row counts, and execution status stored in a logging table for traceability and SLA reporting.  
- **Slowly Changing Dimension Support:** Implements type 2 SCD logic to incrementally update dimension tables without losing historical record integrity.  
- **Flexible Execution Rules:** Use of precedence constraints with expressions enables conditional execution pathways, supporting complex business logic without hardcoding.  
- **Parameterization:** Enables reusability of packages by allowing dynamic configuration of file locations, database connections, and control flags.

---

## 🚦 Module Breakdown

| Module                | Purpose                                   | Components                                      |
|-----------------------|-------------------------------------------|-------------------------------------------------|
| **Control Flow**      | Manages task orchestration and sequencing | Foreach Loop Container, Precedence Constraints, Script Tasks, Email Notifications |
| **Data Flow Tasks**   | Performs the bulk ETL work: extraction, transformation, load | Flat File Source, Data Conversion, Derived Column, Conditional Split, Lookup, Slowly Changing Dimension, OLE DB Destination |
| **Event Handlers**    | Manage reactive flows on errors            | Capture Error Description, Send Failure Email, Update Log Table, Move File to Error Folder |
| **Precedence Constraints** | Condition-based workflow control     | Expression evaluation with AND/OR logical operators for precise execution control |

---

## 🛡️ Error Management

The package includes a dedicated error handling mechanism that triggers on any failure during data flow execution:

1. **CaptureErrorDescription:** Retrieves detailed error messages for debugging.  
2. **SendFailureEmail:** Notifies administrators with error details instantly to minimize downtime.  
3. **UpdateLogTable:** Persistently logs error event data for operational audits.  
4. **MoveFileToError:** Safely archives problematic input files preventing data loss and enabling error replay.

This structured approach ensures ETL robustness and timely operator intervention.

---

## ⚙️ Installation & Usage

1. Clone this repository into your SSIS development environment (Visual Studio with SSDT).  
2. Configure connection managers to point to your data source files and destination database servers.  
3. Adjust package parameters including file paths, email SMTP configuration, and business logic flags to suit your environment.  
4. Execute the SSIS project manually for testing or schedule with SQL Server Agent or similar scheduling tool for automated runs.

The package is modular, so you can extend or customize functionality via parameterization without re-engineering the core logic.

---

## 📂 Data Sources & Destinations

- **Source:** Flat file format (CSV or text files), typically exported from transactional systems or external vendors. Data arrived in raw format requiring cleansing and validation.  
- **Destination:** SQL-based data warehouse supporting analytical workloads. Data is loaded via OLE DB destination components with carefully mapped schema columns corresponding to transformed data.  
- **Reference Tables:** Lookups validate foreign key integrity, reducing bad data and maintaining referential consistency.

---

## 🎨 Customization

- Modify or extend Derived Column expressions and Conditional Split rules to implement custom validation and transformation logic according to evolving requirements.  
- Add or remove entities in Foreach Loop Containers to handle more or less data files dynamically at runtime.  
- Enhance error handling with additional notification channels or more granular logging schemas.

---

## 🔍 Troubleshooting

- Monitor email alerts for immediate indications of ETL failures or data quality issues.  
- Review log tables to understand load durations, record counts, and error occurrences.  
- Check error output folders for invalid data extracts that require cleansing or business rule revision.  
- Common issues include data truncation warnings, missing lookup matches, or connectivity errors—all annotated in detailed logs.

---

## 📝 Credits

Developed by a professional data engineer leveraging years of experience in building scalable, maintainable ETL pipelines. Inspired by industry best practices, Microsoft SSIS documentation, and open-source project standards.

---

