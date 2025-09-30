## 🔄 ETL Execution Flow: Dimension vs. Fact Loading

This SSIS pipeline is divided into two distinct flows to ensure modularity, maintainability, and referential integrity:

- 🧱 **Dimension Flow**: Handles enrichment and historical tracking of business entities.
- 📊 **Fact Flow**: Loads transactional data with lookups to dimension tables.

---

## 🧱 Dimension Loading Flow

This flow processes five dimension files: `Products`, `Employees`, `Stores`, `Discounts`, and `Customers`.

### 🔁 Step-by-Step Execution

#### 🗂️ 1. File Detection & Readiness Check
- **Script Task** checks if the file exists and monitors its size.
- ✅ If stable, sets `IsFileReady = true`.

#### 📝 2. Audit Log Start
- **Execute SQL Task** inserts a log entry:
  - `PackageName`: e.g., `Dim_Products`
  - `Status`: `Processing`
  - `StartTime`: Current timestamp

#### 📥 3. Data Flow Task Begins

##### 📂 Flat File Source
- Reads raw data from CSV/Excel.
- Supports dynamic file paths via parameters.

##### 🔄 Data Conversion
- Converts column types for SQL Server compatibility.

##### ➕ Derived Column
- Adds calculated fields (e.g., `FullName`, `FormattedDate`).

##### 🧬 Slowly Changing Dimension (SCD)
- Detects changes in attributes.
- Inserts new records or updates existing ones.

##### 🔀 Conditional Split
- Separates valid and invalid records based on business rules.

##### ❌ Error Handling
- Invalid records redirected to error table.
- Captures error description in a variable.
- Sends email alert with:
  - File name
  - Error message
  - Timestamp
- Moves file to error folder.

##### 📊 Row Count
- Tracks number of rows processed.

##### 🏁 OLE DB Destination
- Inserts valid records into dimension table.

#### 📦 4. Archive File
- **File System Task** moves processed file to archive folder.

#### 🕰️ 5. Audit Log Completion
- **Execute SQL Task** updates log entry:
  - `Status`: `Success`
  - `EndTime`: Timestamp
  - `RowsRead`, `RowsInserted`, `RowsErrored`

#### 📬 6. Success Notification
- Sends email with summary:
  - Package name
  - File name
  - Row counts
  - Duration

📸 _Refer to screenshots in `Screenshots/DimensionPackage_DataFlow.png`_

---

## 📊 Fact Loading Flow

This flow processes the `Transactions` file and ensures referential integrity by joining with dimension tables.

### 🔁 Step-by-Step Execution

#### 🗂️ 1. File Detection & Readiness Check
- Same logic as dimension flow.
- Sets `IsFileReady = true` if file is stable.

#### 📝 2. Audit Log Start
- Logs package start with `Fact_Transactions` as `PackageName`.

#### 📥 3. Data Flow Task Begins

##### 📂 Flat File Source
- Reads transactional data.

##### 🔄 Data Conversion
- Converts types (e.g., `Amount`, `Date`, `StoreID`).

##### 🔍 Lookup Transformations
- Joins with dimension tables:
  - `Products`
  - `Employees`
  - `Stores`
  - `Customers`
- Retrieves surrogate keys for referential integrity.

##### ➕ Derived Column
- Adds calculated metrics (e.g., `NetAmount = Amount - Discount`).

##### 🔀 Conditional Split
- Filters out records with missing lookups or invalid data.

##### ❌ Error Handling
- Redirects failed records to error table.
- Sends email with error details.
- Moves file to error folder.

##### 📊 Row Count
- Tracks rows read and inserted.

##### 🏁 OLE DB Destination
- Inserts valid records into fact table.

#### 📦 4. Archive File
- Moves successfully processed file to archive.

#### 🕰️ 5. Audit Log Completion
- Updates log with:
  - `Status`: `Success`
  - `RowsRead`, `RowsInserted`, `RowsErrored`
  - `StartTime`, `EndTime`

#### 📬 6. Success Notification
- Sends summary email with metrics and timestamps.

📸 _Refer to screenshots in `Screenshots/FactPackage_DataFlow.png`_

---

## 🧠 Why This Design Works

- 🔧 **Modular**: Each package is independently executable and reusable.
- 📈 **Scalable**: Supports multiple files via loop containers.
- 🛡️ **Resilient**: Errors are quarantined without halting the pipeline.
- 🧾 **Auditable**: Every step is logged with timestamps and metrics.
- 📬 **Communicative**: Email alerts ensure visibility for success and failure.

